# IOC
	- 在传统的编程结构中，调用者需要其他的对象的协助时，往往由调用者创建被调用者的实例对象。在 Spring 中，创建被调用者的工作不再由调用者完成而是交给容器，因此被称为控制反转（IOC），也被称为依赖注入。
	- ## 常见的依赖注入方式
		- ### @Autowired注入
			- 1. 简单直观
			- 2. 根据调用顺序，有空指针风险
		- ### 构造器注入
			- 1. 可以解决循环依赖的问题
			- 2. 避免空指针风险
- # AOP
  id:: 64e960c6-2e3f-498b-be35-3c5367b9a023
	- OOP（面向对象编程）使用继承、多态等特性建立的是一套纵向层次结构，而AOP（面向切面编程）则采取横向抽取机制，将程序运行过程分解为各个切面，达到模块间解耦的作用。目前最流行的AOP有Spring AOP和AspectJ
	- ## AOP相关术语
		- `Joinpoint`(连接点) 指那些被拦截到的点，在Spring中，可以被动态代理拦截目标类的方法
		- `Pointcut`(切入点) 指要对哪些Joinpoint进行拦截，即被拦截的连接点
		- `Advice`(通知) 指拦截到Joinpoint之后要做的事情，即对切入点增强的内容
		- `Target`(目标) 指代理的目标对象
		- `Weaving`(植入) 指把增强代码应用到目标上，生成代理对象的过程
		- `Proxy`(代理) 指生成的代理对象
		- `Aspect`(切面) 切入点和通知的结合
	- ## AOP的实现
		- 基于 JDK动态代理和CGLIB实现的。**如果目标对象实现了接口，默认情况下会采用 JDK 的动态代理，如果目标对象没有实现了接口,会使用 CGLIB 动态代理。** [[Java 代理]] 和 [[CGLIB]]