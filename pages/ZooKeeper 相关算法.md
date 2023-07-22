- # Paxos 算法
- 一种基于消息传递且具有高度容错性的一致性算法。
- 解决的问题：如何快速正确地在一个分布式系统中，对某个数据达成一致，并且保证不论发生任何异常，都不会破坏整个系统的一致性。
- # ZAB协议
- ZAB（ZooKeeper Atomic Broadcast，ZooKeeper原子广播）协议，基于Paxos算法的数据一致性算法。
- 协议内容分为两部分：
	- ## 消息广播模式
		- 消息广播，类似于两段式提交协议，由于其使用队列发送事务消息，保证了顺序一致性：
			- Leader接收到写数据请求，会为该广播事务`Proposal`分配一个事务ID`ZXID`
			  logseq.order-list-type:: number
			- Leader会为每个Follower服务器各自分配一个单独的**队列**，将`Proposal`添加到到队列中，以**FIFO策略**进行消息的发送。同时将`Proposal`写入到磁盘日志文件。
			  logseq.order-list-type:: number
			- 每个Follower接收到事务后，将其以事务日志的形式写入到磁盘日志文件中。然后返回给Leader一个ACK响应。
			  logseq.order-list-type:: number
			- Leader收到ACK数目超过服务器半数时，发送`Commit`消息给所有的Follower。同时将磁盘中的事务日志数据加载到ZNode内存数据结构中。
			  logseq.order-list-type:: number
			- Follower接收到`Commit`消息后，将磁盘中的数据加载到ZNode内存数据结构中。
			  logseq.order-list-type:: number
	- ## 崩溃恢复模式
	- 两段式提交的消息广播模式中，有可能因Leader宕机带来数据不一致，有两种场景：
		- Leader发起事务后，还没向Follower发送事务消息就宕机
		  logseq.order-list-type:: number
		- Leader收到半数ACK就宕机，还没向Follower发送Commit消息就宕机
		  logseq.order-list-type:: number
	- ZAB协议崩溃恢复要求满足两个保证：
		- 确保Leader**已经发起且发送了消息**的事务`Proposal`，Follower必须执行
		  logseq.order-list-type:: number
		- 确保Leader**已经发起但未发送消息**的事务`Proposal`，直接被丢弃
		  logseq.order-list-type:: number
	- 崩溃恢复分为两步：Leader选举和数据恢复。
		- **Leader选举**，[[ZooKeeper Leader选举机制]]。根据上述两个保证，ZAB协议需要保证选举出来的Leader满足以下条件：
			- 新Leader必须不能包含未提交的事务（即不能是原Leader）
			  logseq.order-list-type:: number
			- 新Leader节点中包含最大的事务ID，ZXID。
			  logseq.order-list-type:: number
		- 实际上选举过程中的“投票给`ZXID`最大的节点”这一过程，已经保证了这两个条件
		- **数据恢复**，Leader重新工作之前，需要确保所有Follower将未同步的事务都从Leader同步过，且应用到数据内存后，才会将该Follower添加到实际的Follower列表中。
		-