- Kafka 事务可以保证对多个分区写入操作的原子性。操作的原子性是指多个操作要么全部成功，要么全部失败。为了使用事务，Producer 必须**显式设置**唯一的`transactionalId`，事务要求**生产者开启幂等性**，因此通过将 `transactional.id`参数设置为非空从而开启事务特性，同时需要将 `enable.idempotence`设置为`true`。
- # 过程与原理
- 查找事务协调器：查找事务协调器`Transaction Coordinator`：生产者向任意Broker发送请求，获取事务协调器地址。
- 初始化事务
  logseq.order-list-type:: number
	- 生产者向事务协调器请求`PID`(Producer ID)
	  logseq.order-list-type:: number
	- 事务协调器在 `Transaction Log`中记录这`<TransactionId, pid>`的映射关系。
	  logseq.order-list-type:: number
- 开始事务
  logseq.order-list-type:: number
	- 生产者在本地开始事务。不需要事务协调器，因为其是在生产者发送第一条消息之后才认为事务已开启。
- read-process-write流程：
  logseq.order-list-type:: number
	- 一旦生产者开始发送消息，会将该`<Transaction, Topic, Partition>`存于 Transaction Log内，并将其状态置为 BEGIN。
	  logseq.order-list-type:: number
	- 此时虽然没有commit或者abort，但是生产者发送的消息已经保存到Broker上了。即使后面执行 abort，消息也不会删除，只是更改状态字段标识消息为 abort状态。
	  logseq.order-list-type:: number
- 事务提交或者终结：
  logseq.order-list-type:: number
	- 在生产者执行`commitTransaction`或者`abortTransaction`时，事务协调器会执行一个两段式提交：
		- 第一阶段，将 Transaction Log内的该事务状态设置为 `PREPARE_COMMIT`或 `PREPARE_ABORT`。
		  logseq.order-list-type:: number
		- 第二阶段，将 `Transaction Marker`写入该事务涉及到的所有消息（即将消息标记为committed 或 aborted）。
		  logseq.order-list-type:: number
	- 一旦`Transaction Marker`写入完成，`Transaction Coordinator`会将最终的`COMPLETE_COMMIT`或`COMPLETE_ABORT`状态写入`Transaction Log`中以标明该事务结束。