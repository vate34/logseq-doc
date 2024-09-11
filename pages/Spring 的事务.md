## 什么是事务
	- 事务是逻辑上的一组操作，要么都执行，要么都不执行。
	- **事务能否生效数据库引擎是否支持事务是关键。**比如常用的 MySQL 数据库默认使用支持事务的 `innodb`引擎。但是，如果把数据库引擎变为 `myisam`，那么程序也就不再支持事务了！
- ## 事务的特性
	- 即`ACID`，与 [[MySQL 事务]] 中的特性一致。
	- **原子性**（`Atomicity`）：事务是最小的执行单位，不允许分割。事务的原子性确保动作要么全部完成，要么完全不起作用；
	- **一致性**（`Consistency`）：执行事务前后，数据保持一致，例如转账业务中，无论事务是否成功，转账者和收款人的总额应该是不变的；
	- **隔离性**（`Isolation`）：并发访问数据库时，一个用户的事务不被其他事务所干扰，各并发事务之间数据库是独立的；
	- **持久性**（`Durability`）：一个事务被提交之后。它对数据库中数据的改变是持久的，即使数据库发生故障也不应该对其有任何影响。
- ## Spring 支持的事务管理方式
	- ### 编程式事务管理
		- 通过 `TransactionTemplate`或者`TransactionManager`手动管理事务，实际应用中很少使用。
	- ### 声明式事务管理
		- 基于 ((64e960c6-2e3f-498b-be35-3c5367b9a023)) 实现，使用`@Transactional`注解进行管理的方式，代码侵入性小。`@Transactional`注解的相关参数如下：
		- | 属性名 | 说明 |
		  | ---- | ---- | ---- |
		  | propagation | 事务的传播行为，默认值为 REQUIRED |
		  | isolation | 事务的隔离级别，默认值采用 DEFAULT |
		  | timeout | 事务的超时时间，默认值为-1（不会超时）。如果超过该时间限制但事务还没有完成，则自动回滚事务。 |
		  | readOnly | 指定事务是否为只读事务，默认值为 false。 |
		  | rollbackFor | 用于指定能够触发事务回滚的异常类型，并且可以指定多个异常类型。 |
- ## 事务传播机制
	- 事务传播机制就是，多个事务方法互相调用时，事务如何在这些方法间传播。当事务方法被另一个事务方法调用时，必须指定事务应该如何传播。例如：方法可能继续在现有事务中运行，也可能开启一个新事务，并在自己的事务中运行。
	- 在Spring中对于事务的传播行为定义了七种类型分别是：**REQUIRED、SUPPORTS、MANDATORY、REQUIRES_NEW、NOT_SUPPORTED、NEVER、NESTED**。
	- 可以根据是否支持当前事务来划分为三类：
		- ### 支持当前事务（和当前为同一事务）
			- **`REQUIRED`**，如果当前存在事务，则使用该事务。 如果当前没有事务，则创建一个新的事务。是 Spring 默认的事务传播机制。
			- **`SUPPORTS`**，如果当前存在事务，则使用该事务，如果当前不存在事务，则仍以非事务的方式运行。
			- **`MANDATORY`**，如果当前存在事务，则使用该事务，如果当前事务不存在则抛出异常。
		- ### 不支持当前事务
			- **`REQUIRED_NEW`**，总是创建一个新的事务，如果当前有事务，则将当前事务挂起。
				- 可以理解为设置事务传播类型为REQUIRES_NEW的方法，在执行时，不论当前是否存在事务，总是会新建一个事务。
			- **`NOT_SUPPORTED`**，始终以非事务的方式运行，如果当前有事务，则将当前事务挂起。
				- 可以理解为设置事务传播类型为NOT_SUPPORTED的方法，在执行时，不论当前是否存在事务，都会以非事务的方式运行。
			- **`NEVER`**，始终以非事务的方式运行，如果当前有事务，则抛出异常。
				- 很容易理解，就是我这个方法不使用事务，并且调用我的方法也不允许有事务，如果调用我的方法有事务则我直接抛出异常。
		- ### 嵌套事务
			- **`NESTED`**，当前存在事务，则创建一个嵌套事务来运行，如果当前没有事务，则和 `REQUIRED`一样开启一个事务运行。
				- 和REQUIRES_NEW的区别
				  > REQUIRES_NEW是新建一个事务并且新开启的这个事务与原有事务无关，而NESTED则是当前存在事务时（我们把当前事务称之为父事务）会开启一个嵌套事务（称之为一个子事务）。
				  在NESTED情况下父事务回滚时，子事务也会回滚，而在REQUIRES_NEW情况下，原有事务回滚，不会影响新开启的事务。
				- 和REQUIRED的区别
				  > REQUIRED情况下，调用方存在事务时，则被调用方和调用方使用同一事务，那么被调用方出现异常时，由于共用一个事务，所以无论调用方是否catch其异常，事务都会回滚
				  而在NESTED情况下，被调用方发生异常时，调用方可以catch其异常，这样只有子事务回滚，父事务不受影响
