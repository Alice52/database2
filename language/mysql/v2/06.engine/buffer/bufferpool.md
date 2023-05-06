## buffer pool

![avatar](/static/image/mysql/mysql-engine-layout.png)

![avatar](/static/image/mysql/mysql-conf-bp.png)

![avatar](/static/image/mysql/innoddb-bp.png)

1. 简介

   - 存放的是数据与索引: **索引并不是都在内存中**
   - 缓存页{16KB}{数据页和索引页}: bp 数据的基本单元 + `磁盘数据 按照数据页组织: 16KB`;
   - 描述数据{800byte}: 所属表空间, 数据页的编号, bp 中的地址, next free 节点, netx flush 节点, netx lru 节点
   - 相关的数据结构: 都是双向链表
     1. Free 链表{双向}: 存放空闲缓存页
     2. 缓存页哈希表: 快速定位缓存页{key(表空间号+数据页号)-value(缓存页地址)}
        ![avatar](/static/image/mysql/innodb-bp-free.png)
     3. Flush 链表: 存放脏页
     4. LRU 链表: 内存不够时淘汰方式 + 优化{数据冷热区分}
   - 预读机制: 调用存储引擎时将下次可能用到的数据和索引加载到 Buffer Pool

2. LRU 流程(mysql 版本): 数据先进 old, 待满 1s 且被再次访问时移入 young 头部

   - 默认按照 5:3 的比例把整个 LRU 链表分成了 young 区域`{热点数据}`和 old`{新载入数据}` 区域
   - 要访问数据页 P3, 由于 P3 在 young 区域, ~~将其移到链表头部~~`访问young区前1/4时不会移动, 后3/4的数据才会移到链表头部`, 变成状态 2
   - 之后要访问一个新的不存在于当前链表的数据页, 这时候依然是淘汰掉数据页 Pm, 但是新插入的数据页 Px, 是**放在 LRU_old 处**
   - 处于 old 区域的数据页, 每次被访问的时候都要做下面这个判断
     1. 若这个数据页在 LRU 链表中存在的时间超过了 1`innodb_old_blocks_time` 秒, 就把它移动到链表头部
     2. 如果这个数据页在 LRU 链表中存在的时间短于 1 秒, 位置保持不变

   ![avatar](/static/image/mysql/mysql-pb-o-y.png)

3. 作用(加速-快): 查询操作(bp) + 更新(bp+change-buffer+redolog)

   - 读和写操作都可以先操作 bp 内存, 如果没有则到磁盘里去加载
   - 也就是说[change buffer]: `把xx更新为xx, 或插入xx, 修改内存数据{则变为脏页} + 写 log 文件{redo/bin}后即可提交, 不需要获取数据页`{唯一索引除外}

4. 内存命中率(内部是热点数据): 99%+ 才是健康的
   ```sql
   show engine innodb status;
   show status like 'Innodb_buffer_pool_%';
   show status like  'innodb%read%';
   ```
