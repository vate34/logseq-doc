- 1. 字符串进行比较时，使用 `equals`方法判断是字符串的内容是否相等；使用 `==`判断两个对象引用的地址是否相等，即判断是否为同一个对象。
- 2. java 为了避免产生大量的字符串对象，设计了一个字符串常量池。工作原理为： **创建一个字符串时，JVM首先为检查字符串常量池中是否有值相等的字符串，如果有，则不再创建，直接返回该字符串的引用地址，若没有，则创建，然后放到字符串常量池中，并返回新创建的字符串的引用地址。**
- 3. 对于使用`new String("str")`的方式创建对象的场景，会在堆上创建一个 `String`对象，并存储 `"str"`，再将这个对象的引用地址返回。（与常量池无关）
- 4. 有 `+`的情况：
	- 如果是字符串常量相加的情况`"A"+"B"`，会将`"A"`和`"B"`拼接得到`"AB"`，此时从常量池中查询是否存在这个字符串，如果有返回引用地址，否则创建这个常量
	- 如果相加的是字符串常量的引用，如`String s1 = "A", String s2 = "B"`，此时相加，虚拟机会创建一个`StringBuilder`对象，再使用`append`方法进行追加操作，最后使用`toString`方法转换为字符串对象。
	- 如果相加的对象为由`new`创建的字符串对象，虚拟机也是会看成一个`StringBuilder`进行`append`操作，最后执行`toString`。
	- 如果相加的对象分别为字符串常量和字符串对象，也是会看成StringBuilder进行操作。
	  
	  ```java
	  public class StringTest {
	  public static void main(String[] args){
	     String s1="Hello";
	     String s2="Hello";
	     String s3=new String("Hello");
	     String s4=new String("Hello");
	     System.out.println("s1和s2 引用地址是否相同："+(s1 == s2));
	     System.out.println("s1和s2 值是否相同："+s1.equals(s2));
	     System.out.println("s1和s3 引用地址是否相同："+(s1 == s3));
	     System.out.println("s1和s3 值是否相同："+s1.equals(s3));
	     System.out.println("s3和s4 引用地址是否相同："+(s3 == s4));
	  }
	  }
	  /**
	  s1和s2 引用地址是否相同：true //都是从常量池中获取的
	  s1和s2 值是否相同：true
	  s1和s3 引用地址是否相同：false // 一个是从常量池获取的，一个是在堆中创建的
	  s1和s3 值是否相同：true
	  s3和s4 引用地址是否相同： false // 在堆里创建的String对象，没有使用常量
	  **/
	  ```
	  
	  参考：
- [利用加号+连接字符串详解](https://www.cnblogs.com/duzhentong/p/7816566.html)
- [Java中String直接赋字符串和new String的一些问题](https://www.cnblogs.com/wkfvawl/p/11656137.html)