- 写请求直接发送给Leader
  logseq.order-list-type:: number
	- 客户端发送写数据请求
	  logseq.order-list-type:: number
	- Leader写数据写入自身
	  logseq.order-list-type:: number
	- Leader将要写的数据发送给Follower
	  logseq.order-list-type:: number
	- Follower写完后，发送ack给Leader
	  logseq.order-list-type:: number
	- Leader根据获取到的ack数目，**判断写入数据的节点数已经超过总节点数一半，发送ack给客户端通知已写完**
	  logseq.order-list-type:: number
	- Leader继续通知其他Follower节点写入数据
	  logseq.order-list-type:: number
- 写请求发送给Follower节点（Follower没有写数据的权限）
  logseq.order-list-type:: number
	- 客户端发送写数据的请求
	  logseq.order-list-type:: number
	- Follower节点将写请求发送给Leader
	  logseq.order-list-type:: number
	- Leader写数据写入到自身
	  logseq.order-list-type:: number
	- Leader将写数据发送给Follower，并根据获取到的ack数目是否超过总节点一半，来判断是否已经写完
	  logseq.order-list-type:: number
	- Leader写完成之后，应答ack给原本的Follower
	  logseq.order-list-type:: number
	- Follower回复给客户端
	  logseq.order-list-type:: number
	- Leader继续通知其他Follower节点写入数据
	  logseq.order-list-type:: number