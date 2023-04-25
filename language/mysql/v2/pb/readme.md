## pool buffer

1. 加速更新操作: wal(pb) + change buffer + redolog
2. 加速查询操作: pb
3. 内存命中率(内部是热点数据): 99%+ 才是健康的
   ```sql
   show engine innodb status
   ```
4. 相关参数

   - 大小: `innodb_buffer_pool_size=3/4 * instance_mem`
   - 个数: `innodb_buffer_pool_instances={LEAST(DBInstanceClassMemory/1G, 8)}`
   - old 区占比: `innodb_old_blocks_pct=37`(默认是 3/8)
   - 时间: `innodb_old_blocks_time=1000ms` 缓存时间

5. LRU 流程(0ysql 版本): 数据先进 old, 待满 1s 且被再次访问时移入 young 头部

   - 默认按照 5:3 的比例把整个 LRU 链表分成了 young 区域和 old 区域
   - 要访问数据页 P3, 由于 P3 在 young 区域, 将其移到链表头部, 变成状态 2
   - 之后要访问一个新的不存在于当前链表的数据页, 这时候依然是淘汰掉数据页 Pm, 但是新插入的数据页 Px, 是**放在 LRU_old 处**
   - 处于 old 区域的数据页, 每次被访问的时候都要做下面这个判断
     1. 若这个数据页在 LRU 链表中存在的时间超过了 1 秒, 就把它移动到链表头部
     2. 如果这个数据页在 LRU 链表中存在的时间短于 1 秒, 位置保持不变

   ![avatar](/static/image/mysql/mysql-pb-o-y.png)

6. 相关场景说明

   - update
   - insert
   - query
   - delete
   - bnl 下大表冷数据: io/cpu/mem/pb`{数据量小于 3/8 时-会被放入 young 区 | 大于 3/8 时会导致 young 区数据不能正常淘汰}`
