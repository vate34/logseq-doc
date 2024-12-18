- 功能要求：
	- 根据用户id查询用户标签（用户画像）
	- 根据标签查询用户集合（人群）
- 用户ID（uid）uid 是一个连续递增的 Long 类型，它是唯一的，从 1 开始。
- 标签ID，tagId是一个连续递增的Integer，它是唯一的，从1开始。
- 实现两个方法：
	- 根据uid查询tagid集合。`Set<Integer> getUserPortraitByUid(Long uid);`
	- 查询tagid查询语句查询用户集合。`Set<Long> getUserCrowdByTag(Map<Integer, Boolean> tagQuery);`
- 细节：
	- user_tag数据应当手动生成
	- user_tag数据不变动
	- 标签规模不超过2000
	- uid规模会变化的
	- 两个接口查询时间在五秒之内
- 功能要求：
	- 设计1:标签数目少于10，用户数量少于100万
		- 提供文档设计和工程代码实现
		- 代码应当能够单机运行
	- 设计2:标签数目1000，用户数量为1亿。数据库不适合添加太多的索引，不超过20个；可以尝试使用空间换取时间。可以考虑使用`bitmap`来构建索引；实现可分为两个部分，提前建立索引和查询。建议实现索引构建和查询逻辑
- 实现方式：
	- 使用redis的bitmap结构来存储数据。
	- 用户到标签的映射：user1:"00100010"，每一位1代表当前用户在对应的标签为1，数据量较大，采用`EWAHCompressedBitmap`来优化空间占用。用于方法1
	- 标签到用户的映射：tag01:"00010001"，每一位1代表当前位置的用户，redis字符串最大长度为512M，即最多可存储约42亿个用户的信息。对与占用过多空间的问题，可以使用`EWAHCompressedBitmap`来优化空间占用。用于方法2
	- 需要一个全量用户的tag记录：alluser:00111111，获取第二个查询为非的情况。
	-