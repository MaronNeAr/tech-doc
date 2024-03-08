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
  
- 利用int型的state标记读写锁，高16位表示读锁，低16位表示写锁

- 读锁是公平的，写锁是非公平的

###### Condition

- BlockQueue

  ```java
  public class BlockQueue {
    
       private    Lock lock=new ReentrantLock();//锁
       private   List listQueue=new ArrayList();//存储消息的集合
       private    Condition notNull=lock.newCondition();//队列不为空
       private   Condition notFull=lock.newCondition();//队列不为满
    
       public    void add(String message){
           lock.lock();//操作队列先加锁
           try {
               //队列满了，通知消费者线程，生产线程阻塞
               while (listQueue.size()>=10){
                   notNull.signal();
                   System.out.println("队列已满"+ Thread.currentThread().getName()+"等待");
                   notFull.await();
               }
   
               //往队列添加一条消息，同时通知消费者有新消息了
               listQueue.add(message);
               System.out.println(Thread.currentThread().getName()+"生产一条消息");
               notNull.signal();//通知消费者线程
           } catch (InterruptedException e) {
               e.printStackTrace();
           }finally {
               lock.unlock();//释放锁
           }
       }
   
   
       public void remove(){
           lock.lock();//操作队列先加锁
           try {
               //队列空了，通知生产线程，消费线程阻塞
               while (listQueue.size()==0){
                   System.out.println("队列已空"+ Thread.currentThread().getName()+"等待");
                   notNull.await();
               }
               //队列删除一条消息，同时通知生产者队列有位置了
               listQueue.get(0);
               listQueue.remove(0);
               System.out.println(Thread.currentThread().getName()+"消费一条消息");
               notFull.signal();//同时通知生产者队列
   
           } catch (InterruptedException e) {
               e.printStackTrace();
           }finally {
               lock.unlock();
           }
       }
   
   }
  ```

- 关键方法

  - **`await()`：**使当前线程进入等待状态，并释放持有的锁。线程会等待直到被其他线程通过 `signal()` 或 `signalAll()` 方法唤醒。
  - **`awaitUninterruptibly()`：**与 `await()` 类似，但不会响应中断。
  - **`signal()`：**唤醒等待在该条件上的一个线程。被唤醒的线程会尝试重新获得锁，然后继续执行。
  - **`signalAll()`：**唤醒等待在该条件上的所有线程。通常在共享资源状态发生变化时使用。
  - **`awaitNanos(long nanosTimeout)`：**在指定时间内等待，如果在指定时间内未被唤醒，返回负数。
  - **`awaitUntil(Date deadline)`：**在指定的时间点之前等待，如果在指定时间前未被唤醒，返回 `true`；否则，返回 `false`。

###### StampedLock

- 采用的是“乐观读”的策略
- 底层基于时间戳（版本号）
- 关键方法
  - **`readLock()`（乐观读模式）：**通过 `readLock()` 获取一个读锁，返回一个 stamp（版本戳）。在乐观读模式下，不会阻塞其他线程的写操作，读操作可以同时进行。
  - **`tryOptimisticRead()`（尝试乐观读）：**返回一个 stamp，表示一个乐观读的开始。在乐观读的过程中，其他线程可能会进行写操作，所以在使用结果前需要通过 `validate()` 方法验证 stamp 是否仍然有效。
  - **`validate(long stamp)`（验证乐观读的结果）：**用于验证乐观读的结果是否仍然有效。如果在 `tryOptimisticRead()` 返回 stamp 后有写操作发生，则 `validate()` 返回 false，需要尝试其他的方式。
  - **`writeLock()`（写模式）：**通过 `writeLock()` 获取一个写锁，返回一个 stamp。写锁是排他性的，其他线程无法同时获取读锁或写锁。
  - **`unlock(long stamp)`（解锁）：**用于释放读锁或写锁，传入获取锁时返回的 stamp。释放锁会更新版本戳。