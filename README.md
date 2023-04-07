## sql vs nosql vs newsql

1. NoSQL 是一个综合的数据库类别, 旨在克服 SQL 数据库产生的问题

   - mysql 的 json(非结构化的数据) 处理能力很弱: nosql 对其在底层就做了更多优化
   - 数据量问题
   - 并发问题
   - 超大字段
   - 使用场景的精细化优化: 如 redis

2. _NewSQL 是想结合两者的优势(事务, 规模, 扩展，,数据类型等)_

|   dimension    | sql                      | nosql                          |   _newsql_   |
| :------------: | :----------------------- | :----------------------------- | :----------: |
|      Type      | rdbms                    | distibute or no-relation       |    都可以    |
|     struct     | 结构化 & 预定义 & 改代价 | 动态结构 & 无预定义 & 改代价小 |    都可以    |
|  scalability   | 垂直扩展                 | 垂直 & 水平扩展                |    都可以    |
| store(amount)  | 不适合分层存储           | 适合分层存储(大数据量)         |    都可以    |
|      base      | 基于表(多行事务)         | 键值对 & 面向文档,列,图形      |    都可以    |
|       HA       | 一般                     | 高                             |      高      |
|      场景      | 事务, 金钱等             | 大数据, 社交,物联网            |
|     theory     | acid                     | cap(base)                      |     acid     |
|      生态      | 好                       | 好                             |
|    example     | mysql, pgsql, oracle     | mongodb, redis, neo4j, hive    | VoltDB,NuoDB |
| 复杂查询(聚合) | 一般                     | 差                             |
|      事务      | 友好                     | 一般                           | 支持联机事务 |
|      索引      | 友好                     | 友好                           |

---

## reference

1. [nosql vs newsql](https://www.geeksforgeeks.org/difference-between-nosql-and-newsql/)
2. [nosql vs newsql](https://www.geeksforgeeks.org/difference-between-sql-and-nosql/)

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
