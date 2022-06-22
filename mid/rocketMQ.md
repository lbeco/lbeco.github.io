# RocketMQ

## 使用消息队列的好处：

通常来说，使用消息队列能为我们的系统带来下面三点好处：

1. **通过异步处理提高系统性能（减少响应所需时间）。**
2. **削峰/限流**
3. **降低系统耦合性。**

## RocketMQ

RocketMQ是java实现的一个高性能分布式消息队列。



topic路由信息 无须在集群之间保持强一致，而是追求最终一致性，并且能容忍分钟级的不一致。正是基于这种特性，RocketMQ的NameServer集群之间互不通信，这样极大地降低了NameServer实现的复杂度，对网络的要求 也降低了不少，性能相比较ZooKeeper还有了极大的提升。

RocketMQ追求消息发送的高吞吐量， RocketMQ的消息存储文件被设计成文件组的概念，组内单个文件大小 固定，方便引入内存映射机制，所有主题的消息存储按顺序编写，极 大地提升了消息的写性能。同时为了兼顾消息消费与消息查找，引入 了消息消费队列文件与索引文件。

在设计上允许消息被重复消费。 这样极大地简化了消息中间件的内核，使得实现消息发送高可用变得 非常简单和高效，消息重复问题由消费者在消息消费时实现幂等。



## 架构

`RocketMQ` 技术架构中有四大角色 `NameServer` 、`Broker` 、`Producer` 、`Consumer`

`Broker`： 消息队列服务器，生产者生产消息到 `Broker` ，消费者从 `Broker` 拉取消息并消费。一个Topic可以分布在多个broker上

NameServer：注册中心，Broker把自己注册到注册中心上

Producer：生产者

Consumer：消费者

![img](https://rocketmq.apache.org/assets/images/rmq-basic-arc.png)



RocketMq提供两种方式：pull和push进行消息的消费

而RocketMq的push方式，本质上也是采用pull的方式进行实现的。也就是说**这两种方式本质上都是采用consumer轮询从broker拉取消息的**

## 使用

顺序模式：普通顺序和严格顺序 

普通顺序可以容忍短暂乱序，严格顺序性能差可用性低

重复消费：将消费者的接口配置为幂等

同步刷盘和异步刷盘：刷盘将数据存储到硬盘重。同步刷盘需要返回ACK，异步不需要，提升了读写



重试队列：消费端回传RECONSUME_LATER，MQ将消息放入重试队列

消费16次后进入死信队列



### 数据存储

Topic队列中的数据存储模式：

CommitLog-存储所有的消息元数据，包括Topic、QueueId以及message

CosumerQueue-消费逻辑队列：存储消息在CommitLog的offset

IndexFile-索引文件：存储消息的key和时间戳等信息，使得RocketMq可以采用key和时间区间来查询消息

![图片](https://img-blog.csdnimg.cn/img_convert/0e4ccbaa3ab74575592dc7d0df30dccb.png)

多个queue对应一个单独的CommitLog文件，性能较kafka慢，但是在partition多后性能不下降。可靠性较kafka强



## 消息去重

RocketMq中，当consumer消费完消息后，因为网络问题未及时发送ack到broker,broker就不会删掉当前已经消费过的消息，那么，该消息将会被重复投递给消费者去消费。

解决方案：

- 消费端处理消息的业务逻辑保持幂等性。那么不管来多少条重复消息，可以实现处理的结果都一样
- 建立一张日志表，使用消息主键作为表的主键，在处理消息前，先insert表，再做消息处理。这样可以避免消息重复消费。这个表可以是redis



## 问题

### 性能高的原因

RocketMq采用文件系统存储消息，采用顺序写的方式写入消息，使用零拷贝发送消息

### 如何保证RocketMQ不丢失消息

一条消息从生产到被消费，将会经历三个阶段：

![在这里插入图片描述](https://img-blog.csdnimg.cn/0afce44fa93245c9be41f731d0962e5c.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMxOTYwNjIz,size_16,color_FFFFFF,t_70)

**生产阶段**，Producer 新建消息，然后通过网络将消息投递给 MQ Broker

**存储阶段**，消息将会存储在 Broker 端磁盘中

**消费阶段**， Consumer 将会从 Broker 拉取消息

要保证不丢消息，就在这三者中做不丢消息的保证。

**生产阶段：**

生产者（Producer） 通过网络发送消息给 Broker，当 Broker 收到之后，将会返回确认响应信息给 Producer。所以生产者只要接收到返回的确认响应，就代表消息在生产阶段未丢失。发送消息的方式有同步和异步2种方式，不管是同步还是异步，都会碰到网络问题导致发送失败的情况。针对这种情况，我们可以设置合理的重试次数，当出现网络问题，可以自动重试。


**存储阶段：**

存储阶段为了消息不丢失，可以设置为同步刷盘。
