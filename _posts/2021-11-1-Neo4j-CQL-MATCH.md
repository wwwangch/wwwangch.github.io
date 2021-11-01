---
layout: post
title: Neo4j-CQL-MATCH&RETURN命令
categories: Neo4j
description: Neo4j 查询语言之MATCHHE和RETURN命令
keywords: Neo4j, CQL, Cypher, MATCH, RETURN
---

# MATCH

## Neo4j CQL - MATCH命令

Neo4j CQL MATCH 命令用于

- 从数据库获取有关节点和属性的数据
- 从数据库获取有关节点，关系和属性的数据

### MATCH 命令语法：

```CQL
MATCH 
(
   <node-name>:<label-name>
)
```

语法说明

| 语法元素     | 描述                         |
| ------------ | ---------------------------- |
| <node-name>  | 这是我们要创建一个节点名称。 |
| <label-name> | 这是一个节点的标签名称       |

注意事项 -

- Neo4j 数据库服务器使用此 <node-name> 将此节点详细信息存储在 Database.As 中作为 Neo4j DBA 或 Developer，我们不能使用它来访问节点详细信息。
- Neo4j 数据库服务器创建一个 <label-name> 作为内部节点名称的别名。作为 Neo4j DBA 或 Developer，我们应该使用此标签名称来访问节点详细信息。

**注意-**我们不能单独使用 MATCH Command 从数据库检索数据。 如果我们单独使用它，那么我们将 InvalidSyntax 错误。



# RETURN

Neo4j CQL RETURN子句用于 -

- 检索节点的某些属性
- 检索节点的所有属性
- 检索节点和关联关系的某些属性
- 检索节点和关联关系的所有属性

## RETURN命令语法：

```CQL
RETURN 
   <node-name>.<property1-name>,
   ........
   <node-name>.<propertyn-name>
```



语法说明:

| 语法元素                            | 描述                                                         |
| ----------------------------------- | ------------------------------------------------------------ |
| <node-name>                         | 它是我们将要创建的节点名称。                                 |
| <Property1-name>...<Propertyn-name> | 属性是键值对。 <Property-name>定义要分配给创建节点的属性的名称 |



# 注意：

在Neo4j CQL中，我们不能单独使用MATCH或RETURN命令，因此我们应该合并这两个命令以从数据库检索数据。

Neo4j使用CQL MATCH + RETURN命令 - 



- 检索节点的某些属性
- 检索节点的所有属性
- 检索节点和关联关系的某些属性
- 检索节点和关联关系的所有属性

## MATCH RETURN命令语法：

```
MATCH Command
RETURN Command
```



语法说明：

| 语法元素   | 描述                       |
| ---------- | -------------------------- |
| MATCH命令  | 这是Neo4j CQL MATCH命令。  |
| RETURN命令 | 这是Neo4j CQL RETURN命令。 |

##  

## MATCH命令语法：

```
MATCH 
(
   <node-name>:<label-name>
)
```



语法说明：

| 语法元素     | 描述                         |
| ------------ | ---------------------------- |
| <node-name>  | 它是我们将要创建的节点名称。 |
| <label-name> | 它是一个节点标签名称         |



要点 -

- Neo4j数据库服务器使用此<node-name>将此节点详细信息存储在Database.As中作为Neo4j DBA或Developer，我们不能使用它来访问节点详细信息。
- Neo4j数据库服务器创建一个<label-name>作为内部节点名称的别名。作为Neo4j DBA或Developer，我们应该使用此标签名称来访问节点详细信息。

## 

## RETURN命令语法：

```
RETURN 
   <node-name>.<property1-name>,
   ...
   <node-name>.<propertyn-name>
```



语法说明：

| 语法元素                            | 描述                                            |
| ----------------------------------- | ----------------------------------------------- |
| <node-name>                         | 它是我们将要创建的节点名称。                    |
| <Property1-name>...<Propertyn-name> | 属性是键值对。 定义将分配给创建节点的属性的名称 |

## 例

```CQL
MATCH (dept: Dept)
RETURN dept.deptno,dept.dname
```

这里 -

- dept是节点名称
- 这里Dept是一个节点标签名
- deptno是dept节点的属性名称
- dname是dept节点的属性名
