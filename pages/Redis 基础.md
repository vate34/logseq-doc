# 特点
	- 高性能。运行在内存中，速度快
	  logseq.order-list-type:: number
	- 高并发。使用单线程；多路I/O复用模型，非阻塞式IO
	  logseq.order-list-type:: number
	- 操作原子性
	  logseq.order-list-type:: number
- # 单线程
	- Redis 命令的核心模块是单线程的，与服务端的连接、事件处理是多线程的。原因：
	- 基于内存的操作，**CPU 不是性能瓶颈，瓶颈是网络开销和机器内存**。多线程的目的是提高 CPU 的利用率，在此处不是必要的
	  logseq.order-list-type:: number
	- **多线程操作会引入锁**，以及线程切换等功能，会消耗CPU
	  logseq.order-list-type:: number
	- 单线程的核心是使用了 ((66a70caf-18a8-4097-a749-73a7c94c65db)) ，采用多路 I/O 复用技术可以让单个线程高效的处理多个连接请求(尽量减少网络 I/O 的时间消耗)
	  logseq.order-list-type:: number
	- 多线程：也有可以异步处理的删除操作，用于避免删除数据消耗了不必要的时间
	  logseq.order-list-type:: number
- # 数据类型
	- **字符串**。可以是字符串、整数或浮点数。对整个字符串或字符串的一部分进行操作；对整数或浮点数进行自增或自减操作。
	  logseq.order-list-type:: number
	- List 列表**。一个链表，链表上的每个节点都包含一个字符串。对链表的两端进行push和pop操作，读取单个或多个元素；根据值查找或删除元素；**
	  logseq.order-list-type:: number
	- Set 集合**。包含字符串的无序集合。字符串的集合，包含基础的方法有看是否存在添加、获取、删除；还包含计算交集、并集、差集等**
	  logseq.order-list-type:: number
	- Hash 散列**。包含键值对的无序散列表。包含方法有添加、获取、删除单个元素**
	  logseq.order-list-type:: number
	- Zset有序集合。和散列一样，用于存储键值对。字符串成员与浮点数分数之间的有序映射；元素的排列顺序由分数的大小决定；包含方法有添加、获取、删除单个元素以及根据分值范围或成员来获取元素
	  logseq.order-list-type:: number
	- HyperLogLogs 基数统计。这个结构可以非常省内存的去统计各种计数，比如注册 IP 数、每日访问 IP 数、页面实时UV、在线用户数，共同好友数等。
	  logseq.order-list-type:: number
	- Bitmap 位存储。Bitmap 即位图数据结构，都是操作二进制位来进行记录，只有0 和 1 两个状态。
	  logseq.order-list-type:: number
	- Geospatial 地理位置。保存地理信息。geo底层的实现原理实际上就是Zset, 我们可以通过Zset命令来操作geo
	  logseq.order-list-type:: number
	- Stream
	  logseq.order-list-type:: number
- # 事务
	- Redis 事务的本质是通过 MULTI、EXEC、WATCH 等一组命令的集合。事务支持一次执行多个命令，一个事务中所有命令都会被序列化。在事务执行过程，会按照顺序串行化执行队列中的命令，其他客户端提交的命令请求不会插入到事务执行命令序列中。
	- **Redis 不支持回滚**，“Redis 在事务失败时不进行回滚，而是继续执行余下的命令”， 所以 Redis 的内部可以保持简单且快速。
	  logseq.order-list-type:: number
	- 如果在一个事务中的命令出现错误，那么所有的命令都不会执行**；**
	  logseq.order-list-type:: number
	- 如果在一个事务中出现运行错误，那么正确的命令会被执行**。
	  logseq.order-list-type:: number
- # 删除策略
	- 定时删除。对于每一个设置了过期时间的 key 都会创建一个定时器，一旦达到过期时间都会删除。
	  logseq.order-list-type:: number
		- 优点：这种方式立即清除过期数据，对内存比较好。
		- 缺点：占用了大量 CPU 的资源去处理过期数据，会影响 redis 的吞吐量和响应时间。
	- 惰性删除。当访问一个 key 的时候，才会判断该 key 是否过期，如果过期就删除。
	  logseq.order-list-type:: number
		- 优点：该方式能最大限度节省 CPU 的资源。
		- 缺点：但是对内存不太好，有一种比较极端的情况：出现大量的过期 key 没有被再次访问，因为不会被清除，导致占用了大量的内存。
	- 定期删除。每隔一段时间，扫描redis 中过期key 的字典，并清除部分过期的key。这种方式是前俩种一种折中方法。不同的情况下，调整定时扫描时间间隔，让CPU 与 内存达到最优。
	  logseq.order-list-type:: number
- # 内存淘汰策略
	- Redis 内存淘汰策略是指达到maxmemory极限时，使用某种算法来决定来清理哪些数据，以保证新数据存入。
	- 不处理，等报错（默认配置）。发现内存不够时，不删除key，执行写入命令时直接返回错误信息。
	  logseq.order-list-type:: number
	- 从所有的结果集中的 key 中挑选，进行淘汰
	  logseq.order-list-type:: number
	- 从设置了过期时间的 key 中挑选，进行淘汰
	  logseq.order-list-type:: number