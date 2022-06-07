# Java并发编程

## 线程池

### 核心参数

```java
ExecutorService tp = new ThreadPoolExecutor(5,5,1000, TimeUnit.SECONDS,
        new LinkedBlockingDeque<>());
```

一、corePoolSize 线程池核心线程大小
线程池中会维护一个最小的线程数量，即使这些线程处理空闲状态，他们也不会被销毁，除非设置了allowCoreThreadTimeOut。这里的最小线程数量即是corePoolSize。

二、maximumPoolSize 线程池最大线程数量
一个任务被提交到线程池以后，首先会找有没有空闲存活线程，如果有则直接将任务交给这个空闲线程来执行，如果没有则会缓存到工作队列（后面会介绍）中，如果工作队列满了，才会创建一个新线程，然后从工作队列的头部取出一个任务交由新线程来处理，而将刚提交的任务放入工作队列尾部。线程池不会无限制的去创建新线程，它会有一个最大线程数量的限制，这个数量即由maximunPoolSize指定。

三、keepAliveTime 空闲线程存活时间
一个线程如果处于空闲状态，并且当前的线程数量大于corePoolSize，那么在指定时间后，这个空闲线程会被销毁，这里的指定时间由keepAliveTime来设定

四、unit 空闲线程存活时间单位
keepAliveTime的计量单位

五、workQueue 工作队列
新任务被提交后，会先进入到此工作队列中，任务调度时再从队列中取出任务。jdk中提供了四种工作队列：

​	1、ArrayBlockingQueue
​	是一个基于数组结构的有界阻塞队列，此队列按 FIFO（先进先出）原则对元素进行排序。该队列有限大

​	2、LinkedBlockingQueue
​	一个基于链表结构的阻塞队列，此队列按FIFO （先进先出） 排序元素，吞吐量通常要高于ArrayBlockingQueue。静态工厂方法Executors.newFixedThreadPool()使用了这个队列。**该队列无限大**

​	3、SynchronousQueue
​	一个**不存储元素**的阻塞队列。每个插入操作必须等到另一个线程调用移除操作，否则插入操作一直处于阻塞状	态，吞吐量通常要高于LinkedBlockingQueue，静态工厂方法Executors.newCachedThreadPool（5）使用了	这个队列。

​	4、PriorityBlockingQueue
​	一个具有优先级的**无限**阻塞队列。

六、threadFactory 线程工厂
创建一个新线程时使用的工厂，可以用来设定线程名、是否为daemon线程等等

七、handler 拒绝策略
当工作队列中的任务已到达最大限制，并且线程池中的线程数量也达到最大限制，这时如果有新任务提交进来，该如何处理呢。这里的拒绝策略，就是解决这个问题的，jdk中提供了4中拒绝策略：

- ThreadPoolExecutor.AbortPolicy:丢弃任务并抛出RejectedExecutionException异常。 **（默认）**
- ThreadPoolExecutor.DiscardPolicy：丢弃任务，但是不抛出异常。 
- ThreadPoolExecutor.DiscardOldestPolicy：丢弃队列最前面的任务，然后重新提交被拒绝的任务
- ThreadPoolExecutor.CallerRunsPolicy：由调用线程（提交任务的线程）处理该任务



## synchronized

### 四大状态

无锁状态：压根没人访问，无锁
偏向锁状态：只有一个访问，没有竞争，不会释放锁
轻量级锁状态：一旦产生竞争，则出现轻量级锁。轻量级锁通过自旋来尝试获取锁
重量级锁状态：多次循环未果，markWord转为重量级锁。

状态只升不降

对象头主要包括两部分 MarkWord和Klass Pointer，而synchronized依靠MarkWord来实现

