- 共有四种内部类：
	- 成员内部类
	- 静态内部类
	- 局部内部类
	- 匿名内部类
- # 成员内部类
- 成员内部类就像外部类的普通成员一样，可以访问外部类的属性及方法
- 成员内部类内部不允许存在任何静态变量或静态方法(`static`)；因**为成员内部类是属于对象的，而静态变量、静态方法会先于外部类的对象存在**，因此不允许成员内部类存在静态属性、方法
- 成员内部类如果需在外部类的外部使用，则需**通过调用外部类对象的普通方法创建，** 在没有外部类实例之前是无法创建内部类的
  
  ```java
  public class OutClass {
  
    public class InnerClass{}
    public InnerClass getInnerClass(){
        return new InnerClass();
    }
    class InnerClass{
        String innerName = "InnerClass";
        void test(){
        }
    }
  }
  //----调用外部类对象的普通方法获取内部类
  OutClass outClass = new OutClass();
  OutClass.InnerClass innerClass =  outClass.getInnerClass();
  ```
- # 静态内部类
- 用`static`修饰的内部类称之为静态内部类，静态内部类和非静态内部类之间存在一个最大的区别；非静态内部类在编译完成之后会隐含的保存着一个引用，该引用是指向创建它的外围类，但是静态类没有
- **静态内部类的创建不需要依赖外部类可以直接创建**
- **静态内部类不可以使用任何外部类的非**`static`**属性和方法**
- 静态内部类可以存在自己的成员变量包括非静态和静态属性和方法
  
  ```java
  public class OutClass {
    String name = "OutClass";
    static String nickName = "out";
    void hello(){}
    static void hi(){}
    static class StaticInnerClass{
        String innerName = "staticInnerClass";
        static String staticName = "staticName";
        
        void test(){
            System.out.println(innerName);
            System.out.println(nickName);
            System.out.println(staticName);
            hi();
        }
        static void staticTest(){ }
    }
  ```
- # 局部内部类
- 即方法内部类
- 方法内部类不允许使用访问权限修饰符；public、private、protected均不允许
- 方法内部类**对外部完全隐藏**，除了创建这个类的方法可以访问它以外，其他地方均不能访问
- 方法的访问区域范围就是方法内部类可以访问的区域范围
  
  ```java
  public class Test {
    public static void main(String[] args) {
        sayHello("shu");
    }
    static void  sayHello(String hello){
        class InnerClass {
            public void run(String name){
                System.out.println(name);
                System.out.println(hello);
            }
        }
        InnerClass innerClass = new InnerClass();
        innerClass.run("hello");
    }
  }
  ```
- # 匿名内部类
- 匿名内部类就是一个没有名字的方法内部类，因此特点和方法与方法内部类完全一致
- 匿名内部类必须继承一个抽象类或者实现一个接口
- 匿名内部类没有类名，因此没有构造方法
- 匿名内部类使得编码更加简洁
  
  ```java
  interface CInterface {  void test(); }
  class Outer{
    public void hello(CInterface cInterface) {
        cInterface.test();
    }
  }
  public class Test {
    public static void main(String[] args) {
        Outer outer = new Outer();
        outer.hello(new CInterface(){
            public void test(){
                System.out.println("test CInterface");
            }
        });
    }
  }   
  ```
  
  
  参考：[基础篇：JAVA内部类的使用介绍](https://segmentfault.com/a/1190000023832584)