- # 第一节
- 服务器处理客户端请求的过程：
	- **连接管理**。客户端可以通过`TCP/IP`、`命名管道或共享内存`、`Unix域套接字`等方式连接到服务器，每个连接的客户端都会被分配一个进程来处理与它的交互，断开连接后不会立马销毁线程。
	  logseq.order-list-type:: number
	- **解析与优化**
	  logseq.order-list-type:: number
		- **查询缓存**。查询缓存会提升一些性能，但是维护缓存也会造成一些开销，MySQL8.0已删除该功能。
		  logseq.order-list-type:: number
		- **语法解析**。判断请求的语法是否正确，然后从文本中将要查询的表、各种查询条件都提取出来放到`MySQL`服务器内部使用的一些数据结构上来。
		  logseq.order-list-type:: number
		- **查询优化**。对查询语句优化以提高执行效率。
		  logseq.order-list-type:: number
	- **存储引擎**。`MySQL`服务器把数据的存储和提取操作都封装到了一个叫`存储引擎`的模块里
	  logseq.order-list-type:: number
	- > 为了管理方便，人们把`连接管理`、`查询缓存`、`语法解析`、`查询优化`这些并不涉及真实数据存储的功能划分为`MySQL server`的功能，把真实存取数据的功能划分为`存储引擎`的功能。各种不同的存储引擎向上边的`MySQL server`层提供统一的调用接口（也就是存储引擎API）