- ```java
  Object sharedInstance = getSingleton(beanName);
  
  public Object getSingleton(String beanName) {
      return getSingleton(beanName, true);
  }
  
  protected Object getSingleton(String beanName, boolean allowEarlyReference) {
      // 查询缓存中是否有创建好的单例
      Object singletonObject = this.singletonObjects.get(beanName);
      // 如果缓存不存在，判断是否正在创建中
      if (singletonObject == null && isSingletonCurrentlyInCreation(beanName)) {
          // 加锁防止并发
          synchronized (this.singletonObjects) {
              // 从earlySingletonObjects中查询是否有early缓存
              singletonObject = this.earlySingletonObjects.get(beanName);
              // early缓存也不存在，且允许early引用
              if (singletonObject == null && allowEarlyReference) {
                  // 从单例工厂Map里查询beanName
                  ObjectFactory<?> singletonFactory = this.singletonFactories.get(beanName);
                  if (singletonFactory != null) {
                      // singletonFactory存在，则调用getObject方法拿到单例对象
                      singletonObject = singletonFactory.getObject();
                      // 将单例对象添加到early缓存中
                      this.earlySingletonObjects.put(beanName, singletonObject);
                      // 移除单例工厂中对应的singletonFactory
                      this.singletonFactories.remove(beanName);
                  }
              }
          }
      }
      return (singletonObject != NULL_OBJECT ? singletonObject : null);
  }
  ```