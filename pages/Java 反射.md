# 获取到反射的 Class 对象
	- 1. 使用 Class.forName 静态方法获取。当你知道该类的全路径名时，你可以使用该方法获取 Class 类对象。
	- ```java
	  Class clz = Class.forName("java.lang.String");
	  ```
	- 2. 使用 .class 方法，适用于编译前已经知道操作的类。
	- ```java
	  Class clz = String.class;
	  ```
	- 3. 使用类对象的 getClass() 方法
	- ```java
	  String str = new String("Hello");
	  Class clz = str.getClass();
	  ```
	- 这些方式获取到的 Class 对象是同一个，因为 java 运行的时候每个类只会生成一个 Class 对象
- # 获取成员变量
	- 1. getFields 获取公有字段
	- 2. getDeclaredFields 获取所有声明的字段，包括公有字段和私有字段。如果要修改字段则需要调用setAccessible(true) 方法
	- 3. 也可以通过 Field getField(name) 和 Field getDeclaredField(name) 获取指定的字段
- # 获取构造方法
	- 1. getConstructors 获取公有的构造方法
	- 2. getDeclaredConstructors 获取所有的构造方法。调用私有构造方法则需要调用setAccessible(true) 方法
	- ```java
	  // 1.获取所有声明的构造方法 
	  Constructor[] declaredConstructorList = studentClass.getDeclaredConstructors(); 
	  for (Constructor declaredConstructor : declaredConstructorList) {
	  System.out.println("declared Constructor: " + declaredConstructor); 
	  } 
	  // 2.获取所有公有的构造方法 
	  Constructor[] constructorList = studentClass.getConstructors(); 
	  for (Constructor constructor : constructorList) {
	  System.out.println("constructor: " + constructor); 
	  }
	  ```
- # 获取非构造方法
	- 1. getMethods 获取公有非构造方法
	- 2. getDeclaredMethods 获取所有的非构造方法。调用私有方法则需要调用setAccessible(true) 方法
- # 实践
  
  ```java
  // 1.通过字符串获取Class对象，这个字符串必须带上完整路径名 
  Class studentClass = Class.forName("com.test.reflection.Student"); 
  // 2.获取声明的构造方法，传入所需参数的类名，如果有多个参数，用','连接即可
  Constructor studentConstructor = studentClass.getDeclaredConstructor(String.class); 
  // 如果是私有的构造方法，需要调用下面这一行代码使其可使用，公有的构造方法则不需要下面这一行代码 
  studentConstructor.setAccessible(true); 
  // 使用构造方法的newInstance方法创建对象，传入构造方法所需参数，如果有多个参数，用','连接即可 Object 
  student = studentConstructor.newInstance("NameA"); 
  // 3.获取声明的字段，传入字段名 
  Field studentAgeField = studentClass.getDeclaredField("studentAge"); 
  // 如果是私有的字段，需要调用下面这一行代码使其可使用，公有的字段则不需要下面这一行代码 
  // studentAgeField.setAccessible(true); 
  // 使用字段的set方法设置字段值，传入此对象以及参数值 
  studentAgeField.set(student,10); 
  // 4.获取声明的函数，传入所需参数的类名，如果有多个参数，用','连接即可 
  Method studentShowMethod = studentClass.getDeclaredMethod("show",String.class); 
  // 如果是私有的函数，需要调用下面这一行代码使其可使用，公有的函数则不需要下面这一行代码 
  studentShowMethod.setAccessible(true); 
  // 使用函数的invoke方法调用此函数，传入此对象以及函数所需参数，如果有多个参数，用','连接即可。函数会返回一个Object对象，使用强制类型转换转成实际类型即可 
  Object result = studentShowMethod.invoke(student,"message"); 
  System.out.println("result: " + result);
  ```
-