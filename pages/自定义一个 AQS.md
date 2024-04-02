- 参考自：https://tech.meituan.com/2019/12/05/aqs-theory-and-apply.html
- ## 定义
	- ```java
	  public class LeeLock  {
	  
	      private static class Sync extends AbstractQueuedSynchronizer {
	          @Override
	          protected boolean tryAcquire (int arg) {
	              return compareAndSetState(0, 1);
	          }
	  
	          @Override
	          protected boolean tryRelease (int arg) {
	              setState(0);
	              return true;
	          }
	  
	          @Override
	          protected boolean isHeldExclusively () {
	              return getState() == 1;
	          }
	      }
	      
	      private Sync sync = new Sync();
	      
	      public void lock () {
	          sync.acquire(1);
	      }
	      
	      public void unlock () {
	          sync.release(1);
	      }
	  }
	  ```
- ## 使用
	- ```java
	  public class LeeMain {
	  
	      static int count = 0;
	      static LeeLock leeLock = new LeeLock();
	  
	      public static void main (String[] args) throws InterruptedException {
	  
	          Runnable runnable = new Runnable() {
	              @Override
	              public void run () {
	                  try {
	                      leeLock.lock();
	                      for (int i = 0; i < 10000; i++) {
	                          count++;
	                      }
	                  } catch (Exception e) {
	                      e.printStackTrace();
	                  } finally {
	                      leeLock.unlock();
	                  }
	  
	              }
	          };
	          Thread thread1 = new Thread(runnable);
	          Thread thread2 = new Thread(runnable);
	          thread1.start();
	          thread2.start();
	          thread1.join();
	          thread2.join();
	          System.out.println(count);
	      }
	  }
	  ```