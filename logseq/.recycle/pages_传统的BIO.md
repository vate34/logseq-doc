- 传统的网络通讯方式为阻塞式的，即
- 客户端向服务器端发出请求后，客户端会一直等待(不会再做其他事情)，直到服务器端返回结果或者网络出现问题。
- 服务器端同样的，当在处理某个客户端A发来的请求时，另一个客户端B发来的请求会等待，直到服务器端的这个处理线程完成上一个处理。
  
  **缺点**：同一时间只能接收和处理同一个客户端的请求，在高并发的情况下不能接受。
- ![image.png](../assets/image_1689587595025_0.png)
-