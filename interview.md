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



* **字节一面 质量保障**
01轮流打印
mysql一道题联查


* **字节二面 质量保障**

“sh a.sh”、“source a.sh”和“./a.sh”三种方式执行a.sh的区别 <https://www.cnblogs.com/pcat/p/5467188.html>
判断舞会有多少人戴着黑帽子
Python 装饰器

>服务循环依赖检测 dfs
java 双检锁

* **字节三面 质量保障**
  数据库乐观悲观锁
  sql语句强行使用某索引
  64匹马赛马
  HTTP中 PUT 和POST的区别
  MVVN
  https流程
  
  > 打印排序
  
* **字节hr面** 
  为什么从华为离职
  为什么转行
  转行有什么考虑
  为什么选择字节，看重哪一方面
  要转GO

* **腾讯一面 游戏数据**

  HTTP GET POST差别
  HTTP的编码（要命）
  项目相关，十分详细。重点是数据库调优
  几万个会话怎么做 很多条数据怎么做（某公司项目确实没啥用）
  10W个数据最大1000个
  要转GO
  
  >k个一组反转链表（没写出来，吐了）
  
* **腾讯一面 游戏增值**

  项目怎么判断微服务的调用 (烂项目没有这玩意)

  多线程 nio bio oauth2.0

  分布式锁 实现

  防重放机制

  设计一个连接管理类 提供过期和最大连接数功能

  合并k个有效链表
  
* **腾讯二面 游戏增值 电话面**

  项目 项目亮点和细节 还有哪里可以改进，你负责了哪些方向

  https tcp ip 

  Linux进程的几种状态之R、S、D、T、Z 僵尸进程

  用户态内核态 协程 进程切换开销

  对数据库做了哪些优化

  docker k8s 微服务怎么拆分

  todo 修改简历 删去一些吹逼的内容

  总体上聊天比较愉快

* **腾讯三面 游戏增值 电话面**

  考了一道easy，

- **阿里一面  中间件**

  springcloud了解不

  rocketmq是什么一个结构，怎么保证可用性

  two sum的各种变体，有序无序有重复无重复，怎么优化，复杂度

- **阿里二面  中间件**

  tcp三握四挥 tcp头文件 wireshark调试

  多线程 数据库怎么优化

  java各种调试 arthas怎么用（没用过翻车了）

  别人说你api很慢怎么办（面试官经典解答：第一步，质疑调你api的人，第二步，质疑你调的api，再后面，质疑线上资源被占用了，最后，用arthas看看自己，再看看jvm）


* **微软一面**

  搜索二叉树序列化和反序列化（中序遍历）
  完全二叉树序列化和反序列化（2n和2n+1）

  * **微软二面**
  top k问题 （没吃透，留下了悔恨的泪水）
  
* **阿里一面 支付宝设备管理**

  就闲扯，题都没做，问了点tcp的内容

  全程和我说不要对他们部门有太多幻想，不知道是来干啥的这

  **阿里二面 支付宝设备管理**

  深入地问了之前项目中的问题 有灰度发布吗 连接和微服务的生命周期

  netty的线程模型 netty为什么这么快 netty是阻塞还是非阻塞

  你做的最有成就的事情是什么

  有论文/比赛/实验室经历吗

  

* **美团一面**

  全在问项目

  sql怎么调优，explain怎么看

  多研究研究怎么说专有技术名词

  领域模型

  java logback log4j

  mybatis 代理 两种代理方式 spi （不会）

  

  做题：前缀和的two sum



