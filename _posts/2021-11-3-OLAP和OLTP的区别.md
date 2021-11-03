---
layout: post
title: OLAP和OLTP的区别
categories: 补充
description:  OLAP和OLTP的区别
keywords: OLAP, OLTP

---

# OLAP和OLTP的区别



## OLAP

OLAP（On-Line Analytical Processing）联机分析处理，也称为面向交易的处理过程，其基本特征是前台接收的用户数据可以立即传送到计算中心进行处理，并在很短的时间内给出处理结果，是对用户操作快速响应的方式之一。应用在数据仓库，使用对象是决策者。OLAP系统强调的是数据分析，响应速度要求没那么高。

## OLTP

OLTP（On-Line Transaction Processing）联机事务处理，它使分析人员能够迅速、一致、交互地从各个方面观察信息，以达到深入理解数据的目的。它具有FASMI(Fast Analysis of Shared Multidimensional Information)，即共享多维信息的快速分析的特征。主要应用是传统关系型数据库。OLTP系统强调的是内存效率，实时性比较高。



## 比较

![img](https://i.loli.net/2021/11/02/Uf9i6tKq1jExmWa.png)

![img](https://i.loli.net/2021/11/02/MptruzE1Ofs4JbN.png)



## OLAP场景的关键特征[ ](https://clickhouse.com/docs/zh/#olapchang-jing-de-guan-jian-te-zheng)

- 绝大多数是读请求
- 数据以相当大的批次(> 1000行)更新，而不是单行更新;或者根本没有更新。
- 已添加到数据库的数据不能修改。
- 对于读取，从数据库中提取相当多的行，但只提取列的一小部分。
- 宽表，即每个表包含着大量的列
- 查询相对较少(通常每台服务器每秒查询数百次或更少)
- 对于简单查询，允许延迟大约50毫秒
- 列中的数据相对较小：数字和短字符串(例如，每个URL 60个字节)
- 处理单个查询时需要高吞吐量(每台服务器每秒可达数十亿行)
- 事务不是必须的
- 对数据一致性要求低
- 每个查询有一个大表。除了他以外，其他的都很小。
- 查询结果明显小于源数据。换句话说，数据经过过滤或聚合，因此结果适合于单个服务器的RAM中

很容易可以看出，OLAP场景与其他通常业务场景(例如,OLTP或K/V)有很大的不同， 因此想要使用OLTP或Key-Value数据库去高效的处理分析查询场景，并不是非常完美的适用方案。例如，使用OLAP数据库去处理分析请求通常要优于使用MongoDB或Redis去处理分析请求。