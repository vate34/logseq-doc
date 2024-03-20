# 静态代理
	- 创建一个接口，然后创建被代理的类实现该接口并且实现该接口中的抽象方法。之后再创建一个代理类，**同时使其也实现这个接口**。在代理类中持有一个被代理对象的引用（聚合方式），而后在代理类方法中调用该对象的方法。
	- 缺点：由于代理只能为一个类服务，如果需要代理的类很多，那么就需要编写大量的代理类，比较繁琐。
	  
	  ```java
	  // 接口
	  public interface HelloInterface {
	    void sayHello();
	  }
	  
	  // 被代理类
	  public class Hello implements HelloInterface{
	    @Override
	    public void sayHello() {
	        System.out.println("Hello zhanghao!");
	    }
	  }
	  
	  // 代理类
	  public class HelloProxy implements HelloInterface{
	    private HelloInterface helloInterface = new Hello();
	    @Override
	    public void sayHello() {
	        System.out.println("Before invoke sayHello" );
	        helloInterface.sayHello();
	        System.out.println("After invoke sayHello");
	    }
	  }
	  
	  // 调用
	  public static void main(String[] args) {
	        HelloProxy helloProxy = new HelloProxy();
	        helloProxy.sayHello();
	  }
	  ```
- # 动态代理
	- 利用反射机制在运行时创建代理类，要求代理目标必须实现接口。接口、被代理类不变，构建一个 handler 类来实现 InvocationHandler 接口。
	  
	  ```java
	  public class ProxyHandler implements InvocationHandler{
	    private Object object;
	    public ProxyHandler(Object object){
	        this.object = object;
	    }
	    @Override
	    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
	        System.out.println("Before invoke "  + method.getName());
	        method.invoke(object, args);
	        System.out.println("After invoke " + method.getName());
	        return null;
	    }
	  }
	  ```
	- **通过 Proxy 类的静态方法 newProxyInstance 返回一个接口的代理实例**。执行动态代理：
	- ```java
	  public static void main(String[] args) {     
	  	System.getProperties().setProperty("sun.misc.ProxyGenerator.saveGeneratedFiles", "true");
	  	HelloInterface hello = new Hello();
	  	InvocationHandler handler = new ProxyHandler(hello);
	  	HelloInterface proxyHello = (HelloInterface) Proxy.newProxyInstance(hello.getClass().getClassLoader(), hello.getClass().getInterfaces(), handler);
	  	proxyHello.sayHello();
	  }
	  ```
	- ## 动态代理实现步骤
		- 1. 委托类
			- 委托类必须实现自某一接口
		- 2. 中介类
			- 中介类必须实现InvocationHandler接口。中介类持有一个委托类对象引用，在 invoke 方法中调用了委托类对象的相应方法，可以看出**中介类与委托类构成了静态代理关系，在这个关系中，中介类是代理类，委托类就是委托类**
		- 3. 动态生成代理类
			- **调用 Proxy 类的 newProxyInstance 方法来获取一个代理类实例**。这个代理类实现了我们指定的接口并且会把方法调用分发到指定的调用处理器。
		- ```java
		  public static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h) throws IllegalArgumentException
		  ```
		- 三个参数：
			- loader：定义类的ClassLoder
			- interfaces：代理类的实现接口
			- h：调用处理器
	- ## 实际调用过程
		- **首先通过 newProxyInstance 方法获取代理类实例，而后我们便可以通过这个代理类实例调用代理类的方法，对代理类的方法的调用实际上都会调用中介类 (调用处理器) 的 invoke 方法，在 invoke 方法中我们调用委托类的相应方法，并且可以添加自己的处理逻辑。**
	- Java 动态代理其实内部是通过 Java 反射机制来实现的。
- ### CGLIB动态代理
	- [[CGLIB]]
	- CGLIB使用ASM机制实现动态代理，不需要被代理类实现接口。代理类要实现`MethodInterceptor` ，用于方法的拦截；创建代理类实例的`Enhancer` 对象要继承具体的代理对象类，CGLIB是通过继承实现的。
	- 示例：
	  ```java
	  import java.lang.reflect.Method;
	  import java.util.Date;
	  
	  public class LogInterceptor implements MethodInterceptor {
	    /**
	     * @param object 表示要进行增强的对象
	     * @param method 表示拦截的方法
	     * @param objects 数组表示参数列表，基本数据类型需要传入其包装类型，如int-->Integer、long-Long、double-->Double
	     * @param methodProxy 表示对方法的代理，invokeSuper方法表示对被代理对象方法的调用
	     * @return 执行结果
	     * @throws Throwable
	     */
	    @Override
	    public Object intercept(Object object, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
	        before();
	        Object result = methodProxy.invokeSuper(object, objects);   // 注意这里是调用 invokeSuper 而不是 invoke，否则死循环，methodProxy.invokesuper执行的是原始类的方法，method.invoke执行的是子类的方法
	        after();
	        return result;
	    }
	    private void before() {
	        System.out.println(String.format("log start time [%s] ", new Date()));
	    }
	    private void after() {
	        System.out.println(String.format("log end time [%s] ", new Date()));
	    }
	  }
	  import net.sf.cglib.proxy.Enhancer;
	  
	  public class CglibTest {
	    public static void main(String[] args) {
	        LogInterceptor daoProxy = new LogInterceptor(); 
	        Enhancer enhancer = new Enhancer();
	        enhancer.setSuperclass(Dao.class);  // 设置超类，cglib是通过继承来实现的
	        enhancer.setCallback(daoProxy);
	        // 可以设置多个拦截器  
	        // enhancer.setCallbacks(new Callback[]{logInterceptor, logInterceptor2, NoOp.INSTANCE});
	        // 也可以设置回调过滤器
	        // enhancer.setCallbackFilter(new DaoFilter());
	  
	        Dao dao = (Dao)enhancer.create();   // 创建代理类
	        dao.update();
	        dao.select();
	    }
	  }
	  ```
	- CGLIB 创建动态代理类的模式是：
		- 1. 查找目标类上的所有非final 的public类型的方法定义；
		- 2. 将这些方法的定义转换成字节码；
		- 3. 将组成的字节码转换成相应的代理的class对象；
		- 4. 实现 MethodInterceptor接口，用来处理对代理类上所有方法的请求
- # JDK动态代理与CGLIB动态代理对比
- JDK动态代理：基于Java反射机制实现，必须要实现了接口的业务类才能用这种办法生成代理对象。缺点是业务类必须实现自接口。
- CGLIB动态代理：基于ASM机制实现，通过生成业务类的子类作为代理类。缺点是对于final方法无法代理。