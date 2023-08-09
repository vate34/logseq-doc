- ```c++
  ObjectMonitor() {
      _header       = NULL;
      _count        = 0; //锁的计数器，获取锁时count数值加1，释放锁时count值减1，直到
      _waiters      = 0, //等待线程数
      _recursions   = 0; //锁的重入次数
      _object       = NULL; 
      _owner        = NULL; //指向持有ObjectMonitor对象的线程地址
      _WaitSet      = NULL; //处于wait状态的线程，会被加入到_WaitSet
      _WaitSetLock  = 0 ;
      _Responsible  = NULL ;
      _succ         = NULL ;
      _cxq          = NULL ; //阻塞在EntryList上的单向线程列表
      FreeNext      = NULL ;
      _EntryList    = NULL ; //处于等待锁block状态的线程，会被加入到该列表
      _SpinFreq     = 0 ;
      _SpinClock    = 0 ;
      OwnerIsThread = 0 ;
    }
  ```