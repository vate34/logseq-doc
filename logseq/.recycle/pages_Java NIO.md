# Java NIO
	- NIO 中的 N 可以理解为 New，也可以理解为 Non-blocking（非阻塞），它是面向缓冲的，基于通道的 I/O 操作方法
	- **NIO一个重要的特点是：socket主要的读、写、注册和接收函数，在等待就绪阶段都是非阻塞的，真正的I/O操作是同步阻塞的（消耗CPU但性能非常高）。**
	- NIO 的核心组件包括：
		- Channel(通道)
		- Buffer(缓冲区)
		- Selector(选择器)
- # Channel（通道）
	- 通道是对 BIO 中流的模拟，其与流的不同之处在于：流是单向的，一个流只能负责读或者写；通道是单项的，可以同时用于读写。
	- 通道包括一下类型：
		- `FileChannel`：从文件中读写数据；
		- `DatagramChannel`：通过 UDP 读写网络中数据；
		- `SocketChannel`：通过 TCP 读写网络中数据；
		- `ServerSocketChannel`：可以监听新进来的 TCP 连接，对每一个新进来的连接都会创建一个 SocketChannel。
- # Buffer（缓冲区）
	- BIO 是使用流来读写数据，一个流一次只读写一个字节；而 **NIO 是使用块来作为基本单位处理数据**。
	- 不同与 BIO 的边读文件边处理数据，NIO 向通道读写的数据，都必须先放置到缓冲区（Buffer）中。
	- 缓冲区实质上是一个数组，但它不仅仅是一个数组。缓冲区提供了对数据的结构化访问，而且还可以跟踪系统的读/写进程。
	- 缓冲区的类型如下：
		- `ByteBuffer`
		- `CharBuffer`
		- `ShortBuffer`
		- `IntBuffer`
		- `LongBuffer`
		- `FloatBuffer`
		- `DoubleBuffer`
	- 缓冲区的状态变量有：
		- `capacity`：最大容量；
		- `position`：当前已经读写的字节数；
		- `limit`：还可以读写的字节数。
		- `mark`：记录上一次 postion 的位置，默认是 0，算是一个便利性的考虑，往往不是必须 的。
	- 代码示例：
	  
	  ```java
	  public static void fastCopy(String src, String dist) throws IOException {
	    /* 获得源文件的输入字节流 */
	    FileInputStream fin = new FileInputStream(src);
	    /* 获取输入字节流的文件通道 */
	    FileChannel fcin = fin.getChannel();
	    /* 获取目标文件的输出字节流 */
	    FileOutputStream fout = new FileOutputStream(dist);
	    /* 获取输出字节流的通道 */
	    FileChannel fcout = fout.getChannel();
	    /* 为缓冲区分配 1024 个字节 */
	    ByteBuffer buffer = ByteBuffer.allocateDirect(1024);
	    while (true) {
	        /* 从输入通道中读取数据到缓冲区中 */
	        int r = fcin.read(buffer);
	        /* read() 返回 -1 表示 EOF */
	        if (r == -1) {
	            break;
	        }
	        /* 切换读写 */
	        buffer.flip();
	        /* 把缓冲区的内容写入输出文件中 */
	        fcout.write(buffer);
	        /* 清空缓冲区 */
	        buffer.clear();
	    }
	  }
	  ```
	- NIO 还提供了一个可以直接访问物理内存的类 `DirectBuffer`。普通的 `Buffer` 分配的是 JVM 堆内存，而 `DirectBuffer` 是直接分配物理内存。
- # Selector(选择器)
	- NIO 被称为非阻塞式 IO，因为其非阻塞性特征被广泛使用。而 Selector 是其基础，用于检查一个或多个 Channel 的状态是否可读写。NIO 实现了 ((66a70caf-18a8-4097-a749-73a7c94c65db)) 的 Reactor 模型：
		- 一个线程（`Thread`）使用一个**Selector 通过轮询的方式去监听多个 Channel 上的事件（`accpet`、`read`）**，如果某个 `Channel` 上面发生监听事件，这个 `Channel` 就处于就绪状态，然后进行 I/O 操作。
		- 通过**配置监听的 `Channel` 为非阻塞**，那么当 `Channel` 上的 IO 事件还未到达时，就不会进入阻塞状态一直等待，而是继续轮询其它 `Channel`，找到 IO 事件已经到达的 `Channel` 执行。
		- 因为创建和切换线程的开销很大，因此使用**一个线程来处理多个事件**而不是一个线程处理一个事件具有更好的性能。
	- 需要注意的是，只有 `SocketChannel` 才能配置为非阻塞，而 `FileChannel` 不能，因为 `FileChannel` 配置非阻塞也没有意义。
- # 代码示例
	- ```java
	  public class NIOServer {
	  public static void main(String[] args) throws IOException {
	  // 创建选择器 
	      Selector selector = Selector.open();
	  // 将通道注册到选择器上
	      ServerSocketChannel ssChannel = ServerSocketChannel.open();
	      ssChannel.configureBlocking(false);
	      ssChannel.register(selector, SelectionKey.OP_ACCEPT);
	      ServerSocket serverSocket = ssChannel.socket();
	      InetSocketAddress address = new InetSocketAddress("127.0.0.1", 8888);
	      serverSocket.bind(address);
	      while (true) {
	  // 监听事件
	          selector.select();
	  // 获取到达的事件
	          Set<SelectionKey> keys = selector.selectedKeys();
	          Iterator<SelectionKey> keyIterator = keys.iterator();
	          while (keyIterator.hasNext()) {
	              SelectionKey key = keyIterator.next();
	              if (key.isAcceptable()) {
	                  ServerSocketChannel ssChannel1 = (ServerSocketChannel) key.channel();
	                  // 服务器会为每个新连接创建一个 SocketChannel
	                  SocketChannel sChannel = ssChannel1.accept();
	                  sChannel.configureBlocking(false);
	                  // 这个新连接主要用于从客户端读取数据
	                  sChannel.register(selector, SelectionKey.OP_READ);
	              } else if (key.isReadable()) {
	                  SocketChannel sChannel = (SocketChannel) key.channel();
	                  System.out.println(readDataFromSocketChannel(sChannel));
	                  sChannel.close();
	              }
	              keyIterator.remove();
	          }
	      }
	  }
	  }
	  ```