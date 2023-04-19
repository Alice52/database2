## version 2

### overview

1. 单机版数据量

   - 单数据库存储不超过 1 亿的数据比较合适

2. 单机版 qps/connection: `8c32g`

   - connection: 1w+
   - qps: 8k

3. 单机版 rtt
4. 性能: 平稳到达瓶颈

   - `cpu:mem`: 1:4 最为适宜
   - **并发数增加到一定数量后, cpu 使用率和操作 qps 数不再随着并发数的增加而提高**

5. cap

   | scenario                          | main focus | description                                                              |
   | :-------------------------------- | :--------- | :----------------------------------------------------------------------- |
   | no partition                      | ca         | the system is available and provides strong consistency                  |
   | partition, majority connected     | ap         | not synchronized writes from the old primary are ignored                 |
   | partition, majority not connected | cp         | only read access is provided to avoid separated and inconsistent systems |

### intros

1. mongodb 是一个**文档数据库**的非关系型数据库(nosql)

   - 由 c++语言编写
   - 以 json 为数据模型(bson): 可以存复杂的数据类型
   - nosql: 介于 sql 与 nosql 之间, 最像 sql 的 nosql, 但是使用的时候还是有逻辑的 schema 的(包括 mq)
   - **单表**查询语言支持的非常强大, 后面开始支持事务: 对连表/聚合查询不友好(语法&性能)
   - 旨在为 web 应用提供**可扩展**的**高性能(index)**的**海量数据**下的**分布式**存储解决方案
   - **mongodb 数据库使用的是 JavaScript 进行操作的, 在 MongoDB 含有一个对 ES 标准实现的引擎, 在 MongoDB 中所有 ES 中的语法中都可以使用**
   - 在 mongodb 中, 数据库和集合都不需要手动创建, 当我们创建文档时, 如果文档所在的集合或数据库不存在会自动创建数据库和集合

2. concept

   | SQL 概念            | MongoDB 概念       |
   | :------------------ | :----------------- |
   | 数据库(database)    | 数据库 (database)  |
   | 表(table)           | 集合 (collection)  |
   | 行(row)             | 文档(document)     |
   | 列 (column)         | 字段 (field)       |
   | 索引(index)         | 索引(index)        |
   | 主键(primary key)   | id (字段)          |
   | 视图 (view)         | 视图(view)         |
   | 表连接(table joins) | 聚合操作 ($lookup) |
   | like                | $regex             |

3. pros

   - 高可用(ha) + 水平扩展: replica-set, sharding, 恢复
   - 海量数据: tb-pb 级数据
   - 非结构化的数据: 半结构化(支持业务变化)
   - 高性能(单表): bson
   - 弱关系: 表之间的关系简单明朗, 关联数据存一个集合(无需关联 join/读不同的区)

4. 应用场景: 无需强事务+数据结构变化+负载查询

   - 社交场景: 文本存储
   - 物流场景: 变更记录一次查询等
   - 游戏场景
   - 物联网场景

---

## mongodb knowledge

### [mongodb install](./mongodb-install.md)

1. introducation
2. window os
3. linux os

### [mongodb crud](./mongodb-crud.md)

1. insert
2. find
3. remove
4. update
5. 常见函数与投影: limit skip sort
6. [practise](./mongodb-crud-practise.md)

### [mongodb relation](./mongodb-doc-relation.md)

1. relations
2. demo

### [mongoose](./mongodb-mongoose.md)

1. mongoose introducation
   - 相关认知
   - mongoose 对象
     1. schema(definition, option)
     2. model
     3. document
2. mongoose 基本使用
   - 基础
   - demo
3. mongoose 模块化

---

## reference

1. [mongodb 性能-aliyun](https://help.aliyun.com/document_detail/144258.html)
2. [mongodb 性能-单机 qps](https://help.aliyun.com/document_detail/440059.html)
3. [mongodb 性能-自建](https://www.cnblogs.com/lovecindywang/archive/2011/03/02/1969324.html)
