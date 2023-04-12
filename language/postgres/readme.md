## postgres sql

1. [feature: 满足大部分数据组件的功能](https://www.zhihu.com/question/20010554/answer/94999834): 一专多能

   - OLTP: 事务处理是 PostgreSQL 的本行
   - OLAP: citus 分布式插件, ANSI SQL 兼容, 窗口函数, CTE, CUBE 等高级分析功能, 任意语言写 UDF
   - 流处理: PipelineDB 扩展, Notify-Listen, 物化视图, 规则系统, 灵活的存储过程与函数编写
   - 时序数据: timescaledb 时序数据库插件, 分区表, BRIN 索引
   - 空间数据: PostGIS 扩展(杀手锏), 内建的几何类型支持, GiST 索引
   - 搜索索引: 全文搜索索引足以应对简单场景, 丰富的索引类型, 支持函数索引, 条件索引
   - NoSQL: JSON, JSONB, XML, HStore 原生支持, 至 NoSQL 数据库的外部数据包装器
   - 数据仓库: 能平滑迁移至同属 Pg 生态的 GreenPlum, DeepGreen, HAWK 等, 使用 FDW 进行 ETL
   - 图数据: 递归查询
   - 缓存: 物化视图

   ![avatar](/static/image/pgsql/pg-feature.png)

2. 性能

   - 探探: `几百万日活，几百万全局DB-TPS，几百TB数据的量级下`

3. **集成多种异构技术是相当棘手的工作**

   - 如果真有那么一样技术可以满足你所有的需求
   - 那么使用该技术就是最佳选择, 而不应试图去集成多个组件来重新实现它

4. 优点

   - 性能和可扩展性: 包括地理空间支持和无限制的并发性, 以及跨多种数据类型的深入、广泛的数据分析
   - 通过使用多版本并发控制(MVCC)支持并发: 这使得写操作和读操作同时发生
   - 业务连续性: 通过**跨服务器的异步或同步复制方法**提供高可用性服务
   - 通过开源数据库管理技术实现更大的灵活性和成本效益的创新
   - 由于它的兼容性和对多种编程语言的支持: 包括 Python、Java、JavaScript、C/C++ 和 Ruby，因此支持深度语言。

## reference

1. [pgsql vs mysql](https://www.zhihu.com/question/20010554/answer/94999834)
2. [pgsql](https://www.zhihu.com/question/20092344/answer/2364049461)
3. [pgsql](https://www.zhihu.com/question/20092344/answer/2541269117)
