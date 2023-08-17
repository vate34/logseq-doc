- #多线程
- #设计模式
- 所谓单例，就是整个程序有且仅有一个实例。该类负责创建自己的对象，同时确保只有一个对象被创建。 在Java中，一般常用在工具类的实现或创建对象需要消耗大量资源时。
- 特点：
	- 构造器私有
	- 持有自行类型的属性
	- 拥有对外提供获取实例的静态方法
- # 懒汉模式
- 在请求获取实例时，生成实例并返回，即延迟初始化。
- 线程不安全，在高并发时可能产生多个实例，严格意义上来说不是单例模式
- ```java
  public class Singleton {
    private static Singleton instance;
    private Singleton Singleton(){}
    public static Singleton getInstance() {
      if(instance == null) {
        instance = new Singleton();
      }
      return instance;
    }
  }
  ```
- # 饿汉模式
- 在初始化类对象的时候就生成了实例，线程安全，但容易产生垃圾
- ```java
  public class Singleton {
    private static Singleton instance = new Singleton();
    private Singleton() {};
    public static Singleton getInstance() {
      return instance;
    }
  }
  ```
- # 双重校验锁模式
- 即DCL，即 double-checked locking。使用`synchronized`和`volatile`，以及两次检查来保证线程安全，且能够保证高性能。
	- 双重检查，第一次是为了避免不必要的实例；第二次是为了进行同步，避免多线程问题。
	- 由于`instance = new Singleton()`对象的创建在JVM中可能会进行重排序， 在多线程访问下存在风险，使用`volatile`修饰`instance`实例变量，解决该问题。
- ```java
  public class Singleton {
    private static volatile Singleton instance;
    private Singleton() {};
    public static Singleton getInstance() {
      // 第一个判断，减少不必要的同步，优化性能
      if(instance == null)
        // 加锁同步，保证线程安全
        synchronized(Singleton.class) {
        	// 第二次判断，同步，避免多线程问题（创建多个）
        	if (instance == null) {
            // instance 变量被volatile，下面的新建对象操作不会被重排序
            instance = new Singleton();
          }
      }
      return instance;
    }
  }
  ```
- # 静态内部类模式
- 利用由JVM实现的类对象的初始化锁：一个类的静态变量、静态初始化块，全局加载且仅加载一次，时间点在类被初始化时。优点：
	- 代码简洁
	- 延迟初始化
	- 线程安全
- ```java
  public class Singleton {
    private Singleton() {};
    public static Singleton getInstance() {
      return Inner.instance;
    }
    private static class Inner {
      private static final Singleton instance = new Singleton();
    }
  }
  ```
- 类的加载顺序参加：[[Java 类的加载顺序]]
- # 枚举单例模式
- ```java
  public enum Singleton  {
    INSTANCE
    //doSomething 该实例支持的行为
    //可以省略此方法，通过Singleton.INSTANCE进行操作
    public static Singleton get Instance() {
        return Singleton.INSTANCE;
    }
  }
  ```
- # Spring 中的单例模式
- spring的对象默认是单例的，其实现方法为**双重校验锁模式**。
- ```java
      @Nullable
      protected Object getSingleton(String beanName, boolean allowEarlyReference) {
          Object singletonObject = this.singletonObjects.get(beanName);
          if (singletonObject == null && isSingletonCurrentlyInCreation(beanName)) {
              synchronized (this.singletonObjects) {
                  singletonObject = this.earlySingletonObjects.get(beanName);
                  if (singletonObject == null && allowEarlyReference) {
                      ObjectFactory<?> singletonFactory = this.singletonFactories.get(beanName);
                      if (singletonFactory != null) {
                          singletonObject = singletonFactory.getObject();
                          this.earlySingletonObjects.put(beanName, singletonObject);
                          this.singletonFactories.remove(beanName);
                      }
                  }
              }
          }
          return singletonObject;
      }
  ```
- # 破坏单例模式的场景
- 反序列化。解决方式是实现`readResolve`方法，返回实例的引用，无视掉反序列化的值。
  logseq.order-list-type:: number
- 反射。解决方式可以是，在构造函数中对实例化次数进行统计，大于一次就抛出异常。
  logseq.order-list-type:: number
- ```java
  public class Singleton {
      private static int count = 0;
      private static Singleton instance = null;
      private Singleton(){
          synchronized (Singleton.class) {
              if(count > 0){
                  throw new RuntimeException("创建了两个实例");
              }
              count++;
          }
      }
      public static Singleton getInstance() {
          if(instance == null) {
              instance = new Singleton();
          }
          return instance;
      }
  }
  ```