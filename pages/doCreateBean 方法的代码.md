- ```java
   protected Object doCreateBean(String beanName, RootBeanDefinition mbd, @Nullable Object[] args) throws BeanCreationException {
          // 1、创建一个对bean原始对象的包装对象-BeanWrapper，执行createBeanInstance，即构造方法或工厂方法，给BeanWrapper赋值
          BeanWrapper instanceWrapper = null;
          if (mbd.isSingleton()) {
              instanceWrapper = (BeanWrapper)this.factoryBeanInstanceCache.remove(beanName);
          }
          if (instanceWrapper == null) {
              instanceWrapper = this.createBeanInstance(beanName, mbd, args);
          }
  
          Object bean = instanceWrapper.getWrappedInstance();
          Class<?> beanType = instanceWrapper.getWrappedClass();
          if (beanType != NullBean.class) {
              mbd.resolvedTargetType = beanType;
          }
          // 2、允许其他修改beanDefinition，如使用Annotation增强Bean定义等，这通过类MergedBeanDefinitionPostProcessor来完成
          synchronized(mbd.postProcessingLock) {
              if (!mbd.postProcessed) {
                  try {
                      this.applyMergedBeanDefinitionPostProcessors(mbd, beanType, beanName);
                  } catch (Throwable var17) {
                      throw new BeanCreationException(mbd.getResourceDescription(), beanName, "Post-processing of merged bean definition failed", var17);
                  }
  
                  mbd.postProcessed = true;
              }
          }
          // 3、将当前bean 的 ObjetFactory放入singletonFactories中， 
          boolean earlySingletonExposure = mbd.isSingleton() && this.allowCircularReferences && this.isSingletonCurrentlyInCreation(beanName);
          if (earlySingletonExposure) {
              if (this.logger.isTraceEnabled()) {
                  this.logger.trace("Eagerly caching bean '" + beanName + "' to allow for resolving potential circular references");
              }
  
              this.addSingletonFactory(beanName, () -> {
                  return this.getEarlyBeanReference(beanName, mbd, bean);
              });
          }
  
          Object exposedObject = bean;
          // 4、执行 populateBean，设置属性值
          // 5、执行 initializeBean，调用 Bean的初始化方法
          try {
              this.populateBean(beanName, mbd, instanceWrapper);
              exposedObject = this.initializeBean(beanName, exposedObject, mbd);
          } catch (Throwable var18) {
              if (var18 instanceof BeanCreationException && beanName.equals(((BeanCreationException)var18).getBeanName())) {
                  throw (BeanCreationException)var18;
              }
  
              throw new BeanCreationException(mbd.getResourceDescription(), beanName, "Initialization of bean failed", var18);
          }
          // 6、再次处理循环依赖问题
          if (earlySingletonExposure) {
              Object earlySingletonReference = this.getSingleton(beanName, false);
              if (earlySingletonReference != null) {
                  if (exposedObject == bean) {
                      exposedObject = earlySingletonReference;
                  } else if (!this.allowRawInjectionDespiteWrapping && this.hasDependentBean(beanName)) {
                      String[] dependentBeans = this.getDependentBeans(beanName);
                      Set<String> actualDependentBeans = new LinkedHashSet(dependentBeans.length);
                      String[] var12 = dependentBeans;
                      int var13 = dependentBeans.length;
  
                      for(int var14 = 0; var14 < var13; ++var14) {
                          String dependentBean = var12[var14];
                          if (!this.removeSingletonIfCreatedForTypeCheckOnly(dependentBean)) {
                              actualDependentBeans.add(dependentBean);
                          }
                      }
  
                      if (!actualDependentBeans.isEmpty()) {
                          throw new BeanCurrentlyInCreationException(beanName, "Bean with name '" + beanName + "' has been injected into other beans [" + StringUtils.collectionToCommaDelimitedString(actualDependentBeans) + "] in its raw version as part of a circular reference, but has eventually been wrapped. This means that said other beans do not use the final version of the bean. This is often the result of over-eager type matching - consider using 'getBeanNamesForType' with the 'allowEagerInit' flag turned off, for example.");
                      }
                  }
              }
          }
          // 7、注册bean的销毁回调方法，在beanFactory中注册销毁通知，以便在容器销毁时，能够做一些后续处理工作
          try {
              this.registerDisposableBeanIfNecessary(beanName, bean, mbd);
              return exposedObject;
          } catch (BeanDefinitionValidationException var16) {
              throw new BeanCreationException(mbd.getResourceDescription(), beanName, "Invalid destruction signature", var16);
          }
      }
  ```
- BeanWrapper接口，作为spring内部的一个核心接口，正如其名，它是bean的包裹类，即在内部中将会保存该bean的实例，提供其它一些扩展功能。同时，BeanWrapper接口还继承了PropertyAccessor, propertyEditorRegistry, TypeConverter、ConfigurablePropertyAccessor接口，所以它还提供了访问bean的属性值、属性编辑器注册、类型转换等功能。