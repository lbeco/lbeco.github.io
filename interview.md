面经
===
* **字节一面**
tcp三次握手四次挥手 timewait 
mysql索引 hash索引 B+树 
https原理
redis雪崩击穿 穿透
乐观锁悲观锁和java实现
编程题：接雨水
* **字节二面**
tcp拥塞 
各种排序 各种排序的时间复杂度  桶排序 dfs bfs prim kruskal
正则表达式查ip
联合索引 
数组实现循环队列
堆的原理
编程题：判断图是不是二分图

* **字节三面**
讲项目
数据链路怎么实现
防暴力破解
numa
linux fork子进程会继承什么 文件描述符
两台机子之间网络不好，如何传输大量数据udp传输，个人回答：压缩和校验判断正确性，tcp用来判断哪些需要重传
一个二维平面上的离散点集封闭图形，如何判断任意一点是否包含在其中
判断数组中唯一不重复数字（异或）
判断数组中唯二不重复数字（答了map）
两个人轮流拿石子，可以拿1-2个，把石子拿完的人获胜。先手怎么赢，什么情况下先手必赢
升级版，可以拿1或2或4到2的n次方个，先手怎么赢，什么时候必赢
尾部递归
redis击穿
写一下scp命令
编程题：忘了，反正不难
* **腾讯一面 运维开发（怕是因为简历上一堆运维相关被丢过来，气死）**
jvm虚拟机，垃圾回收器 
tcp三次握手 为什么不能两次 
项目里起了什么作用，committer工作都干了啥
linux运行一个指令提示not found是为什么 linux上的软件版本怎么控制
Java interface干啥的
Kafka hadoop了解吗（不懂）
别人会用三个什么词评价你
平时有什么爱好
我们也不知道你这算不算实习，回去等通知吧

* **腾讯一面 游戏安全**
描述项目
如何防重放
对程序做了哪些安全加固
用的是tomcat吗
用linux命令遍历文件夹下所有. h文件命令
斗地主有52张牌，设计一个自动发牌的程序，算时间复杂度
python有五个线程，运行在几个cpu上
用过什么锁
进程间通信
* **百度一面**
java基本类
hashmap八股文 怎么扩容 put怎么put
介绍一下常用的锁
项目中用了什么并发框架concurrenthashmap
红黑树
b树b+树及其区别
b+树和b树的范围查找
jvm内存模型 什么情况下进入老年代 什么情况下被回收
强软弱虚引用
threadlocal，用的是什么数据结构
bean加载
new创建过程
IOC aop
spring循环依赖怎么解决
引用计数和标记
代码层面，代理怎么实现
编程题：判断链表是否有环 算时间复杂度
*在此鄙视一下牛客网，忘记给我发进二面的通知了，垃圾*

* **字节一面**
项目
mysql 索引 聚簇非聚簇 性能
mysql隔离级别，锁，MVCC
为什么用B+
MVCC能不能解决幻读，为什么
next key lock能不能解决幻读
证书怎么确保正确 根证书 证书链 
证书协议流程


>编程题：
给你一个int型的数组，这个数组是由一个有序数组循环右移N位得到的。 要实现一个函数，返回该数组的中位数
输入 567891234 输出 5

>智力题
有 1000 只水桶，其中有且只有一桶装的含有毒药，其余装的都是水。它们从外观看起来都一样。如果小猪喝了毒药，它会在 一小时内死去。
问题来了，如果需要你在一小时内，弄清楚哪只水桶含有毒药，你最少需要多少只猪？
如果改成猪喝了水，15分钟就死，需要几只猪？

* **字节一面 抖音直播国际化**
四次挥手
shuffle算法
B+树和B树
timewait
udp设计可靠协议
内存对齐
页表 缓存 cpu和内存交互（敢问我这个，让你看看什么叫专业人士）
内存泄漏
tlb

hashmap扩容（一直没听懂面试官要说啥，最后也说我答得不好）
项目
负载均衡（不会）

>编程题：
数组连续最大和

* **字节二面 抖音直播国际化**
三次握手四次挥手
synflush
双检锁

volatile
netty
nio bio aio
类型擦除


最大堆 最小堆
两个stack队列
优先队列

>编程题：
O(1)实现大顶堆小顶堆

* **google intern**
来自国际友人


0 1 0 1
0 -1 0 0
1 1 0 -1

def getbike(bike[[int]], p[[int]]) -> (p,b)


[1,2,4,12,3,5,7,45,76,98,577,3466,]
dp[]
1  2 1  4 3 4 5  6  7  8   9  10
