# 作用
	- ThreadLocal是一个将在**多线程中为每一个线程创建单独的变量副本的类**； 当使用ThreadLocal来维护变量时，ThreadLocal会为每个线程创建单独的变量副本，避免因多线程操作共享变量而导致的数据不一致的情况。
	- ThreadLocal变量的特点：
		- 每个线程内部都会有，在线程内部任何地方都可以使用
		- 线程之间互不影响。不存在线程安全问题，也不会影响程序执行性能
- # 创建
	- 1. 直接创建：`ThreadLocal myThreadLocal = new ThreadLocal()`;
	- 2. 创建泛型对象：`ThreadLocal myThreadLocal = new ThreadLocal<String>()`;
	- 3. 创建泛型对象并初始化值：
		- ```java
		  private ThreadLocal myThreadLocal = new ThreadLocal<String>() {
		    @Override
		    protected String initialValue() {
		        return "This is the initial value";
		    }
		  };
		  ```
	- 通过代码实例化了一个 ThreadLocal 对象。我们只需要实例化对象一次，并且也不需要知道它是被哪个线程实例化。虽然所有的线程都能访问到这个 ThreadLocal 实例，**但是每个线程却只能访问到自己通过调用 ThreadLocal 的 set() 方法设置的值**。即使是两个不同的线程在同一个 ThreadLocal 对象上设置了不同的值，他们仍然无法访问到对方的值。
- # 原理
	- 每个线程 Thread 类都有个属性 ThreadLocalMap，用来维护该线程的多个 ThreadLocal 变量，该 Map 是自定义实现的 Entry[] 数组结构，并非继承自原生 Map 类，**Entry 其中 Key 即是 ThreadLocal 变量本身的弱引用，Value 则是具体该线程中的变量副本值。**
	- 因此 ThreadLocal 其实只是个符号意义，本身不存储变量，仅仅是用来索引各个线程中的变量副本。
- ## 弱引用
	- 对对象进行弱引用不会影响垃圾回收器回收该对象，即如果一个对象只有弱引用存在了，则下次 GC 将会回收掉该对象（不管当前内存空间足够与否）。参见：[[Java 四种引用类型]]
	- 假如使用强引用，当 ThreadLocal 不再使用需要回收时，发现某个线程中 ThreadLocalMap 存在该 ThreadLocal 的强引用，无法回收，造成内存泄漏。因此，使用弱引用可以防止长期存在的线程（通常使用了线程池）导致ThreadLocal无法回收造成内存泄漏。