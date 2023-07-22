- Redis 锁主要利用 Redis 的 `setnx`( set if not exist) 命令来实现。
	- 加锁命令：`SETNX key value`，当键不存在时，对键进行设置操作并返回成功，否则返回失败。KEY 是锁的唯一标识，一般按业务来决定命名。
	- 解锁命令：`DEL key`，通过删除键值对释放锁，以便其他线程可以通过 `SETNX` 命令来获取锁。
	- 锁超时：`EXPIRE key timeout`, 设置 key 的超时时间，以保证即使锁没有被显式释放，锁也可以在一定时间后自动释放，避免资源被永远锁住。
- 上述实现方式存在的问题：
	- `SETNX`和`EXPIRE`的非原子性。
	  logseq.order-list-type:: number
		- 如果`SETNX`成功，在设置锁超时时间后，服务器或网络出现故障，导致`EXPIRE`命令没有执行，锁没有设置超时时间变成死锁。解决方案：
			- a. 使用Lua脚本。
				- ```lua
				  if (redis.call('setnx', KEYS[1], ARGV[1]) < 1)
				  then return 0;
				  end;
				  redis.call('expire', KEYS[1], tonumber(ARGV[2]));
				  return 1;
				  // 使用实例
				  EVAL "if (redis.call('setnx',KEYS[1],ARGV[1]) < 1) then return 0; end; redis.call('expire',KEYS[1],tonumber(ARGV[2])); return 1;" 1 key value 100
				  ```
			- b. 添加过期时间。Redis在 2.6.12 版本开始，为 SET 命令增加一系列选项：
				- `SET key value [EX seconds] [PX milliseconds] [NX|XX]`
					- EX seconds: 设定过期时间，单位为秒
					- PX milliseconds: 设定过期时间，单位为毫秒
					- NX: 仅当key不存在时设置值
					- XX: 仅当key存在时设置值
				- `set`命令结合`nx`即为`setnx`，同时可添加`nx`参数为过期时间。例如：`set resourceName value ex 5 nx`。
	- 锁误解除。锁被其他线程错误解除。例如：
	  logseq.order-list-type:: number
		- > 如果线程 A 成功获取到了锁，并且设置了过期时间 30 秒，但线程 A 执行时间超过了 30 秒，锁过期自动释放，此时线程 B 获取到了锁；随后 A 执行完成，线程 A 使用 DEL 命令来释放锁，但此时线程 B 加的锁还没有执行完成，线程 A 实际释放的是线程 B 加的锁。
			- 解决方案为：在`value`添加当前线程的标识，在删除之前，根据其验证锁是否为当前线程所持有。
	- 超时解锁导致并发。执行时间超过过期时间导致锁释放，被其他线程获取，并发执行。
	  logseq.order-list-type:: number
		- > 如果线程 A 成功获取锁并设置过期时间 30 秒，但线程 A 执行时间超过了 30 秒，锁过期自动释放，此时线程 B 获取到了锁，线程 A 和线程 B 并发执行。
		- 解决方案：
			- 将过期时间设置足够长，确保代码逻辑在锁释放之前能够执行完成。
			  logseq.order-list-type:: number
			- (`Redisson`解决方案)为获取锁的线程增加守护线程，为将要过期但未释放的锁增加有效时间。
			  logseq.order-list-type:: number
	- 无法等待锁释放。上述命令都是立即返回结果的，如果客户端要求同步等待锁释放就无法使用。解决方案：
	  logseq.order-list-type:: number
		- 要求客户端轮训查询。当未获取到锁时，等待一段时间重新获取锁，直到成功获取锁或等待超时。比较消耗服务器资源，不建议。
		  logseq.order-list-type:: number
		- 使用`Redis`的发布订阅功能，当获取锁失败时，订阅锁释放消息，获取锁成功后释放时，发送锁释放消息。
		  logseq.order-list-type:: number
- # Redlock
- 以上几种基于 Redis 单机实现的分布式锁其实都存在一个问题，就是**加锁时只作用在一个 Redis 节点上**，即使 Redis 通过 Sentinel 保证了高可用，但由于 Redis 的复制是异步的，**Master 节点获取到锁后在未完成数据同步的情况下发生故障转移**，此时其他客户端上的线程依然可以获取到锁，因此会丧失锁的安全性。过程如下：
	- 客户端 A 从 Master 节点获取锁。
	- Master 节点出现故障，主从复制过程中，锁对应的 key 没有同步到 Slave 节点。
	- Slave 升 级为 Master 节点，但此时的 Master 中没有锁数据。
	- 客户端 B 请求新的 Master 节点，并获取到了对应同一个资源的锁。
	- 出现多个客户端同时持有同一个资源的锁，不满足锁的互斥性。
- 正因为如此，在 Redis 的分布式环境中，Redis 的作者 antirez 提供了 RedLock 的算法来实现一个分布式锁。
-
-
- 参考资料：
	- [分布式锁的实现之 redis 篇](https://xiaomi-info.github.io/2019/12/17/redis-distributed-lock/)
	- [基于Redis的分布式锁实现](https://juejin.cn/post/6844903830442737671)