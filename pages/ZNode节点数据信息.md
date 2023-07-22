- 查看节点信息使用命令 `ls -s <node_name>`:
	- ```config
	  cZxid = 0x0
	  ctime = Thu Jan 01 00:00:00 UTC 1970
	  mZxid = 0x0
	  mtime = Thu Jan 01 00:00:00 UTC 1970
	  pZxid = 0x0
	  cversion = -1
	  dataVersion = 0
	  aclVersion = 0
	  ephemeralOwner = 0x0
	  dataLength = 0
	  numChildren = 1
	  ```
- 具体含义：
	- cZxid：创建节点的事务ID
	- ctime：节点被创建的毫秒数
	- mZxid：最后更新节点的事务ID
	- mtime：节点最后被修改的毫秒数
	- pZxid：子节点最后更新的事务ID
	- cversion：子节点变化号，即子节点修改次数
	- dataVersion：节点数据变化号
	- aclVersion：节点控制列表的变化号
	- ephemeralOwner：如果是临时节点，这个是节点拥有者的session id，否则为0
	- dataLength：节点的数据长度
	- numChildren：子节点数量