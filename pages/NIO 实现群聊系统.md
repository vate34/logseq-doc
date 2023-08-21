- 服务器端：可以检测用户上线，离线，并实现消息转发功能。
- 客户端：通过 Channel 可以无阻塞发送数据给其他所有用户，同时可以接收其他用户发送的消息（由服务器转发得到）。
- ```java
   public class GroupChatClient {
      private static final String HOST = "127.0.0.1";
      private static final int PORT = 6667;
      private Selector selector;
      private SocketChannel socketChannel;
      private String username;
      public GroupChatClient() {
          try {
              selector = Selector.open();
              //连接服务器
              socketChannel = SocketChannel.open(new InetSocketAddress(HOST, PORT));
              //设置非阻塞
              socketChannel.configureBlocking(false);
              //注册
              socketChannel.register(selector, SelectionKey.OP_READ);
              username = socketChannel.getLocalAddress().toString().substring(1);
              System.out.println("客户端: " + username + ",准备就绪...");
          } catch (IOException e) {
              e.printStackTrace();
          }
      }
  
      /**
       * 向服务器发送数据
       *
       * @param info
       */
      public void sendInfo(String info) {
          info = username + "说: " + info;
          try {
              socketChannel.write(ByteBuffer.wrap(info.getBytes()));
          } catch (IOException e) {
              e.printStackTrace();
          }
      }
  
      /**
       * 读取服务端回复的消息
       */
      public void readInfo() {
          try {
              //有可用通道
              if (selector.select() > 0) {
                  Iterator<SelectionKey> iterator = selector.selectedKeys().iterator();
                  while (iterator.hasNext()) {
                      SelectionKey key = iterator.next();
                      if (key.isReadable()) {
                          //得到相关的通道
                          SocketChannel sc = (SocketChannel) key.channel();
                          //得到一个buffer
                          ByteBuffer buffer = ByteBuffer.allocate(1024);
                          //读取
                          sc.read(buffer);
                          //把读取到的缓冲区数据转成字符串
                          String msg = new String(buffer.array());
                          System.out.println(msg.trim());
                      }
                      iterator.remove(); //删除当前的selectionKey，防止重复操作
                  }
              }
          } catch (IOException e) {
              e.printStackTrace();
          }
      }
  
      public static void main(String[] args) {
          //启动客户端
          GroupChatClient chatClient = new GroupChatClient();
          //启动一个线程,每隔3秒，读取从服务器端发送的数据
          new Thread(() -> {
              while (true) {
                  chatClient.readInfo();
                  try {
                      TimeUnit.MILLISECONDS.sleep(500);
                  } catch (InterruptedException e) {
                      e.printStackTrace();
                  }
              }
          }).start();
          //发送数据给服务器
          Scanner scanner = new Scanner(System.in);
          while (scanner.hasNextLine()) {
              chatClient.sendInfo(scanner.nextLine());
          }
      }
  } 
   public class GroupChatServer {
      //定义属性
      private Selector selector;
      private ServerSocketChannel listenChannel;
      private static final int PORT = 6667;
      public GroupChatServer() {
          try {
              //获得选择器
              selector = Selector.open();
              //listenChannel
              listenChannel = ServerSocketChannel.open();
              //绑定端口
              listenChannel.socket().bind(new InetSocketAddress(PORT));
              //设置非阻塞模式
              listenChannel.configureBlocking(false);
              //将该listenChannel注册到Selector
              listenChannel.register(selector, SelectionKey.OP_ACCEPT);
          } catch (IOException e) {
              e.printStackTrace();
          }
      }
  
      public static void main(String[] args) {
          //创建一个服务器对象
          GroupChatServer groupChatServer = new GroupChatServer();
          //监听
  		while(true) {
  			groupChatServer.listen();
  		}
      }
  
      /**
       * 监听
       */
      public void listen() {
          try {
              //如果返回的>0，表示已经获取到关注的事件
              while (selector.select() > 0) {
                  Iterator<SelectionKey> iterator = selector.selectedKeys().iterator();
                  //判断是否有事件
                  while (iterator.hasNext()) {
                      //获得事件
                      SelectionKey key = iterator.next();
                      //如果是OP_ACCEPT，表示有新的客户端连接
                      if (key.isAcceptable()) {
                          SocketChannel socketChannel = listenChannel.accept();
                          //设置为非阻塞
                          socketChannel.configureBlocking(false);
                          //注册到Selector
                          socketChannel.register(selector, SelectionKey.OP_READ);
                          System.out.println("获取到一个客户端连接 : " + socketChannel.getRemoteAddress() + " 上线!");
                      } else if (key.isReadable()) {
                          //如果是读事件,就读取数据
                          readData(key);
                      }
                      iterator.remove();
                  }
              }
          } catch (IOException e) {
              e.printStackTrace();
          } finally {
          }
      }
  
      /**
       * 读取客户端消息
       */
      private void readData(SelectionKey key) {
          SocketChannel channel = null;
          try {
              //得到channel
              channel = (SocketChannel) key.channel();
              //创建buffer
              ByteBuffer buffer = ByteBuffer.allocate(1024);
              if (channel.read(buffer) != -1) {
                  String msg = new String(buffer.array());
                  System.out.println(msg);
                  // 转发消息给其它客户端(排除自己)
                  sendInfoOtherClients(msg, channel);
              }
          } catch (Exception e) {
              try {
                  System.out.println(channel.getRemoteAddress() + " 下线了!");
                  // 关闭通道
                  key.cancel();
                  channel.close();
              } catch (IOException ioException) {
                  ioException.printStackTrace();
              }
          } finally {}
      }
  
      /**
       * 转发消息给其它客户端(排除自己)
       */
  
      private void sendInfoOtherClients(String msg, SocketChannel self) throws IOException {
          //服务器转发消息
          System.out.println("服务器转发消息中...");
          //遍历所有注册到selector的socketChannel并排除自身
          for (SelectionKey key : selector.keys()) {
              //反向获取通道
              Channel targetChannel = key.channel();
              //排除自身
              if (targetChannel instanceof SocketChannel && targetChannel != self) {
                  //转型
                  SocketChannel dest = (SocketChannel) targetChannel;
                  //将msg存储到buffer中
                  ByteBuffer buffer = ByteBuffer.wrap(msg.getBytes());
                  //将buffer中的数据写入通道
                  dest.write(buffer);
              }
          }
      }
  }
  ```