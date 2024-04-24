## 介绍
	- 分页查询：指将一个比较大的数据集分成多个小块（页），并只返回需要的那一页数据。
		- 分页查询通常需要记录数量（page size）和目标页数（page number）。
		- 在实际分页查询时，通常使用的是`LIMIT offset, pageSize`来指定查询范围。
		- 例如`select * from table_name limit 20, 10`，20为offset偏移量，表示从第21条查询，返回后边十条数据。
		- **实际查询并不是跳过 offset 行数据，而是获取 offset + pageSize 行数据，然后放弃前 offset 行，返回 pageSize 行。**
	- 深分页：
		- 当查询便宜量 offset 过大，我们称之为深度分页。此时需要扫描大量数据才能找到指定页的数据，查询性能会降低。
		- `SELECT * FROM t_order ORDER BY id LIMIT 1000000, 10`
- ## 优化方案
	- ### 1. SQL优化 - 子查询
		- 通过将 `select * ` 转变为 `select id`，把符合条件的 id 筛选出来，最后通过嵌套查询的方式按顺序取出对应的行即可。
		- ```sql
		  -- 优化前
		  select *
		  from people
		  order by create_time desc
		  limit 5000000, 10;
		  
		  -- 优化后
		  select a.*
		  from people a
		  inner join(
		      select id
		      from people
		      order by create_time desc
		      limit 5000000, 10
		  ) b ON a.id = b.id;
		  ```
	- ### 2. SQL 优化 - 索引优化
		- 将 where 条件和排序字段的相关字段添加联合索引
		- 如果查询的列不是很多可以使用覆盖索引来减少回表次数。 ((64e960c6-6609-4d9a-8264-c203a650f303))
	- ### 3. SQL 优化 - id 限定优化（分页游标）
		- 假设数据表的 id 是**连续递增**的，则可以根据上一次查询的结果，以及要查询的页数和记录数，计算出要查询页的ID范围。
		- 缺点比较明显：不支持跳页，而且要求 id 是升序的。
		- ```sql
		  select * 
		  from orders_history 
		  where type=2 
		  and id between 1000000 and 1000100 
		  limit 100;
		  ```
		- ```sql
		  select * from orders_history 
		  where id >= 1000001 
		  limit 100;
		  ```
	- ### 4. 业务限制
		- a. 强制指定查询条件
		- b. 限制最大页码数
	- ### 5. 分库分表