#### Lock和Condition

###### 互斥锁

- `Lock`中的`lock()`方法可以不能被中断，`lockInterruptibly()`方法可以被中断
- `FairSync`：公平锁；`NonfairSync`：非公平锁
- `AbstractQueuedSynchronizer`原理
  - 需要一个`state`变量，标记该锁的状态。`state`变量至少有两个 值:0、1。对`state`变量的操作，要确保线程安全，也就是会用到CAS。表示阻塞态的线程数量
  - 需要记录当前是哪个线程持有锁。
  - 需要底层支持对一个线程进行阻塞或唤醒操作。
  - 需要有一个队列维护所有阻塞的线程。这个队列也必须是线程 安全的无锁队列，也需要用到CAS。
- `Unsafe`中的`park()`：表示阻塞当前进程；``unpark(Thread T)``：表示唤醒T线程`park()`处的阻塞
- `Acquire`：把线程放入阻塞队列，然后阻塞该线程
- `FairSync`与`NonfairSync`的区别：`FairSync`只有当没有线程持有锁，且排在队列第一个时才去抢锁，否则继续排队，即所谓的“公平”
- `addWaiter`：为当前线程生成一个`Node`，然后将该结点放入双向队列的尾部

###### 读写锁

- 读写锁底层实现

  ```java
  public class ReadWriteLock {
      private int readers; // 记录当前读取线程的数量
      private int writers; // 记录当前写入线程的数量
      private int writeRequests; // 记录等待写入的请求数量
  
      public synchronized void lockRead() throws InterruptedException {
          while (writers > 0 || writeRequests > 0) {
              wait();
          }
          readers++;
      }
  
      public synchronized void unlockRead() {
          readers--;
          notifyAll();
      }
  
      public synchronized void lockWrite() throws InterruptedException {
          writeRequests++;
          while (readers > 0 || writers > 0) {
              wait();
          }
          writeRequests--;
          writers++;
      }
  
      public synchronized void unlockWrite() {
          writers--;
          notifyAll();
      }
  }
  ```
  
  