- 在Spring框架进行bean对象依赖注入时，@Autowired利用可以对成员变量、方法和构造函数进行标注，来完成自动装配的工作。
- @Autowired可标注在成员变量，也可以标注在成员变量的set方法上，以及类的构造函数上。
- ## 三种方式
- 1. 直接标注成员变量
- ```java
  @Autowired
  UserDao userDao;
  ```
  2. setter注入
  ```java
  @Autowired
  void setUserDao(UserDao userDao){
    this.userDao = userDao;
  }
  ```
  3. 构造函数注入
  ```java
  @Autowired
  Service(UserDao userDao){
    this.userDao = userDao;
  }
  ```
- ## List类型自动注入
- 当同一种bean有不同实现时：
- ```java
  public interface Converter {}
  public class BookConverter implements Converter {
  	}
  public class CustomerConverter implements Converter {
  	}
  public class AuthorConverter implements Converter {
  	}
  ```
- 在需要注入以上3个bean的类中声明`List<Converter> converters;`Spring会自动从容器中取出这三个相同类型的bean装配到List类型的converters中，从而简化了依赖注入的过程。
- ## Map类型自动注入
- 在需要注入以上3个bean的类中声明
- `Map<String,Converter> converters;`
- 此时，Spring会自动从容器中取出这三个相同类型的bean以及bean的名称，注入到Map类型的converters中，此时该map得key对应为bean的名称，value则为对应bean对象。
- 程序中可使用如下方式获取对象bean
- `converter.get("BookConverter”)`