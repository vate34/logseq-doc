# 泛型方法

定义在方法上，格式：

```java
public <泛型类型> 返回类型 方法名（泛型类型 变量名） {}
```

方法声明中定义的形参只能在该方法里使用，而接口、类声明中定义的类型形参则可以在整个接口、类中使用。
# 泛型接口

定义在接口上，格式：

```java
public interface 接口名<泛型类型> {}
```

示例：

```java
/**
* 泛型接口的定义格式:        修饰符  interface 接口名<数据类型> {}
*/
public interface Inter<T> {
  public abstract void show(T t) ;
}
/**
* 实现是泛型类
*/
public class InterImpl<E> implements Inter<E> {
  @Override
  public void show(E t) {
      System.out.println(t);
  }
}
Inter<String> inter = new InterImpl<String>() ;
inter.show("hello") ;
```
# 泛型类

定义在类上，格式：

```java
public class 类名 <泛型类型1,...> {}
```
- ## 泛型类派生
- 泛型类派生子类时，不能包含类型参数，需要传入具体的类型。（泛型接口实现不需要）
- 错误示例：
	- `public class A extends Container<K, V> {}`
- 正确示例：
	- `public class A extends Container<Integer, String> {}`
- 也可以不指定具体的类型，系统会把参数当作 Object 类型处理
	- `public class A extends Container {}`
# 高级通配符
- ## 上界通配符
- <? extends T>表示的是类型的上界【包含自身】，通配的参数化类型，只能够是 T 或 T 的子类
	- `List<? extends Animal> list = new ArrayList<Cat>();`
- ## 下界通配符
- <? super T>表示的是类型的下界，参数化的类型，只能够是 T 或 T 的超类，层层至上，直至 Object
	- List<? super Cat> list = new ArrayList<Animal>();`
- ## 无界通配符
- <?> ,任意类型，没有明确，起到占位符的作用
- # 泛型擦除
  
  编译器编译带泛型类型说明的集合时，会去掉类型信息。
  
  ```java
  public class GenericTest {
    public static void main(String[] args) {
        new GenericTest().testType();
    }
  
    public void testType(){
        ArrayList<Integer> collection1 = new ArrayList<Integer>();
        ArrayList<String> collection2= new ArrayList<String>();
        
        System.out.println(collection1.getClass()==collection2.getClass());
        //两者class类型一样,即字节码一致
        
        System.out.println(collection2.getClass().getName());
        //class均为java.util.ArrayList,并无实际类型参数信息
    }
  }
  ```
- 这是因为不管为泛型的类型形参传入哪一种类型实参，对于Java来说，它们依然被当成**同一类处理**，在内存中也只占用一块内存空间。从Java泛型这一概念提出的目的来看，其只是作用于代码编译阶段，在编译过程中，对于正确检验泛型结果后，会将泛型的相关信息擦出，也就是说，成功编译过后的class文件中是不包含任何泛型信息的。泛型信息不会进入到运行时阶段。
- > 在静态方法、静态初始化块或者静态变量的声明和初始化中不允许使用类型形参。由于系统中并不会真正生成泛型类，所以instanceof运算符后不能使用泛型类
- # 泛型与反射
- 1. 把泛型变量当成方法的参数，利用Method类的getGenericParameterTypes方法来获取泛型的实际类型参数。示例：
  
  ```java
  public class GenericTest {
  
    public static void main(String[] args) throws Exception {
        getParamType();
    }
    
     /*利用反射获取方法参数的实际参数类型*/
    public static void getParamType() throws NoSuchMethodException{
        Method method = GenericTest.class.getMethod("applyMap",Map.class);
        //获取方法的泛型参数的类型
        Type[] types = method.getGenericParameterTypes();
        System.out.println(types[0]);
        //参数化的类型
        ParameterizedType pType  = (ParameterizedType)types[0];
        //原始类型
        System.out.println(pType.getRawType());
        //实际类型参数
        System.out.println(pType.getActualTypeArguments()[0]);
        System.out.println(pType.getActualTypeArguments()[1]);
    }
  
    /*供测试参数类型的方法*/
    public static void applyMap(Map<Integer,String> map){
  
    }
  }
  ```
  
  输出：
  
  ```shell
  java.util.Map<java.lang.Integer, java.lang.String>
  interface java.util.Map
  class java.lang.Integer
  class java.lang.String
  ```
  2. 通过反射绕开编译器对泛型的类型限制。示例：
  
  ```java
  public static void main(String[] args) throws Exception {
  //定义一个包含int的链表
  ArrayList<Integer> al = new ArrayList<Integer>();
  al.add(1);
  al.add(2);
  //获取链表的add方法，注意这里是Object.class，如果写int.class会抛出NoSuchMethodException异常
  Method m = al.getClass().getMethod("add", Object.class);
  //调用反射中的add方法加入一个string类型的元素，因为add方法的实际参数是Object
  m.invoke(al, "hello");
  System.out.println(al.get(2));
  }
  ```
# 泛型的限制
## 模糊性错误

对于泛型类User<K,V>而言，声明了两个泛型类参数。在类中根据不同的类型参数重载show方法。

```other
public class User<K, V> {
  public void show(K k) { // 报错信息：'show(K)' clashes with 'show(V)'; both methods have same erasure
  }
  public void show(V t) {
  }
}
```

由于泛型擦除，二者本质上都是Obejct类型。方法是一样的，所以编译器会报错。
## 不能够实例化参数

编译器也不知道该实例化哪种参数
## 对静态成员的限制

静态方法无法访问类上定义的泛型；**如果静态方法操作的类型不确定，必须要将泛型定义在方法上**。

```java
public class User<T> {
  //错误
  private static T t;
  //错误
  public static T getT() {
      return t;
  }
  //正确
  public static <K> void test(K k) {
  }
}
```
## 对泛型数组的限制

不能实例化元素类型为类型参数的数组，但是可以将数组指向类型兼容的数组的引用

```java
public class User<T> {
  private T[] values;
  public User(T[] values) {
      //错误，不能实例化元素类型为类型参数的数组
      this.values = new T[5];
      //正确，可以将values 指向类型兼容的数组的引用
      this.values = values;
  }
}
```
## 对泛型异常的限制

泛型类不能扩展 Throwable，意味着不能创建泛型异常类