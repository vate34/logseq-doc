- Cookie：存储在客户端（用户浏览器）中的信息
- Session：存储在服务器中的用户信息，客户端中有对应的SessionID与之对应
- Token：客户端使用用户名密码登录后，服务端生成Token，客户端每次请求都携带，服务端再验证
- ## Cookie和Session
	- cookie：浏览器存储在本地的数据文件，一般是从服务端接收到，然后保存到本地。当再次请求对应网站时，会带上该域名下的cookie。Cookie的大小限制一般是4K，**Cookie设计为不可跨域名**。
	- session：即会话，是指存储在服务器上的记录了用户会话信息的数据。session没有大小、类型限制，也没有安全问题。
	- > Cookie可以让服务端程序跟踪每个客户端的访问，但是每次客户端的访问都必须传回这些Cookie，如果Cookie很多，这无形地增加了客户端与服务端的数据传输量。此外cookie保存在客户端，有被篡改、盗取的风险。而Session的出现正是为了解决这个问题。
	- ### Session机制
		- Session机制是一种服务器端的机制。其大概流程为：
			- 客户端访问服务端，服务端生成对应的状态信息session
			  logseq.order-list-type:: number
			- 服务端存储状态信息{sessionID: session}，将sessionID随同响应返回给客户端
			  logseq.order-list-type:: number
			- 客户端保存sessionID为cookie文件。（在禁用cookie的情况下可以将会话标识放在路径参数中）
			  logseq.order-list-type:: number
			- 客户端下一次请求时，携带sessionID；服务端利用sessionID提取状态信息
			  logseq.order-list-type:: number
		- 缺点：session机制最大的缺点为，**其使服务器状态化了**，在分布式集群场景下不适用。如：
		- 当用户的session信息只存储在当前访问的服务器中，当负载均衡把请求转发到另外一台服务器上，其没有用户的session信息，就会要求用户重新登录。解决方案：
			- sticky session。通过负载均衡服务器，使得每一个用户的请求都路由到同一个服务器上。
			  logseq.order-list-type:: number
			- session replication。在服务器之间进行session同步，每个服务器都有所有用户的session信息。增大网络开销，增加session数据占用， 不适用于大规模集群场景。
			  logseq.order-list-type:: number
			- session server。引入单独的服务器来存储session数据。缺点在于，使用网络读取session数据增加了时延和不稳定性，同时需要保证session服务器的高可用。
			  logseq.order-list-type:: number
	- ### 缺点：
		- 服务器需要记录每个用户的状态信息，内存开销大。
		  logseq.order-list-type:: number
		- 多机session问题，扩展性差。
		  logseq.order-list-type:: number
		- CORS（跨域资源共享）默认不携带cookie，导致跨域共享资源难。
		  logseq.order-list-type:: number
		- CSRF（跨域请求伪造）sessionID默认保存在cookie中，很容易被伪造请求自动携带上。
		  logseq.order-list-type:: number
- ## Token
	- Token即令牌，**基于token的身份验证是无状态的**，即不将用户会话信息存储在服务器中。
	- 基于token的身份验证的大致流程为：
		- 客户端使用用户名跟密码请求登录。
		  logseq.order-list-type:: number
		- 服务端收到请求后验证用户名和密码。
		  logseq.order-list-type:: number
		- 验证成功后，用特定的算法，将传过来的唯一标识和密钥生成一个token，将其发送给客户端。
		  logseq.order-list-type:: number
		- 客户端收到后，将其存储到local storage（不会被自动携带）或者cookie中。
		  logseq.order-list-type:: number
		- 客户端每次向服务器请求资源，都要携带服务端签发的token。
		  logseq.order-list-type:: number
		- 服务端收到请求，去验证客户端请求里面的toekn，如果验证成功，就向客户端返回请求的数据。
		  logseq.order-list-type:: number
	- Token的思想是算法验证，session的思想是信息存储对比。对比session机制，他的优点有：
		- 支持跨域访问。可以实现系统分离。
		  logseq.order-list-type:: number
		- 无状态。服务端可扩展
		  logseq.order-list-type:: number
			- > token机制在服务端不需要存储session信息，因为Token 自身包含了所有登录用户的信息
			  只需要在客户端的cookie或本地介质存储状态信息.
		- 解耦。不需要绑定到特定的身份验证方案。
		  logseq.order-list-type:: number
		- 更安全。
		  logseq.order-list-type:: number
			- 请求中发送token而不是cookie能够防止CSRF（跨域请求伪造）。
			  logseq.order-list-type:: number
			- token是有时效的，一段时间后用户需要重新验证。
			  logseq.order-list-type:: number
		- 更适用于移动应用。（移动端无法直接使用cookie）。
		  logseq.order-list-type:: number
		- 基于标准化。可以使用标准化的JWT（JSON WEB TOKEN）。
		  logseq.order-list-type:: number
	- ### Token的有效期
		- 根据安全性原则，token应当设置有效期，但是在有效期过期的时候，也不应当要求用户重新登录。
		- 解决方案：
			- 在服务端保存token状态，每次请求操作都会刷新token的过期时间（类似于session的策略）。缺点在于请求量可能非常大，进而导致的代价很大，持久化的代价就更大了。
			  logseq.order-list-type:: number
			- 使用`Refresh Token`。给客户端颁发token的同时，也颁发一个refresh token。一旦token过期，就反馈给客户端，客户端使用refresh token来请求一个全新的token继续使用。这种方案的优点在于，减少了更新有效期的操作。但是refresh token的有效期需要设置长一点。
			  logseq.order-list-type:: number