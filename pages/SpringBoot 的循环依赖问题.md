- #SpringBoot
- # 循环依赖
- 两个 Bean 循环依赖，以及间接依赖导致的循环依赖。SpringBoot （从2.6版本开始）启动时将抛出异常 BeanCurrentlyInCreationException。field 注入和构造器注入都会导致这个问题。
- # 解决
	- 1. 重新设计，解耦
	- 2.使用 @Lazy 注解在需要的 Bean 参数上或者构造器上
	- 3. 使用 Setter 注入，当依赖被最终使用的时候才进行注入
	- 4. 使用 @PostContrust
		- ```java
		  @Component
		  public class ServiceA {
		    @Autowired
		    private ServiceB serviceB;
		    @PostConstruct
		    public void init() {
		        serviceB.setServiceA(this);
		    }
		    public ServiceB getServiceB() {
		        return serviceB;
		    }
		  }
		  ```
	- 5. 实现 ApplicationContextAware 与 InitializingBean
-