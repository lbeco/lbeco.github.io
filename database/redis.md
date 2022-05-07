

## redis

redis持久化：

aof：append only file 持久化的日志文件

rdb：redis文件的快照

以bg形式fork一个线程来生成



数据类型：

string 		key value存储string

hash			存储一个kv的hashmap

list 			链表 可以做栈或者队列

set			集合

sorted set（zset）有序集合类型使用跳表

hyperloglog 基数统计

 bitmap 位图 可以用来做布隆过滤器

 GEO 地理位置 计算距离 指定范围内点等 使用geo hash进行编码

分布式锁：setnx（set if not exist）添加 需要考虑过期时间



事务：需要watch来关注相关数值，watch中数值发生变化就会进行回滚

代码写错了不作回滚

事务没啥人用，太垃圾了

一个名叫过期字典的数据库保存过期时间



删除多余数据：

定时删除 ：时间到就删除

惰性删除： 用到发现过期了

定期删除 ：周期性抽查存储空间 

每秒钟执行server.hz次serverCron

​										databasesCron 数据库轮询

​										activeExpireCycle expires轮询 随机挑选W个key。如果一轮中删除的key超过25%，循环该过程，否则检查下一个expires

数据逐出：满了就删除 使用lru/lfu 等算法



哨兵模式：Sentinel节点监控redis集群中Master主服务器工作的状态

主观下线：对单个redis节点的心跳没有回复

客观下线：哨兵节点共同判断下线

leader选举：类似raft



4.0:混合持久机制

6.0引入多线程



redis实现分布式锁：redlock 



雪崩：较短时间内较多key集中过期，请求大量积压，导致数据库被打爆，于是redis超时，最后崩溃

解决：预警 页面静态化 多级缓存 限流降级 有效期错峰加随机值 超热数据永久持久化

击穿：单个key非常热，过期后击穿 

解决：预先设定或现场将过热数据改为永久key，高峰来临时刷新数据有效期；设置二级缓存，保障两个缓存不会同时被淘汰

穿透：redis内存正常实命中率随时间下降，数据库压力大，崩掉。原因：出现大量非正常url访问，导致去数据库中查找

解决：布隆过滤器，实时监控后黑名单防控 

监控：prometheus cloud insight redis





### redisson

redisson是用java实现的redis分布式可重入锁

https://segmentfault.com/a/1190000038988087

防止redis单点故障，引入redlock，redlock可以保证大多数redis服务同意获取的锁，从而实现分布式