# 相同点
	- 底层都是`servlet`。
	  logseq.order-list-type:: number
	- 两者均是web网关，处理的是http请求。
	  logseq.order-list-type:: number
- # 不同点
	- zuul基于servlet实现，使用的是阻塞式的api，不支持长连接；gateway是基于Spring5构建，能够实现响应式非阻塞式的Api，支持长连接。
	  logseq.order-list-type:: number
	- zuul不依赖spring-webflux，可以扩展至其他微服务框架；gateway依赖于spring-webflux，仅适合于Spring Cloud套件。
	  logseq.order-list-type:: number
	- zuul仅支持同步；gateway支持异步。
	  logseq.order-list-type:: number
	- zuul内部没有实现限流、负载均衡，其负载均衡的实现是采用 Ribbon + Eureka 来实现本地负载均衡；gateway**功能更强大，内部实现了限流、负载均衡**等，扩展性也更强。
	  logseq.order-list-type:: number
-
- 参考： https://juejin.cn/post/6943147637491105806