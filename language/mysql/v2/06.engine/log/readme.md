[toc]

## [undo-log](https://zhuanlan.zhihu.com/p/453169285)

1. 逻辑日志: **记录的是每一条数据的修改逻辑**, **不是针对数据页**[**针对的是行记录**] 物理日志是修改加载进内存的最小的逻辑单元`页`, 如果记录的是页的修改则是物理日志
2. 存储
   - 在 5.6 之前所有的 undo log 全部存储在系统表空间中(ibdata1)
   - 但是从 5.6 开始也可以使用独立表空间来存储 undo log
   - 8.0 InnoDB 默认有两个 undo tablespace: 最大 128, 每个 tablespace 最多有 128 个回滚段(trx_sys_n_rsegs)
3. 实现事务的原子性
4. [用于实现 mvcc](./05.transaction.mvcc.md)
5. 在任何操作之前, _首先将数据备份到一个地方[undo-log], 然后对事物进行修改,_ 如果出错或 rollback 则利用 undo-log 中的备份将数据恢复到事务开始之前的状态: `可以近似按以下方法理解`

   - delete 时则向 undo-log 中插入 insert 一条数据
   - insert 则在 undo-log 中记录一条 delete 语句
   - update 则在 undo-log 中记录一条反向的 update 语句

6. 在 MySQL 中, 实际上每条记录在更新的时候都会同时记录一条回滚操作
7. 记录上的最新值, 通过回滚操作, 都可以得到前一个状态的值
8. 假设一个值从 1 被按顺序改成了 2、3、4, 在回滚

   ![avatar](/static/image/mysql/mysql-isolation-rr.png)

9. 当前值是 4, 但是在查询这条记录的时候, 不同时刻启动的事务会有不同的 read-view

   - 如图中看到的, 在视图 A, B, C 里面, 这一个记录的值分别是 1, 2, 4,
   - 同一条记录在系统中可以存在多个版本, 就是数据库的多版本并发控制(MVCC)
   - 对于 read-view A , 要得到 1, 就必须将当前值依次执行图中所有的回滚操作得到

10. 即使现在有另外一个事务正在将 4 改成 5, 这个事务跟 read-view A, B, C 对应的事务是不会冲突的

11. **当没有事务再需要用到这些回滚日志时, 回滚日志会被删除**: 长事务的会带来很长的视图版本
    - `当系统里没有比这个回滚日志更早的 read-view 的时候`

## redo log

1. 定义: `innodb` 中是一快可`重复利用`的`顺序读写`的`固定大小`的`磁盘空间`, 是一种 `Write-Ahead Logging[先写日志]{将来会出现的数据}`, `物理日志`[数据页上的修改]

   - 是 innodb 中的概念, 所有线程共用{**可以是多个 redolog 文件**}
   - 顺序写: 速度极快
   - WAL: 先写日志
   - commit 前不需要将数据整合到真正的数据表{随机写}
   - 固定大小: 环形数组, redo-log 只能循环写, 不能满了就创建新的{满了就主要停下来, 等待将 redolog 内容写到表磁盘[随机 IO]}
   - 数据页上的修改
   - **redo log 是数据页, 因此一旦 commit 就不能回滚, 否则会导致其他事务的写丢失**
   - 使 MySQL 具有了崩溃恢复能力

2. 作用:

   - redo-log + bin-log **二阶段提交**保证数据安全 + crash safe
   - 快速提高吞吐量等
   - redo log 只记录 innoDB 自身的事务日志

3. redo log buffer: 全局共用{MySQL 的内存中}

   - 是一块内存, 保存修改数据但是还没有 commit 的 redo log: 在一个事务的更新过程中, 日志是要写多次的
   - 事务在执行过程中, 生成的 redo log 是要先写到 redo log buffer
   - **不需要**每次改动都持久化到磁盘, 但是可能会被持久化到磁盘: 因为事务没提交{宕机的话也没影响}
   - innodb_log_buffer_size 控制大小
   - buffer 使用空间达到 1/2 的 innodb_log_buffer_size 时会写入 page cache

     ```sql
     begin;
     insert into t1 ... -- 会先插入 redo log buffer
     insert into t2 ... -- 会先插入 redo log buffer
     commit; -- 写入 redo-log
     ```

4. WAL

   ![avatar](/static/image/mysql/mysql-wal.png)

   - write pos 是当前记录的位置, 一边写一边后移, 写到第 3 号文件末尾后就回到 0 号文件开头
   - checkpoint 是当前要擦除的位置, 擦除记录前要把记录更新到数据文件
   - write pos 和 checkpoint 之间的是空着的部分, 可以用来记录新的操作.
   - `如果 write pos 追上 checkpoint, **这时候不能再执行新的更新**`, 得停下来等待 checkpoint 推进

5. flow

   - 存在 redo log buffer 中: 物理上是在 MySQL 进程内存中
   - write 但是没有 fsync: 数据在 FS page cache 中
   - fsync 持久化到磁盘: 数据在 hard disk

   ![avatar](/static/image/mysql/mysql-redolog-flow.png)

6. 相关配置: `innodb_flush_log_at_trx_commit`

   - 在事务提交之前不需要将数据持久化到数据表的磁盘, 只需要记录到 Redolog 中就行
   - 当系统崩溃时, 虽然数据没有持久化到数据表的磁盘, 但是系统可以根据 redo-log 里的内容将数据恢复到最新的状态
   - innodb_flush_log_at_trx_commit:
     1. 0: 表示写入 innodb 的 logbuffer
     2. **默认值是 1**: 表示每次都将数据直接写到 os buffer 并调用 fsync 刷入磁盘{prepare 阶段就需要持久化}
     3. 2: 表示直接写入 OS buffer{page cache}

7. 将 innodb_flush_log_at_trx_commit 设置为 2

   - **风险: 主机宕机时会丢数据**

8. InnoDB 有一个后台线程: 每隔 1 秒

   - redo log buffer 中的日志 write 写到文件系统的 page cache
   - 调用 fsync 持久化到磁盘
   - **一个没有提交的事务的 redo log, 也是可能已经持久化到磁盘的**

9. 没提交事务的 redo log 被写入磁盘的场景: _回滚时使用 undolog 恢复磁盘数据_

   - InnoDB 每秒一次的后台线程: buffer -> fs page cache -> disk
   - buffer 使用空间达到 1/2 的 innodb_log_buffer_size 时会写入 page cache
   - 并行的事务提交的时候, 顺带将这个事务的 redo log buffer 持久化到磁盘
