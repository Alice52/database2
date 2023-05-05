[toc]

## binlog(归档日志)

1. binlog: service 层的归档日志(表结构修改及数据修改), 可选择性打开的逻辑日志[记录语句逻辑]

   - 数据恢复 + 主从复制
   - 无限空间, 追加记录
   - 顺序写
   - _组提交机制_: 可以大幅度降低磁盘的 IOPS 消耗
   - 核心作用: 数据备份、主备、主主、主从

2. 指定模式

   - statement: 只记录引起数据变更的 SQL 语句 + 没有数据日志文件小 IO 消耗低, 性能好 + 但是可能引起数据不一致(函数)
   - **row**: 记录每次操作的**源数据**与修改后的**目标数据** + 数据一致, 安全可靠,**并发**执行 + binlog 文件大, 性能(io/cpu)消耗, 阅读性差

     ![avatar](/static/image/mysql/log-server-row.png)

   - mixed: STATEMENT 跟 ROW 两种模式的混合使用, 只有涉及到函数等可能引起数据不一致的地方才会使用 row

3. binlog cache{一片内存}

   - 每个线程一个: 不在 `buffer pool(inndodb 的)`
   - binlog_cache_size 设置单个线程被分配的内存
   - 如果超过了这个参数规定的大小, 就要暂存到磁盘
   - 每个线程有自己 binlog cache, 但是共用同一份 binlog 文件

4. 保存 binlog 的流程

   - 事务执行过程中, 先把日志写到 binlog cache
   - 事务提交的时候, 再把 binlog cache 写到 binlog 文件中, 并清空 binlog cache
   - **一个事务的 binlog 是不能被拆开的, 因此不论这个事务多大也要确保一次性写入{binlog cache}**

   ![avatar](/static/image/mysql/mysql-binlog.png)

5. sync_binlog: 都会 write() 写入 fs page cache

   - 0: 不调用 fsync, 交给系统做主 + 后台任务
   - 1: 每个事务都调用 fsync
   - N: n 个事务之后才会调用 fsync

6. 将 sync_binlog 设置为大于 1 的值[比较常见是 100~1000]

   - 风险: 主机宕机时会丢 binlog 日志且不能回滚事务{主备不一致}

7. binlog: statement 与 row 的区别

   - row 是数据级别的, statement 是语句
   - 集群数据一致问题
   - 可重复读下的问题
     ![avatar](/static/image/mysql/mysql-binlog-format.png)
   - 我们一般不管, format 默认都是 row, 像这种只 match 到, 没有 update 到数据的, 最后结果都是快照读
   - lock in share mode: 当前读, 读锁[select * from xx 是不会产生读锁]
   - for update: 当前读
   - select \* from xx: 快照读
