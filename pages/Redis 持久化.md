## RDB
	- 快照，持久化的默认方式。这种方式是定期将内存中数据以快照的方式写入到二进制文件中，默认的文件名为dump.rdb。
	- **优点**
		- 性能最大化。使用了子线程来操作
		  logseq.order-list-type:: number
		- 大数据集情况下，比 AOF 启动效率高
		  logseq.order-list-type:: number
	- **缺点**
		- 数据安全性低。可能会出现数据丢失情况
		  logseq.order-list-type:: number
		- 使用子线程协助完成，可能会导致服务器停机几百毫秒
		  logseq.order-list-type:: number
- ## AOF
	- 在使用 aof 持久化方式时，redis 会将收到的写命令都通过 write 函数追加到文件中(默认是 appendonly.aof)。
	- **优点**
		- 1. 数据安全
	- **缺点**
		- 1. 文件大，恢复慢。（文件较大的问题可以通过触发AOF重写解决）
		- 2. 数据集大的时候，启动慢