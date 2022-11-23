## intros

1. 确定使用类型

   - 首先一定要区分开 redis 缓存{解决并发}和存储的区别

2. 功能: `带宽不应该成为瓶颈`

   - 解决并发-缓存: hot & big key 等问题(流量带宽问题) | 失效等 redis 问题
   - 存储-存储: 需考虑数据持久化丢失问题
   - mq-stream: 消费模型及相关 mq 问题

3. 平衡代价问题

   - 如果是当做**缓存**使用则会引出复杂的数据一致性问题`{这个很不好保证在高可用高并发下}`: 当作缓存使用一定要慎重

4. 举例: 扣库存场景下尽量不要做网上先使用 redis 预减库存的操作{`意义不大[库存多时db还是不行]+引入数据一致性问题`}
   - 并发小, 库存小: 直接使用 db
   - 并发小, 库存大: 直接使用 db
   - 并发大, 库存小: `redis + mq` | _redis 预减库存 + db 也可以_
   - 并发大, 库存大: 只能使用 redis + mq 做, 可以抗巨大流量 | ~~redis 预减库存之后的流量 db 也扛不住~~

## reference

1. https://github.com/Alice52/java-ocean/issues/169
2. https://github.com/Alice52/Alice52/issues/75
3. [高并发下的缓存涉及](https://github.com/Alice52/Alice52/issues/139#issuecomment-1173870778)
