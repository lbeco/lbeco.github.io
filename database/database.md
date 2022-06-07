# MySQL相关

mysql<https://www.cxyxiaowu.com/16302.html>

两阶段提交 <https://www.jianshu.com/p/0924ad31966d>

![v2-82fe912d213bd32cb01a869353ad0557_r](https://i.imgur.com/asHB2dx.jpg)



mvcc 在rc和rr下区别<https://blog.csdn.net/qq_35634181/article/details/113280233>

RR 级别只在事务开始时生成一次，之后一直使用该 ReadView。而 RC 级别则在每次 select 时，都会生成一个 ReadView。

DB_ROW_ID：行ID，随着插入新行而单调递增，如果有主键，则不会包含该列。

DB_TRX_ID：记录插入或更新该行的事务的事务ID。

DB_ROLL_PTR：回滚指针，指向 undo log 记录。每次对某条记录进行改动时，该列会存一个指针，可以通过这个指针找到该记录修改前的信息 。当某条记录被多次修改时，该行记录会存在多个版本，通过DB_ROLL_PTR 链接形成一个类似版本链的概念。

各种锁<https://www.cnblogs.com/yaochunhui/p/14186371.html>

### 悲观锁乐观锁

悲观锁
		我们使用悲观锁的话其实很简单(手动加行锁就行了)：select * from xxxx for update，在select 语句后边加了for update相当于加了排它锁(写锁)，加了写锁以后，其他事务就不能对它修改了！需要等待当前事务修改完之后才可以修改.也就是说，如果操作1使用select ... for update，操作2就无法对该条记录修改了，即可避免更新丢失。

乐观锁
		**乐观锁不是数据库层面上的锁，需要用户手动去加的锁**。一般我们在数据库表中添加一个版本字段version来实现，例如操作1和操作2在更新User表的时，执行语句如下：

```sql
update A set Name=lisi,version=version+1 where ID=#{id} and version=#{version}，
```


此时即可避免更新丢失。



mysql相关<https://www.cnblogs.com/xuwc/p/13873293.html>

执行计划<https://www.cnblogs.com/xinysu/p/7860609.html>

### mvcc能否解决幻读

 <https://blog.csdn.net/qq_35590091/article/details/107734005>


MySQL InnoDB的可重复读并不保证避免幻读，需要应用使用加锁读来保证。而这个加锁度使用到的机制就是next-key locks。

在mysql中，提供了两种事务隔离技术，第一个是mvcc，第二个是next-key技术。这个在使用不同的语句的时候可以动态选择。不加lock inshare mode for...update之类的快照读就使用mvcc。否则 当前读使用next-key。mvcc的优势是不加锁，并发性高。缺点是不是实时数据。next-key的优势是获取实时数据，但是需要加锁。

但是幻读还是解决不掉。如果一个读是当前读一个读是快照读，则还是会出现幻读的情况。

另外，重要：

在rr级别下，mvcc完全解决了重复读，但并不能真正的完全避免幻读，只是在部分场景下利用历史数据规避了幻读

对于快照读，mysql使用mvcc利用历史数据部分避免了幻读（在某些场景看上去规避了幻读）

要完全避免，需要手动加锁将快照读调整为当前读（mysql不会自动加锁），然后mysql使用next-key完全避免了幻读

### 数据库中的锁


InnoDB实现了以下两种类型的行锁：

> 共享锁（S锁、读锁）： 允许一个事务去读一行，阻止其他事务获得相同数据集的排他锁。即多个客户可以同时读取同一个资源，但不允许其他客户修改。
> 
> 排他锁（X锁、写锁)： 允许获得排他锁的事务更新数据，阻止其他事务取得相同数据集的读锁和写锁。写锁是排他的，写锁会阻塞其他的写锁和读锁。


另外，**为了允许行锁和表锁共存**，实现多粒度锁机制，InnoDB还有两种内部使用的意向锁（Intention Locks），这两种意向锁都是表锁：

> 意向共享锁（IS）： 事务打算给数据行加行共享锁，事务在给一个数据行加共享锁前必须先取得该表的IS锁。
>
> 意向排他锁（IX）： 事务打算给数据行加行排他锁，事务在给一个数据行加排他锁前必须先取得该表的IX锁。



视频<https://www.bilibili.com/video/BV1xh411Z79d>

### ACID

ACID 靠什么保证的？
A 原子性由undo log 日志保证， 它记录了需要回滚的日志信息， 事务回滚时撤消已经执行成功的sql
C 一致性由其他三大特性保证、程序代码要保证业务上的一致性
I 隔离性由MVCC 来保证
D 持久性由内存+ redolog 来保证，mysql修改数据同时在内存和redolog 记录这次操作， 宕机的时候可以从redolog恢复（durability）

### 索引下推

mysql在5.6之后提供了该功能。当使用索引条件下推优化时，如果存在某些被索引的列的判断条件时，MySQL服务器将这一部分判断条件传递给存储引擎，然后由存储引擎通过判断索引是否符合MySQL服务器传递的条件，只有当索引符合条件时才会将数据检索出来返回给MySQL服务器。如果没有这功能，就会把所有符合条件的结果返回，然后一个个判断

https://blog.csdn.net/sinat_29774479/article/details/103470244

索引失效：<https://www.cnblogs.com/liehen2046/p/11052666.html>