- ## 事务失效场景
	- 在类或者方法上添加了`@Transactional`注解，却发现方法并没有按事务处理。原因可能有以下这些：
		- ### 事务方法所在的类没有加载到Spring IOC容器中
			- Spring声明式事务的实现完全依赖于Spring的AOP代理机制，未被Spring管理的类中的方法不受Spring的AOP代理管理，因此，声明式事务失效。
		- ### 方法没有被 public 修饰
			- `@Transactional`注解只能作用于`public`修饰的方法上，因为使用 AOP 代理会检查目标方法是否被`public`修饰，如果没有则不会获取`@Transactional`的属性信息。
		- ### 同一个类中的方法调用
			- 内部调用时，没有使用 Spring 生成的代理对象调用的话，代理不会生效。解决方法有：
				- 引用自身 bean。
				  logseq.order-list-type:: number
				- 通过 ApplicationContext 引入 bean。
				  logseq.order-list-type:: number
					- ```java
					  public void A() {
					    ((UserServiceImpl) applicationContext.getBean("userServiceImpl")).B();
					  }
					  ```
				- 通过`AopContext`获取当前代理类，在启动类上添加注解`@EnableAspectJAutoProxy(exposeProxy = true)`，表示是否对外暴露代理对象，即是否可以获取AopContext。然后，在业务类上使用AopContext。
				  logseq.order-list-type:: number
					- ```java
					  public void A() {
					    ((UserServiceImpl) AopContext.currentProxy()).B();
					  }
					  ```
		- ### 方法的事务传播类型不支持
			- 若propagation属性设置如下三种事务传播行为，事务将不会发生回滚。
				- SUPPORTS：如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。
				- NOT_SUPPORTED：以非事务方式运行，如果当前存在事务，则把当前事务挂起。
				- NEVER：以非事务方式运行，如果当前存在事务，则抛出异常。
		- ### 不正确的捕获了异常
			- 使用了try-catch代码块将异常捕捉了，没有向上抛出异常，事务不会回滚。
		- ### 数据库不支持事务
		- ### 属性rollbackFor设置错误
			- `rollbackFor`可以指定能够触发事务回滚的异常类型。Spring默认抛出了未检查`unchecked`异常（继承自 `RuntimeException` 的异常）或者 `Error`才回滚事务。其他异常不会触发回滚事务。如果在事务中抛出其他类型的异常，但却期望 Spring 能够回滚事务，就需要设置 `rollbackFor`属性。
		- ### 方法被 final 修饰
			- 如果一个方法不想被子类重写，那么我们就可以把他写成final修饰的方法。如果事务方法使用final修饰，那么Spring AOP就**无法在代理类中重写该方法**，事务就不会生效。同样的，static修饰的方法也无法通过代理变成事务方法。
		- ### 未开启事务
			- 如果是SpringBoot项目，那么SpringBoot通过DataSourceTransactionManagerAutoConfiguration自动配置类帮我们开启了事务。如果是传统的Spring项目，则需要我们自己配置
		- ### 多线程调用
			- 两个操作在不同的线程时，连获取到的数据库连接不一样，从而是两个不同的事务，所以也不会回滚。
- ### 详细场景
	- [一文搞懂Spring事务传播机制](https://juejin.cn/post/6870790953922215950)
	- [带你读懂Spring 事务——事务的传播机制](https://zhuanlan.zhihu.com/p/148504094)