![img](https://s6.51cto.com/oss/202112/25/15dd58d0348ef26c2a254795ac825dd7.png)

GC标记是标记为GC后的标志。**无锁中对象的hashcode指的是identity_hashcode**

**无锁**：

**偏向锁**：hashcode是懒加载，调用了才使用hashcode。已存在hashcode再执行同步代码，则不会进入偏向锁，会直接进入轻量级锁。

**轻量级锁：**锁在栈中做记录

**重量级锁：**

重量级锁指向一个叫**monitor**的对象：

![img](https://img-blog.csdnimg.cn/20210815163352506.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0NDE2MTkx,size_16,color_FFFFFF,t_70)

waiting是之前获得过锁，但条件不满足进入 WAITING 状态的线程



synchronized 用 3 种用法，用它可以来修饰普通方法、静态方法和代码块

修饰静态方法时，修饰的是字节码对象

### wait/Notify

必须在获取synchronized 锁的时候才可以执行。

wait：让出当前锁

notify：随机唤醒一个处于wait状态的进程，唤醒结果不定（不会让出锁）

notifyall：唤醒所有wait状态的进程（不会让出锁）

## ReentrantLock

### AQS

AQS 是一个用来构建锁和同步器的框架

![enter image description here](D:\study\lbeco\lbeco.github.io\java\multiThread.assets\CLH.png)

AQS 使用一个 int 成员变量来表示同步状态，通过内置的 FIFO 队列来完成获取资源线程的排队工作。AQS 使用 CAS 对该同步状态进行原子操作实现对其值的修改。



```java
private volatile int state;//共享变量，使用volatile修饰保证线程可见性
```

状态信息通过 protected 类型的`getState`，`setState`，`compareAndSetState`进行操作。compareAndSetState是对state的cas操作。

AQS默认使用的就是非公平锁。线程进入后会首先尝试获取锁，获取不到再去排队。

区别：

1. 非公平锁在调用 lock 后，首先就会调用 CAS 进行一次抢锁，如果这个时候恰巧锁没有被占用，那么直接就获取到锁返回了。
2. 非公平锁在 CAS 失败后，和公平锁一样都会进入到 tryAcquire 方法，在 tryAcquire 方法中，如果发现锁这个时候被释放了（state == 0），非公平锁会直接 CAS 抢锁，但是公平锁会判断等待队列是否有线程处于等待状态，如果有则不去抢锁，乖乖排到后面。



AQS 定义两种资源共享方式，一种是shared一种不是。CountDownLatch，Semaphore和ReentrantReadWriteLock的Sync实现了tryAcquireShared

### ReentrantLock

ReentrantLock是可重入锁，会记录使用锁的thread.每进行一次lock,state加一

### **Semaphore**

共享锁

使用AQS同步状态来保存信号量的当前计数。初始化的permit会存储在state里面。state为0时则线程等待

### CyclicBarrier

可重复使用的栅栏

维持两个计数器：count和parties。count是当前减掉的数，parties是设置的标志数。

### CountDownLatch

直接使用state做为countDownLatch的标志位，只能用一次。

```java
countDownLatch.countDown(); // 减一
countDownLatch.await(); // 等待countdown减到0，否则进程stall
```

### ReentrantReadWriteLock

读写锁<https://zhuanlan.zhihu.com/p/91408261>

ReentrantReadWriteLock将state拆分为两部分：高16位和低16位，其中高16位用来表示读锁状态，低16位用来表示写锁状态。当设置读锁成功时，就将高16位加1，释放读锁时，将高16位减1；当设置写锁成功时，就将低16位加1，释放写锁时，将第16位减1。

默认会创建非公平的读写锁



## volatile

作用：

- 保证可见性，利用MESI协议同步cache line

- 防止重排，利用内存屏障防止指令重排序导致的错误。在一个cpu上写回顺序是有保证的，但是多线程情况下：

  线程一： A 命令 B=true

  线程二：if(B) then use A

  在线程一中，A命令和B没有关系，所以有可能进行重排序。但是线程二就有可能use不到A:

  线程一：  B=true                       A 命令

  线程二：if(B) then use A （此时A尚未执行）

  https://blog.csdn.net/weixin_43982927/article/details/107969119

  故需要进行防止重排来保证执行的有序性。

  内存屏障四大指令插入情况

  1.在每个volatile写操作的前面插入一个StoreStore屏障，保证在volatile写之前，其前面的所有普通写操作都已经刷新到主内存中。  

  2.在每个volatile写操作的后面插入一个StoreLoad屏障，避免volatile写与后面可能有的volatile读/写操作重排序

  ![img](https://img-blog.csdnimg.cn/dcaf8d21f2f0483a95bdde94369670ce.png)

  3.在每个volatile读操作的后面插入一个LoadLoad屏障，禁止处理器把上面的volatile读与下面的普通读重排序。

  4.在每个volatile读操作的后面插入一个LoadStore屏障，禁止处理器把上面的volatile读与下面的普通写重排序。


  ![img](https://img-blog.csdnimg.cn/226a9a5dc0474dbfb576b30378bf9603.png)



StoreLoad的主要目的是保证volatile变量自身写后读的可见性，在实现上开销最大。

x86下只有StoreLoad屏障是有效操作，其他的屏障均为no-op。主要原因有二：1. x86中store-buffer是一个FIFO队列，结构上保证了写入顺序；2. x86中没有invalid queue，因此不需要LoadLoad或LoadStore来强制消费失效队列。

在某些cpu中，存在invalid queue。invalid queue用于缓存cache line的失效消息，也就是说，当cpu0写入W0(x, 1)，并从store buffer将修改刷入cache，此时cpu1读取R1(x, 0)仍是允许的。因为使cache line失效的消息被缓冲在了invalid queue中，还未被应用到cache line上。这也是一种会使得指令乱序的可能。load barrier存在的意义就是将invalid queue缓冲刷新。

对于x86而言，mfence指令会负责将store buffer中的数据，写入到cache中。这样做虽然只会生效StoreLoad,但是指不定以后x86就要搞其他屏障了。

arm必须要加这些屏障。



总线风暴：多个volatile在总线上互相发导致带宽爆炸
解决办法：部分volatile和cas使用synchronize



## ThreadLocal

<https://www.zhihu.com/question/341005993/answer/1367225682>
每个线程都有个map，map的key弱引用ThreadLocal实体。此时threadlocal实体受线程中的强引用和map中的弱引用。

![img](https://pic2.zhimg.com/80/v2-539374b14bbd71707aca2d7ddfc6f691_1440w.jpg?source=1940ef5c)

Map利用**线性探测法**解决冲突

**Q:**为什么要搞弱引用

对key的引用回收了，但是threadLocalMap对key的引用如果是强引用的话，就死活不会回收，造成内存泄漏。



**Q:**为什么会有内存泄漏

当线程中的强引用失效，若map不是弱引用，threadlocal迟迟没法被干掉
threadlocal会在get和set的时候顺手找到key为null的对象，然后干掉value防止内存泄漏。但是如果不调用这两个的话还是会泄露。最建议的方法还是每次用完对其进行remove操作



**Q: **为什么不把value也设置为弱引用：

之所以不把threadlocal实体都给设置为弱引用，因为你实体都搞弱引用一个GC直接没了，key拿不到数据

<https://blog.csdn.net/vicoqi/article/details/79743112>

子线程获取父类：
InheritableThreadLocal



## 进程状态



