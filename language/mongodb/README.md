## MongoDB Knowledge

### overview

1. 单机版数据量

   - 单数据库存储不超过 1 亿的数据比较合适
   -

2. 单机版 qps/connection: `8c32g`

   - connection: 1w+
   - qps: 8k

3. 单机版 RTT
4. 性能: 平稳到达瓶颈

   - `cpu:mem`: 1:4 最为适宜
   - **并发数增加到一定数量后, CPU 使用率和操作 QPS 数不再随着并发数的增加而提高**

### theory

| Scenario                          | Main Focus | Description                                                              |
| :-------------------------------- | :--------- | :----------------------------------------------------------------------- |
| No partition                      | CA         | The system is available and provides strong consistency                  |
| partition, majority connected     | AP         | Not synchronized writes from the old primary are ignored                 |
| partition, majority not connected | CP         | only read access is provided to avoid separated and inconsistent systems |

### [MongoDB Install](./mongodb-install.md)

1. introducation
2. window os
3. linux os

### [MongoDB CRUD](./mongodb-crud.md)

1. insert
2. find
3. remove
4. update
5. 常见函数与投影: limit skip sort
6. [practise](./mongodb-crud-practise.md)

### [MongoDB Relation](./mongodb-doc-relation.md)

1. relations
2. demo

### [Mongoose](./mongodb-mongoose.md)

1. mongoose introducation
   - 相关认知
   - mongoose 对象
     1. Schema(definition, option)
     2. Model
     3. Document
2. mongoose 基本使用
   - 基础
   - demo
3. mongoose 模块化

---

## reference

1. [mongodb 性能-aliyun](https://help.aliyun.com/document_detail/144258.html)
2. [mongodb 性能-单机 qps](https://help.aliyun.com/document_detail/440059.html)
3. [mongodb 性能-自建](https://www.cnblogs.com/lovecindywang/archive/2011/03/02/1969324.html)
