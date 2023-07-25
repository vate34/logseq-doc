# 什么是 cglib

1. Cglib 是一个强大的、高性能的 **代码生成包**，提供方法的拦截。
2. 其使用轻量级框架 ASM 操作字节码
3. CGLIB相比于JDK动态代理更加强大，JDK动态代理虽然简单易用，但是其有一个致命缺陷是，只能对接口进行代理
- # 基本使用
- 1. 定义一个需要被代理的类：
  
  ```java
  public class Dao {
    public void update() {
        System.out.println("PeopleDao.update()");
    }
    public void select() {
        System.out.println("PeopleDao.select()");
    }
  }
  ```
  2. 创建一个 Dao 代理，实现 MethodInterceptor 接口，目标是在 update() 方法与 select() 方法调用前后输出两句话：
  
  ```java
  public class DaoProxy implements MethodInterceptor {
    @Override
    public Object intercept(Object object, Method method, Object[] objects, MethodProxy proxy) throws Throwable {
        System.out.println("Before Method Invoke");
        proxy.invokeSuper(object, objects);
        System.out.println("After Method Invoke");
        return object;
    }
  }
  ```
- 相关参数的含义：
	- Object 表示要进行增强的对象
	- Method 表示拦截的方法
	- Object[] 数组表示参数列表，基本数据类型需要传入其包装类型，如 int-->Integer、long-Long、double-->Double
	- MethodProxy 表示对方法的代理，invokeSuper 方法表示对被代理对象方法的调用
- 3. 使用
  这是使用Cglib的通用写法，setSuperclass表示设置要代理的类，setCallback表示设置回调即MethodInterceptor的实现类，使用create()方法生成一个代理对象，注意要强转一下，因为返回的是Object。
  
  ```java
  public class CglibTest {
    @Test
    public void testCglib() {
        DaoProxy daoProxy = new DaoProxy();
        Enhancer enhancer = new Enhancer();
        enhancer.setSuperclass(Dao.class);
        enhancer.setCallback(daoProxy);
        Dao dao = (Dao)enhancer.create();
        dao.update();
        dao.select();
    }
  }
  ```