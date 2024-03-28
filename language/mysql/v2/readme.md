[toc]

## mysql

1. feature

   - basic
   - keyflow
   - ha
   - optimize
   - server
   - engine
   - question: pi

## detail

1. basic

   - introduce
   - syntax
   - view&sp&func
   - transaction && mvcc
   - index
   - lock
   - duration
   - log
   - config

2. keyflow

   - join
   - groupby
   - orderby
   - count
   - random
   - in-exists
   - or
   - limit
   - distinct
   - union
   - partition

3. ha

   - sm

4. optimize

   - practice
   - optimize

5. server

   - log

6. engine

   - innodb.md
   - buffer
   - log

7. others

   - sharding.md
   - [性能压测-aliyun](https://help.aliyun.com/document_detail/151977.htm)

---

## security

1. 不要使用 root
   - 使用的用户具有最小权限
   - 限制用户的 host: `172.*.*.*`
2. 不开放 3306, 且修改数据库端口号
3. 确保账户密码等安全
4. 防止 SQL 注入
5. 做好数据库备份工作
6. 代码相关不要暴露配置信息 + 做好用户权限 api 控制
