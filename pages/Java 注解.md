## 注解的作用
	- 1. 生成文档。通过代码标识的元数据生成 javadoc 文档。（`@Documented`）
	- 2. 编译检查。通过代码标识的元数据，让编译器在编译期间进行检查检验。（`@SuppressWarnings,` `@Deprecated` 和 `@Override`）
	- 3. 编译时动态处理。编译时通过代码里标识的元数据动态处理，如动态生成代码。
	- 4. 运行时动态处理。运行时通过代码里标识的元数据动态处理，例如利用反射来注入实例。
- ## 注解的分类
	- ### 1. Java自带的标准注解
		- 包括`@Override`、`@Deprecated`、`@SuppressWarnings`等，使用这些注解后编译器就会进行检查。
			- `@Deprecated`：所标注内容不再被建议使用；
			- `@Override`：只能标注方法，表示该方法覆盖父类中的方法；只在编译中生效
			- `@SuppressWarnings`：所标注内容产生的警告，编译器会对这些警告保持静默
	- ### 2. 元注解
		- 元注解是用于定义注解的注解，包括`@Retention`、`@Target`、`@Inherited`、`@Documented`、`@Repeatable` 等。元注解也是Java自带的标准注解，只不过用于修饰注解，比较特殊。
		- `@Retention`：注解的作用是描述注解起作用的时间范围
		  
		  ```java
		  public enum RetentionPolicy {
		      SOURCE,    // 源文件保留
		      CLASS,       // 编译期保留，默认值
		      RUNTIME   // 运行期保留，可通过反射去获取注解信息
		  }
		  ```
		- `@Inherited`：被它修饰的注解将具有继承性。如果某个类使用了被@Inherited修饰的注解，则其子类将自动具有该注解。
		- `@Target`：描述注解的使用范围，类型为以下变量：
		  
		  ```java
		  public enum ElementType { 
		      TYPE, // 类、接口、枚举类 
		      FIELD, // 成员变量（包括：枚举常量） 
		      METHOD, // 成员方法 
		      PARAMETER, // 方法参数 
		      CONSTRUCTOR, // 构造方法 
		      LOCAL_VARIABLE, // 局部变量 
		      ANNOTATION_TYPE, // 注解类 
		      PACKAGE, // 可用于修饰：包 
		      TYPE_PARAMETER, // 类型参数，JDK 1.8 新增 
		      TYPE_USE // 使用类型的任何地方，JDK 1.8 新增 
		   }
		  ```
		- `@Documented`：描述在使用 javadoc 工具为类生成帮助文档时，是否要保留其注解信息
		- `@Repeatable`：重复注解，Java8新加入的。允许在同一申明类型(类，属性，或方法)的多次使用同一个注解
		  
		  ```java
		  @Repeatable(Authorities.class)
		  public @interface Authority {
		       String role();
		  }
		  
		  public @interface Authorities {
		      Authority[] value();
		  }
		  
		  public class RepeatAnnotationUseNewVersion {
		      @Authority(role="Admin")
		      @Authority(role="Manager")
		      public void doSomeThing(){ }
		  }
		  ```
		- `@Native`：使用 @Native 注解修饰成员变量，则表示这个变量可以被本地代码引用，常常被代码生成工具使用。对于 @Native 注解不常使用，了解即可
		- `@FunctionalInterface`：标识一个匿名函数或者函数式接口。
	- ### 3. 自定义注解
		- 自定义的注解，可以使用元注解进行注解。使用 @interface 来定义一个注解，可以使用元注解来修饰。
		  
		  ```java
		  @Retention(RetentionPolicy.RUNTIME)
		  @Target(ElementType.TYPE)
		  public @interface MyTestAnnotation {
		  
		  }
		  ```
- ## 注解支持继承吗
	- 不支持。不能使用关键字 extends 来继承某个@interface，但注解在编译后，编译器会自动继承java.lang.annotation.Annotation接口.
- ## 注解的原理
	- 使用反射来获取数据。
	- > 注解本质是一个继承了Annotation 的特殊接口，其具体实现类是Java 运行时生成的动态代理类。
	  > 而我们通过反射获取注解时，返回的是Java 运行时生成的动态代理对象$Proxy1。通过代理对象调用自定义注解（接口）的方法，会最终调用AnnotationInvocationHandler 的invoke 方法。该方法会从memberValues 这个Map 中索引出对应的值。而memberValues 的来源是Java 常量池。