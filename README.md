[toc]

## dimension: todo

1. 架构:
2. 生态
3. HA
4. 部署|备份
5. crash safe
6. 性能
7. 磁盘
8. cpu
9. 数据量
10. 数据类型: json/text 等大字段支持
11. 表|试图
12. 索引
13. 事务
14. 锁
15. SP
16. 统计分析

## sql vs nosql vs newsql

1. NoSQL 是一个综合的数据库类别, 旨在克服 SQL 数据库产生的问题

   - mysql 的 json(非结构化的数据) 处理能力很弱: nosql 对其在底层就做了更多优化
   - 数据量问题
   - 并发问题
   - 超大字段
   - 使用场景的精细化优化: 如 redis, es

2. _NewSQL 是想结合两者的优势(事务, 规模, 扩展，,数据类型等)_

   |   dimension    | sql                      | nosql                          |    **newsql(HTAP)**    |
   | :------------: | :----------------------- | :----------------------------- | :--------------------: |
   |      Type      | rdbms                    | distibute or no-relation       |        combined        |
   |     struct     | 结构化 & 预定义 & 改代价 | 动态结构 & 无预定义 & 改代价小 |         结构化         |
   |  scalability   | 垂直扩展                 | 垂直 & 水平扩展                |    垂直 & 水平扩展     |
   | store(amount)  | 不适合分层存储           | 适合分层存储(大数据量)         | 适合分层存储(大数据量) |
   |      base      | 基于表(多行事务 acid)    | 键值对 & 面向文档,列,图形      | 基于表(多行事务 acid)  |
   |       HA       | 一般                     | 高                             |           高           |
   |      场景      | 事务, 金钱等             | 大数据, 社交,物联网            |
   |     theory     | acid                     | cap(base)                      |          acid          |
   |      生态      | 好                       | 好                             |          一般          |
   |    example     | mysql, pgsql, oracle     | mongodb, redis, neo4j, hive    |      VoltDB,NuoDB      |
   | 复杂查询(聚合) | 一般                     | 差                             |           好           |
   |      事务      | 友好                     | 一般                           |      支持联机事务      |
   |      索引      | 友好                     | 友好                           |          友好          |
   |     sample     | oracle,mysql,pgsql       | mongo,redis,es                 |          tidb          |

## ~~[dbms vs rdbms](https://byjus.com/gate/difference-between-dbms-and-rdbms/)~~

|          RDBMS           | DBMS                  |
| :----------------------: | :-------------------- |
|        表格式存储        | 文件格式存储          |
| 多个数据元素可以一起访问 | 数据元素的单独访问    |
| 表格形式的数据链接在一起 | 数据之间没有联系      |
|        支持 ACID         | 不支持 ACID           |
|        规范化存在        | 规范化不存在          |
|     支持分布式数据库     | 不支持分布式数据库    |
|       数据存储量大       | 存储的数据量很小      |
|  键和索引不允许数据冗余  | 数据冗余很常见        |
|        支持多用户        | 支持单用户            |
|      具有多层安全性      | 处理数据时安全性较低  |
|     对软硬件要求较高     | 软硬件要求低          |
|      Oracle, mysql       | XML, Microsoft Access |

## mysql vs pgsql

1. todo

---

## reference

1. [nosql vs newsql](https://www.geeksforgeeks.org/difference-between-nosql-and-newsql/)
2. [nosql vs sql](https://www.geeksforgeeks.org/difference-between-sql-and-nosql/)
3. [dbms & rdbms](https://www.geeksforgeeks.org/difference-between-rdbms-and-dbms/)

---

## language

### mysql

### redis

### mongodb

### es

## framework

### mybatis

### mapper

### mybatisplus

### jpa
