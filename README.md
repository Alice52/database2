[toc]

## 数据组件

1. 缓存: redis
2. OLTP: mysql
3. OLAP: Hadoop
4. 批处理/数据仓库: flink
5. 流处理/消息队列: Kafka
6. 搜索索引: es
7. 文档数据库: mongo, Couchbase
8. 地理数据库: PostGIS
9. 空间数据库
10. 时序数据库: influxdb
11. 图数据库: Neo4j

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
11. 表|视图: 存储方式
12. 索引: 存储方式
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
   |    example     | mysql, pgsql, oracle     | mongodb, redis, neo4j, hive    |      tidb,VoltDB       |
   | 复杂查询(聚合) | 一般                     | 差                             |           好           |
   |      事务      | 友好                     | 差                             |      支持联机事务      |
   |      索引      | 友好                     | 友好                           |          友好          |

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

1. join(hash join):
2. oltp
3. 数据类型: GIS
4. **json**
5. string/text
6. sp
7. _rr_
8. cluster: ha(crash-safe)
9. 聚合函数(复杂查询)： olap || ETL
10. **性能**: 好上一个数量及

    ![avatar](/static/image/pgsql/pgsql-pt.png)
    ![avatar](/static/image/mysql/mysql-pt.png)

11. 单标数据量: pgsql(kw) | mysql(bw)
12. **分区**
13. 初衷: postgres 更加偏学术研究，各种各样的功能全，则 mysql 偏应用，功能不追求完善，追求实用、性能
14. 架构: 多线程 | 多进程
15. 生态: pgadmin | tidb
16. slogan: 最流行 | 最优秀
17. PG 的优化执行引擎要比 MySQL 更好
18. PG 的开源协议比 MySQL 更好: BSD || GPL
19. 特色: mysql(engine layer)
20. sql 标准实现: PG 好(学院派), 超严格
21. PG 主表采用堆表存放, MySQL 采用索引组织表
    - pgsql 不是完全遵循最左前缀法则
22. 异构技术: pg 可以将多种源作为数据源
23. 提交方式: 异步
24. 使用场景: 如果你的场景并发量比较大，直接 MySQL 在生态(TiDB)及人员招聘上，你会省掉很多事情 || 小 olap(一专多能)
25. 数据库类型: rdbms || ordbms
26. XML 支持: 只 pg 可以
27. 物化视图: 只 pg 可以
28. 批处理

## mysql vs mongodb

1. sql and nosql
   - acid
   - tranasction
   - 使用场景
2. arthecture and cluster
3. [index and explian](https://zhuanlan.zhihu.com/p/519658576)

## interview

1. ER 图

---

## reference

1. [nosql vs newsql](https://www.geeksforgeeks.org/difference-between-nosql-and-newsql/)
2. [nosql vs sql](https://www.geeksforgeeks.org/difference-between-sql-and-nosql/)
3. [dbms & rdbms](https://www.geeksforgeeks.org/difference-between-rdbms-and-dbms/)
4. [pgsql vs mysql](https://www.zhihu.com/question/20010554)
5. [pgsql vs mysql](https://www.zhihu.com/question/20010554/answer/94999834)
6. [_pgsql vs mysql_](https://www.zhihu.com/question/20010554/answer/743955463)
7. [pgsql vs mysql](https://zhuanlan.zhihu.com/p/519946960)

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