5. bp 参数及监控(https://hdm.console.aliyun.com/dbMonitor/MySQL#/performance/instance/rm-2zefr83ok57101uxm/detail)

   - 大小: `innodb_buffer_pool_size=3/4 * instance_mem` + 这个值一般很小需要重新设置
   - 个数: `innodb_buffer_pool_instances={LEAST(DBInstanceClassMemory/1G, 8)}`
   - old 区占比: `innodb_old_blocks_pct=37`(默认是 3/8)
   - 时间: `innodb_old_blocks_time=1000ms` 缓存时间

   ```sql
   show status like 'Innodb_buffer_pool_%';
   show status like  'innodb%read%';
   ```

   |             variable_name             |          value           |               comment                |
   | :-----------------------------------: | :----------------------: | :----------------------------------: |
   |    Innodb_buffer_pool_dump_status     | Dumping pool not started |
   |    Innodb_buffer_pool_load_status     |  pool(s) load completed  |                                      |
   |   Innodb_buffer_pool_resize_status    |            ""            |                                      |
   |   **Innodb_buffer_pool_pages_data**   |           7168           |                                      |
   |     Innodb_buffer_pool_bytes_data     |        117440512         |                                      |
   |    Innodb_buffer_pool_pages_dirty     |            0             |                                      |
   |    Innodb_buffer_pool_bytes_dirty     |            0             |                                      |
   |   Innodb_buffer_pool_pages_flushed    |          939361          |                                      |
   |   **Innodb_buffer_pool_pages_free**   |           1024           |                                      |
   |     Innodb_buffer_pool_pages_misc     |            0             |                                      |
   |    Innodb_buffer_pool_pages_total     |           8192           |                                      |
   |   Innodb_buffer_pool_read_ahead_rnd   |            0             |                                      |
   |     Innodb_buffer_pool_read_ahead     |           5939           |              预读的次数              |
   | Innodb_buffer_pool_read_ahead_evicted |            0             | 预读的未使用即被驱除页数(预读的效率) |
   | **Innodb_buffer_pool_read_requests**  |        342038502         |             读请求的次数             |
   |     **Innodb_buffer_pool_reads**      |           9507           |      表示从物理磁盘读取页的次数      |
   |     Innodb_buffer_pool_wait_free      |            0             |                                      |
   |   Innodb_buffer_pool_write_requests   |         9870536          |                                      |
   |           Innodb_data_read            |          17298           |           总共读入的字节数           |
   |           Innodb_data_reads           |            --            |      读取请求次数(可一次读多页)      |
   |           Innodb_pages_read           |                          |                                      |

   - pb 命中率计算:

     1. `1- (Innodb_buffer_pool_reads/Innodb_buffer_pool_read_requests)` == `{1-磁盘读次数/读取总次数}`
     2. bp 命中率都在 99% 以上: 如果低于这个值才需要考虑加大 innodb buffer pool 的大小

   - pb 使用率: innodb_buffer_pool_pages_data / (innodb_buffer_pool_pages_data + innodb_buffer_pool_pages_free)

6. 相关场景说明

   - update
   - insert
   - query
   - delete
   - bnl 下大表冷数据: io/cpu/mem/pb`{数据量小于 3/8 时-会被放入 young 区 | 大于 3/8 时会导致 young 区数据不能正常淘汰}`

7. 热点数据呗淘汰场景

   - 预读机制: 是指 MySQL 加载数据页时, 可能会把它相邻的数据页一并加载进来(局部性原理)
   - 全表扫描: 使用 Y/Old 解决

8. 多 buffer 实例

   - 一个 buffer pool 在多线程访问的时候，各个链表都会加锁处理
   - 如果 innodb_buffer_pool_size 小于 1G 时，设置 innodb_buffer_pool_instances 是无效的，都会是 1

   ![avatar](/static/image/mysql/innodb-bp-instances.png)

9. 运行中修改 buffer pool 大小

   - 5.7.5 之前，是不允许在运行时调整 buffer pool 大小的
   - 之后可以: 因为采用 chunk 为单位`innodb_buffer_pool_chunk_size{128m}`来申请内存空间

10. Adaptive Hash Index: 自适应 Hash 索引

    - 属于 buffer pool 中的一片内存
    - 当 InnoDB 观察到某个索引的值频繁被使用, 那么会创建自适应 hash 索引, 可以提高查询速度
    - 通过参数 innodb_adaptive_hash_index 来禁用或启动此特性: 默认为开启{aliyun off}

11. change bugger

    - 好处: 通过减少硬盘随机 IO 读与提高内存利用率, 并发能力更强
    - 限制: 聚簇索引和唯一索引是无法使用 change buffer(确保唯一性)
    - 场景: 对于写多读少的业务场景, 索引页在写完以后马上被访问到的概率很小(merge 需要成本), 此时 change buffer 的收益最高
      ![avatar](/static/image/mysql/innodb-bp-cb-scenario.png)
    - \_
    - 发生更新时, 如果数据/索引页在 Buffer Pool 里命中的话, 就直接更新缓存页
    - 否则, InnoDB 会将这些更新操作缓存在 change buffer 中, 这样就无需从硬盘读入索引页
    - 下次查询时, 读取页数据, 并将 change buffer 的内容应用到 bp 中后获取: 保证数据准确
    - 如果没有查询或者宕机时, 数据丢失问题解决(数据安全): 内存刷脏页

      ![avatar](/static/image/mysql/inndodb-bp-cb.png)

## 内存刷脏页(flush 链表): redolog / change bugger

1. 当内存数据页跟磁盘数据页内容不一致的时候, 我们称这个**内存页为 "脏页"**: change buffer
2. 内存数据写入到磁盘后, 内存和磁盘上的数据页的内容就一致了, 称为"干净页"
3. flush 时机

   - InnoDB 的 redo log 写满: **系统会停止所有更新操作**, 把 checkpoint 往前推进, redo log 留出空间可以继续写
   - 系统内存不足: 当需要新的内存页且内存不够用的时候, 就要淘汰一些数据页, 如果淘汰的是 "脏页", 就要将脏页写到磁盘
     1. 一个查询要淘汰的脏页个数太多, 会导致查询的响应时间明显变长
   - 系统空闲的时候:
   - MySQL 正常关闭: 内存的脏页都 flush 到磁盘上

4. innodb_io_capacity: 主机的 IO 能力{InnoDB 全力刷脏页时速度}

   - 建议: `磁盘的IOPS`
   - `fio -filename=$filename -direct=1 -iodepth 1 -thread -rw=randrw -ioengine=psync -bs=16k -size=500M -numjobs=10 -runtime=10 -group_reporting -name=mytest`

5. 脏页比例是通过 Innodb_buffer_pool_pages_dirty/Innodb_buffer_pool_pages_total
6. MySQL 刷脏页, 如果发现旁边的数据页刚好是脏页会一起刷掉{蔓延}: `innodb_flush_neighbors`
7. 设计策略控制刷脏页的速度
   - 脏页比例: `innodb_max_dirty_pages_pct{0.75}`
   - redo log 写盘速度

---

## reference

1. https://zhuanlan.zhihu.com/p/415004185
2. https://mp.weixin.qq.com/s?__biz=Mzg4NTcyNjczNg==&mid=2247504302&idx=1&sn=119d0819c3127b4194b6677dd90ca63b
