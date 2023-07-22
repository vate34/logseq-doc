- # 监听器原理
	- （客户端）创建一个main线程
	  logseq.order-list-type:: number
	- 在main线程中创建ZooKeeper客户端，此时会创建两个线程
	  logseq.order-list-type:: number
		- connect：负责网络连接通信
		  logseq.order-list-type:: number
		- listener：负责监听
		  logseq.order-list-type:: number
	- 通过connect线程将注册的监听事件发送给ZooKeeper
	  logseq.order-list-type:: number
	- ZooKeeper将注册的监听事件添加到注册监听器列表中
	  logseq.order-list-type:: number
	- ZooKeeper监听到有数据或路径变化，就会将这个消息发送给listener线程
	  logseq.order-list-type:: number
	- listener线程内部调用process方法
	  logseq.order-list-type:: number
- # 常见的监听类型
	- 监听节点数据的变化
	  logseq.order-list-type:: number
	- 监听子节点增减的变化
	  logseq.order-list-type:: number
- # API操作
	- 监听节点值的变化：`get -w <node_path>`。注册一次只能监听一次，监听对象被修改后只能监听到第一次修改事件，想要继续监听必须重新注册。
	  logseq.order-list-type:: number
	- 监听子节点的变化：`ls -w <node_path>`。同样的，注册一次只能监听一次，想要多次生效，必须重新监听。
	  logseq.order-list-type:: number
-