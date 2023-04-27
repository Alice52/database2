- keyword

  ```SQL
  SELECT DISTINCT CONCAT(RTRIM/LTRIM()) *  AS ... -- 连接函数/去空格
  FROM ...
  [LEFT/RIGHT/FULL] JOIN ... ON ...
  WHERE ... REGEXP BINARY BETWEEN ... AND ...  AND/OR XX NOT IN (..., ...) -- 闭区间 <> 不等于
  HAVING ....
  GROUP BY ... ASC/DESC
  ORDER BY ..
  -- LIMIT START_POSITION, LENGTH
  LIMIT LENGTH offset start_position
  ```

  ![avatar](/static/image/mysql/mysql-machine-sequence.png)

## keyword flow

1. join
2. group by ... having
3. order by
4. count/sum/avg: `没有匹配行时则返回 null`
5. random
6. in/exists
7. or
8. limit
9. rank
10. case
11. with
12. null
13. distinct
14. union
