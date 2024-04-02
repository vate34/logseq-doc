- 示例为某个线程等待前面n个线程执行完毕再执行。
- ```java
  public class CountDownLatchExample {
    // 请求的数量
    private static final int THREAD_COUNT = 550;
  
    public static void main(String[] args) throws InterruptedException {
      // 创建一个具有固定线程数量的线程池对象（如果这里线程池的线程数量给太少的话你会发现执行的很慢）
      // 只是测试使用，实际场景请手动赋值线程池参数
      ExecutorService threadPool = Executors.newFixedThreadPool(300);
      final CountDownLatch countDownLatch = new CountDownLatch(THREAD_COUNT);
      for (int i = 0; i < THREAD_COUNT; i++) {
        final int threadNum = i;
        threadPool.execute(() -> {
          try {
            test(threadNum);
          } catch (InterruptedException e) {
            e.printStackTrace();
          } finally {
            // 表示一个请求已经被完成
            countDownLatch.countDown();
          }
  
        });
      }
      countDownLatch.await();
      threadPool.shutdown();
      System.out.println("finish");
    }
  
    public static void test(int threadnum) throws InterruptedException {
      Thread.sleep(1000);
      System.out.println("threadNum:" + threadnum);
      Thread.sleep(1000);
    }
  }
  
  ```
- 上面的代码中，我们定义了请求的数量为 550，当这 550 个请求被处理完成之后，才会执行`System.out.println("finish");`。
- 与 `CountDownLatch` 的第一次交互是主线程等待其他线程。主线程必须在启动其他线程后立即调用 `CountDownLatch.await()` 方法。这样主线程的操作就会在这个方法上阻塞，直到其他线程完成各自的任务。
- 其他 N 个线程必须引用闭锁对象，因为他们需要通知 `CountDownLatch` 对象，他们已经完成了各自的任务。这种通知机制是通过 `CountDownLatch.countDown()`方法来完成的；每调用一次这个方法，在构造函数中初始化的 count 值就减 1。所以当 N 个线程都调 用了这个方法，count 的值等于 0，然后主线程就能通过 `await()`方法，恢复执行自己的任务。