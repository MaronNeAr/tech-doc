#### 线程池与Future

###### ThreadPoolExector

- 核心数据结构
  - AtomicInteger状态变量ctl，最高3位存储线程池状态，其余29位存储线程个数，状态包括RUNNING、SHUTDOWN、STOP、TIDYING和TERMINATED
  - BlockingQueue<Runable>工作队列
  - ReentrantLock互斥访问变量的锁
  - HashSet<Worker>线程集合，每一个线程代表一个Worker对象
- 线程池参数
  - corePoolSize：在线程池中始终维护的线程个数
  - maxPoolSize：在corePooSize已满、队列也满的情况下，扩充线程至此值
  - keepAliveTime/TimeUnit：maxPoolSize 中的空闲线程，销毁所需要的时间，总线程数收缩回corePoolSize
  - blockingQueue：线程池所用的队列类型
  - threadFactory：线程创建工厂，可以自定义，也有一个默认的
  - RejectedExecutionHandler：corePoolSize 已满，队列已 满，maxPoolSize 已满，最后的拒绝策略

- 处理流程
  - step1：判断当前线程数是否大于或等于corePoolSize。如果小于，则新建线程执行；如果大于，则进入step2。
  - step2：判断队列是否已满。如未满，则放入；如已满，则进入step3。
  - step3：判断当前线程数是否大于或等于maxPoolSize。如果小于，则新建线程执行；如果大于，则进入step4 
  - step4：根据拒绝策略，拒绝任务

###### Callable与Future

- Runable是没有返回值的，Callable是有返回值的，其底层实现为Future
- FutureTask是一个Adapter对象，它实现了Future接口，内部包含了一个Callable 对象，从而实现了把Callable转换成Runnable
- 使用submit提交futureTask执行任务，调用futuretask.get()方法时会阻塞，直到获取到异步任务的返回结果

###### ScheduledThreadPoolExecutor

- 实现了按时间调度，可以延迟执行任务，周期执行任务
- 底层使用DelayQueue，元素必须在队列中一段时间后才能被获取