## Spring Bean 实例化过程
	- Spring 创建 Bean 的过程分为三个步骤：
		- 1. 实例化 Instantiation
			- 简单理解为 new 了一个对象，对应方法：`AbstractAutowireCapableBeanFactory`中的`createBeanInstance` 方法
		- 2. 属性赋值 Populate
			- 为实例化出来的对象填充属性，对应方法为：`AbstractAutowireCapableBeanFactory`的`populateBean`方法。
			- 该步骤也是主要发生循环依赖的地方。基于构造方法的注入，第一步和第二步是同时进行的，出现循环依赖会直接报错。
		- 3. 初始化 Initialization
			- 初始化方法，完成 AOP 代理，对应方法为：`AbstractAutowireCapableBeanFactory`的`initializeBean` 方法
- ## 三级缓存
	- Spring 创建单例实例的过程中，会将相关过程中生成的实例存储在三个等级的缓存中：
		- `private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);`
		- `private final Map<String, Object> earlySingletonObjects = new HashMap<>(16);`
		- `private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<>(16);`
	- |缓存|说明|
	  |--|--|
	  |singletonObjects|第一级缓存，存放可用的完全初始化，成品的Bean。|
	  |earlySingletonObjects|第二级缓存，存放半成品的Bean，半成品的Bean是已创建对象，但是未注入属性和初始化。用以解决循环依赖。|
	  |singletonFactories|singletonFactories	第三级缓存，存的是Bean工厂对象，用来生成半成品的Bean并放入到二级缓存中。用以解决循环依赖。如果Bean存在AOP的话，返回的是AOP的代理对象。|
	- 获取实例时，从这三级缓存逐层查询，通过匹配 beanName 获取到实例。详细可查看[[getBean 方法的代码]]
- ## 具体实例化过程
-
-
- 参考： [Spring-三级缓存和循环依赖](https://segmentfault.com/a/1190000023712597)