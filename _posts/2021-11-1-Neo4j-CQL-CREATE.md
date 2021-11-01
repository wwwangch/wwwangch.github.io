---
layout: post
title: Neo4j-CQL-CREATE命令
categories: Neo4j
description: Neo4j 查询语言之CREATE命令
keywords: Neo4j, CQL, Cypher, CREATE
---

# Neo4j CQL - CREATE命令

Neo4j使用CQL“CREATE”命令

- 创建没有属性的节点
- 使用属性创建节点
- 在没有属性的节点之间创建关系
- 使用属性创建节点之间的关系
- 为节点或关系创建单个或多个标签

## 创建无属性节点

Neo4j CQL“CREATE”命令用于创建没有属性的节点。 它只是创建一个没有任何数据的节点。

### CREATE命令语法

```CQL
CREATE (<node-name>:<label-name>)
```

语法说明

| 语法元素     | 描述                       |
| ------------ | -------------------------- |
| CREATE       | 它是一个Neo4j CQL命令。    |
| <node-name>  | 它是我们要创建的节点名称。 |
| <label-name> | 它是一个节点标签名称       |

注意事项 -

1、Neo4j数据库服务器使用此<node-name>将此节点详细信息存储在Database.As中作为Neo4j DBA或Developer，我们不能使用它来访问节点详细信息。

2、Neo4j数据库服务器创建一个<label-name>作为内部节点名称的别名。作为Neo4j DBA或Developer，我们应该使用此标签名称来访问节点详细信息。



## 创建有属性的节点

Neo4j CQL“CREATE”命令用于创建带有属性的节点。 它创建一个具有一些属性（键值对）的节点来存储数据。



### CREATE命令语法： 

```CQL
CREATE (
   <node-name>:<label-name>
   { 	
      <Property1-name>:<Property1-Value>
      ........
      <Propertyn-name>:<Propertyn-Value>
   }
)
```



语法说明：

| 语法元素                              | 描述                                            |
| ------------------------------------- | ----------------------------------------------- |
| <node-name>                           | 它是我们将要创建的节点名称。                    |
| <label-name>                          | 它是一个节点标签名称                            |
| <Property1-name>...<Propertyn-name>   | 属性是键值对。 定义将分配给创建节点的属性的名称 |
| <Property1-value>...<Propertyn-value> | 属性是键值对。 定义将分配给创建节点的属性的值   |

