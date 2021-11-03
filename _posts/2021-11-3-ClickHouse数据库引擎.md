---
layout: post
title: ClickHouse数据库引擎
categories: ClickHouse
description: ClickHouse数据库引擎
keywords: ClickHouse, 引擎

---

# ClickHouse数据库引擎

数据库引擎用来处理数据表

默认情况下，ClickHouse使用Atomic数据库引擎。它提供了可配置的table engines和SQL dialect。

您还可以使用以下数据库引擎：

- [MySQL](https://clickhouse.com/docs/zh/engines/database-engines/mysql/)
- [MaterializeMySQL](https://clickhouse.com/docs/zh/engines/database-engines/materialize-mysql/)
- [Lazy](https://clickhouse.com/docs/zh/engines/database-engines/lazy/)
- [Atomic](https://clickhouse.com/docs/zh/engines/database-engines/atomic/)
- [PostgreSQL](https://clickhouse.com/docs/zh/engines/database-engines/postgresql/)
- [MaterializedPostgreSQL](https://clickhouse.com/docs/zh/engines/database-engines/materialized-postgresql/)
- [Replicated](https://clickhouse.com/docs/zh/engines/database-engines/replicated/)



## Atomic

它支持非阻塞的DROP TABLE和RENAME TABLE查询和原子的EXCHANGE TABLES t1 AND t2查询。默认情况下使用Atomic数据库引擎。

### 创建数据库 

```sql
  CREATE DATABASE test[ ENGINE = Atomic];
```

### Table UUID 

数据库Atomic中的所有表都有唯一的UUID，并将数据存储在目录/clickhouse_path/store/xxx/xxxyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy/，其中xxxyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy是该表的UUID。

通常，UUID是自动生成的，但用户也可以在创建表时以相同的方式显式指定UUID(不建议这样做)。可以使用 show_table_uuid_in_table_create_query_if_not_nil设置。显示UUID的使用SHOW CREATE查询。例如:

```sql
CREATE TABLE name UUID '28f1c61c-2970-457a-bffe-454156ddcfef' (n UInt64) ENGINE = ...;
```

### RENAME TABLES 

RENAME查询是在不更改UUID和移动表数据的情况下执行的。这些查询不会等待使用表的查询完成，而是会立即执行。

### DROP/DETACH TABLES 

在DROP TABLE上，不删除任何数据，数据库Atomic只是通过将元数据移动到/clickhouse_path/metadata_dropped/将表标记为已删除，并通知后台线程。最终表数据删除前的延迟由database_atomic_delay_before_drop_table_sec设置指定。

可以使用SYNC修饰符指定同步模式。使用database_atomic_wait_for_drop_and_detach_synchronously设置执行此操作。在本例中，DROP等待运行 SELECT, INSERT和其他使用表完成的查询。表在不使用时将被实际删除。

### EXCHANGE TABLES 

EXCHANGE以原子方式交换表。因此，不是这种非原子操作：

```
RENAME TABLE new_table TO tmp, old_table TO new_table, tmp TO old_table;
```


因为中间用到了临时表名，容易出现意外，此时可以使用一个原子查询：

```
EXCHANGE TABLES new_table AND old_table;
```



## LAZY

在最后一次访问之后，只在RAM中保存expiration_time_in_seconds秒。只能用于*Log表。

它是为存储许多小的*Log表而优化的，对于这些表，访问之间有很长的时间间隔。

### 创建数据库 

```sql
CREATE DATABASE testlazy ENGINE = Lazy(expiration_time_in_seconds);
```



## MySQL

MySQL引擎用于将远程的MySQL服务器中的表映射到ClickHouse中，并允许您对表进行INSERT和SELECT查询，以方便您在ClickHouse与MySQL之间进行数据交换

MySQL数据库引擎会将对其的查询转换为MySQL语法并发送到MySQL服务器中，因此您可以执行诸如SHOW TABLES或SHOW CREATE TABLE之类的操作。

但您无法对其执行以下操作：

- RENAME
- CREATE TABLE
- ALTER

### 创建数据库 

```sql
CREATE DATABASE [IF NOT EXISTS] db_name [ON CLUSTER cluster]
ENGINE = MySQL('host:port', ['database' | database], 'user', 'password')
```

### 引擎参数

- host:port — MySQL服务地址
- database — MySQL数据库名称
- user — MySQL用户名
- password — MySQL用户密码

### 支持的数据类型

| MySQL                            | ClickHouse  |
| -------------------------------- | ----------- |
| UNSIGNED TINYINT                 | UInt8       |
| TINYINT                          | Int8        |
| UNSIGNED SMALLINT                | UInt16]     |
| SMALLINT                         | Int16       |
| UNSIGNED INT, UNSIGNED MEDIUMINT | UInt32      |
| INT, MEDIUMINT                   | Int32       |
| UNSIGNED BIGINT                  | UInt64      |
| BIGINT                           | Int64       |
| FLOAT                            | Float32     |
| DOUBLE                           | Float64     |
| DATE                             | Date        |
| DATETIME, TIMESTAMP              | DateTime    |
| BINARY                           | FixedString |

其他的MySQL数据类型将全部都转换为String



# 表引擎

表引擎（即表的类型）决定了：

- 数据的存储方式和位置，写到哪里以及从哪里读取数据
- 支持哪些查询以及如何支持。
- 并发数据访问。
- 索引的使用（如果存在）。
- 是否可以执行多线程请求。
- 数据复制参数。

## 一、MergeTree系列引擎

MergeTree系列的表引擎支持**主键索引、数据分区、数据副本和数据采样**这些特性，同时也只有此系列的表引擎支持ALTER相关操作

特点：

- 存储按主键排序的数据
- 如果指定了分区键，则可以使用分区
- 数据复制支持：ReplacedMergeTree表族提供数据复制。
- 数据采样支持

## 二、Log系列引擎

Log家族具有最小功能的**轻量级引擎**。当您需要**快速写入许多小表（最多约100w行）并在以后整体读取它们时**，该类型的引擎是最有效的。

### 共同属性

引擎：

- 数据存储在磁盘上。

- 写入时将数据追加在文件末尾。

- 不支持[突变](https://clickhouse.com/docs/zh/engines/table-engines/log-family/#alter-mutations)操作。

- 不支持索引。

  ```
  这意味着 `SELECT` 在范围查询时效率不高。
  ```

- 非原子地写入数据。

  ```
  如果某些事情破坏了写操作，例如服务器的异常关闭，你将会得到一张包含了损坏数据的表。
  ```

### 1、TinyLog引擎

- 将数据存储在磁盘上，没有索引，没有标记块
- 每列都存储在单独的压缩文件中
- 数据写入时追加写到文件末尾
- 该引擎没有并发控制，不允许同时读写

### 2、StripeLog引擎(数据分块列在一起)

### 3、Log引擎(数据分块记录偏移量)

- *.bin存储每个字段的数据
- mark.mrk数据块标记
- 支持多线程处理
- 并发读写

## 三、外部存储引擎

### 1、HDFS引擎

clickhouse可以直接从HDFS中指定的目录下加载数据，自己不存储数据，仅仅读取数据

```sql
engine=HDFS('hdfs://linux01:8020/ck/test/*','csv')
-clickhouse支持的文件格式有CSV、TSV、JSON等
```

### 2、mysql引擎

```text
engine=MYSQL('localhost:3306','test','test','root','123456')
```

### 3、File引擎

## 四、内部引擎

### 1、Memory

### 2、Set

### 3、Buffer