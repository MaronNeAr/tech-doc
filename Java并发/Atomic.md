#### Atomic

###### AtomicInteger和AtomicLong

- AtomicInteger是很典型的乐观锁
- 典型的悲观锁：synchronized和ReentrantLock

- 自旋和阻塞
  - 策略1:放弃CPU，进入阻塞状态，等待后续被唤醒，再重新被操 作系统调度。
  - 策略2:不放弃CPU，空转，不断重试，也就是所谓的“自旋”。

###### AtomicBoolean和AtomicReference

- Unsafe中只提供int、long、Object三种类型的CAS操作
- AtomicBoolean底层采用int类型转换进行支持

###### AtomicStampedReference和AtomicMarkableReference

- ABA问题的解决：通过版本号机制进行处理
- AtomicMarkableReference中的版本号是boolean类型的

###### AtomicIntegerArray、AtomicLongArray和AtomicReferenceArray

- 底层使用compareAndSwapInt函数，需要把下标转化成对应的内存偏移量

###### Striped64和和LongAdder

- Striped64包括LongAdder、LongAccumulator、DoubleAdder、DoubleAccumulator
- LongAdder底层是基于一个base和多个Cell，如果并发度低则直接加在base上，如果并发度高则先分别加到每个Cell上，再将base和多个Cell进行求和

- LongAdder是最终一致性而不是强一致性



