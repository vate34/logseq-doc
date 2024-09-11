- SpringCloud和Dubbo都是主流的微服务架构.
	- SpringCloud是Apache下的Spring体系下的微服务解决方案.
	- Dubbo是阿里系统中分布式微服务治理框架.
- ## 技术方面对比
	- SpringCloud 功能远远超过 Dubbo, Dubbo 只实现了服务治理(注册和发现). 但
	  是 SpringCloud 提供了很多功能, 有 21 个子项目
	- Dubbo 可以使用 Zookeeper 作为注册中心, 实现服务的注册和发现,
	  SpringCloud 不仅可以使用 Eureka 作为注册中心, 也可以使用 Zookeeper 作为
	  注册中心.
	- Dubbo 没有实现网关功能, 只能通过第三方技术去整合. 但是 SpringCloud 有
	  zuul 路由网关, 对请求进行负载均衡和分发. 提供熔断器, 而且和 git 能完美集成.
- ## 性能方面对比
	- 由于 Dubbo 底层是使用 Netty 这样的 NIO 框架，是基于 TCP 协议传输的，配合
	  以 Hession 序列化完成 RPC。
	- 而 SpringCloud 是基于 Http 协议+Rest 接口调用远程过程的，相对来说，Http
	  请求会有更大的报文，占的带宽也会更多。
	- 使用 Dubbo 时, 需要给每个实体类实现序列化接口, 将实体类转化为二进制进行
	  RPC 通信调用.而使用 SpringCloud 时, 实体类就不需要进行序列化.