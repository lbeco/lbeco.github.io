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
1、ArrayBlockingQueue
是一个基于数组结构的有界阻塞队列，此队列按 FIFO（先进先出）原则对元素进行排序。
2、LinkedBlockingQueue
一个基于链表结构的阻塞队列，此队列按FIFO （先进先出） 排序元素，吞吐量通常要高于ArrayBlockingQueue。静态工厂方法Executors.newFixedThreadPool()使用了这个队列
3、SynchronousQueue
一个不存储元素的阻塞队列。每个插入操作必须等到另一个线程调用移除操作，否则插入操作一直处于阻塞状态，吞吐量通常要高于LinkedBlockingQueue，静态工厂方法Executors.newCachedThreadPool（5）使用了这个队列。
4、PriorityBlockingQueue
一个具有优先级的无限阻塞队列。

六、threadFactory 线程工厂
创建一个新线程时使用的工厂，可以用来设定线程名、是否为daemon线程等等

七、handler 拒绝策略
当工作队列中的任务已到达最大限制，并且线程池中的线程数量也达到最大限制，这时如果有新任务提交进来，该如何处理呢。这里的拒绝策略，就是解决这个问题的，jdk中提供了4中拒绝策略：

ThreadPoolExecutor.AbortPolicy:丢弃任务并抛出RejectedExecutionException异常。 （默认）
ThreadPoolExecutor.DiscardPolicy：丢弃任务，但是不抛出异常。 
ThreadPoolExecutor.DiscardOldestPolicy：丢弃队列最前面的任务，然后重新提交被拒绝的任务
ThreadPoolExecutor.CallerRunsPolicy：由调用线程（提交任务的线程）处理该任务



## synchronized



无锁状态：压根没人访问，无锁
偏向锁状态：只有一个访问，没有竞争，不会释放锁
轻量级锁状态：一旦产生竞争，则出现轻量级锁。轻量级锁通过自旋来尝试获取锁
重量级锁状态：多次循环未果，markWord转为重量级锁。

状态只升不降

对象头主要包括两部分 MarkWord和Klass Pointer，而synchronized依靠MarkWord来实现

![img](https://s6.51cto.com/oss/202112/25/15dd58d0348ef26c2a254795ac825dd7.png)****

GC标记是标记为GC后的标志



## ReentrantLock

### AQS

AQS 是一个用来构建锁和同步器的框架

![enter image description here](D:\study\lbeco\lbeco.github.io\java\multiThread.assets\CLH.png)

AQS 使用一个 int 成员变量来表示同步状态，通过内置的 FIFO 队列来完成获取资源线程的排队工作。AQS 使用 CAS 对该同步状态进行原子操作实现对其值的修改。

读写锁<https://zhuanlan.zhihu.com/p/91408261>

## volatile



总线风暴：多个volatile在总线上互相发导致带宽爆炸
解决办法：部分volatile和cas使用synchronize



## ThreadLocal

<https://www.zhihu.com/question/341005993/answer/1367225682>
每个线程都有个map，map的key弱引用ThreadLocal实体。此时threadlocal实体受线程中的强引用和map中的弱引用。
当线程中的强引用失效，若map不是弱引用，threadlocal迟迟没法被干掉
threadlocal会在get和set的时候顺手找到key为null的对象，然后干掉value防止内存泄漏。但是最建议的方法还是每次用完对其进行remove操作

之所以不把threadlocal实体都给设置为弱引用，因为指不定其他还在引用（todo,不靠谱）

<https://blog.csdn.net/vicoqi/article/details/79743112>

子线程获取父类：
InheritableThreadLocal

