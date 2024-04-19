- SpringBoot Starter生效的原理
	- 1. pom.xml 文件中指定了所需 jar 包的包名与版本号
	- 2. resource/META-INF下的 spring.factories 文件中声明所需的配置类
	- 3. 编写自动配置（AutoConfigure）类，里面会设定相关的属性配置信息
- # 命名
	- 官方的 starter 的命名格式为 `spring-boot-starter-{name}`，非官方的 starter 的命名格式为 `{name}-spring-boot-starter`
- # 引入自动配置相关依赖
	- ```xml
	  <dependency>
	    <groupId>org.springframework.boot</groupId>
	    <artifactId>spring-boot-configuration-processor</artifactId>
	  </dependency>
	  <dependency>
	    <groupId>org.springframework.boot</groupId>
	    <artifactId>spring-boot-autoconfigure</artifactId>
	  </dependency>
	  ```
	- 以及其他相关依赖
- # 创建 spring.factories 文件
	- 在 resource/META-INF 目录下创建名称为 spring.factories 的文件
	- 因为当 Spring Boot 启动的时候，会在 classpath 下寻找所有名称为 `spring.factories` 的文件，然后运行里面的配置指定的自动加载类，将指定类(一个或多个)中的相关 bean 初始化。
- # 编写自动配置类
	- ```java
	  @Configuration
	  @ConditionalOnClass(KiteService.class)
	  @EnableConfigurationProperties(KiteProperties.class)
	  @Slf4j
	  public class KiteAutoConfigure {
	  
	    @Autowired
	    private KiteProperties kiteProperties;
	  
	    @Bean
	    @ConditionalOnMissingBean(KiteService.class)
	    @ConditionalOnProperty(prefix = "kite.example",value = "enabled", havingValue = "true")
	    KiteService kiteService(){
	        return new KiteService(kiteProperties);
	    }
	  }
	  ```
	- 1. `@Configuration`：声明当前是自动配置类
	- 2. `@ConditionalOnClass(KiteService.class)`：只有在 classpath 中找到 KiteService 类的情况下，才会解析此自动配置类，否则不解析
	- 3. `@EnableConfigurationProperties(KiteProperties.class)`：启用配置类
	- 4. `@Bean`：实例化一个 bean
	- 5. `@ConditionalOnMissingBean(KiteService.class)`：与 @Bean 配合使用，只有在当前上下文中不存在某个 bean 的情况下才会执行所注解的代码块，也就是当前上下文还没有 KiteService 的 bean 实例的情况下，才会执行 kiteService() 方法，从而实例化一个 bean 实例出来。
	- 6. `@ConditionalOnProperty`：当应用配置文件中有相关的配置才会执行其所注解的代码块。例如上边的需要配置文件有 kite.example.enabled=true才能生效
- # 实现属性配置类
	- ```java
	  @Data
	  @ConfigurationProperties("kite.example")
	  public class KiteProperties {
	    private String host;
	    private int port;
	  }
	  ```
	- 使用的时候在配置文件中声明其属性值
- # 实现相关功能
	- 相关业务功能可以放在单独的 jar 包中
	- ```java
	  @Slf4j
	  public class KiteService {
	    private String host;
	    private int port;
	    public KiteService(KiteProperties kiteProperties){
	        this.host = kiteProperties.getHost();
	        this.port = kiteProperties.getPort();
	    }
	    public void print(){
	        log.info(this.host + ":" +this.port);
	    }
	  }
	  ```
-