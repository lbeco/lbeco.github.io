使用消息队列的好处：

通常来说，使用消息队列能为我们的系统带来下面三点好处：

1. **通过异步处理提高系统性能（减少响应所需时间）。**
2. **削峰/限流**
3. **降低系统耦合性。**



`RocketMQ` 技术架构中有四大角色 `NameServer` 、`Broker` 、`Producer` 、`Consumer`

`Broker`： 消息队列服务器，生产者生产消息到 `Broker` ，消费者从 `Broker` 拉取消息并消费。一个Topic可以分布在多个broker上

NameServer：注册中心，Broker把自己注册到注册中心上

Producer：生产者

Consumer：消费者



顺序模式：普通顺序和严格顺序 

普通顺序可以容忍短暂乱序，严格顺序性能差可用性低

重复消费：将消费者的接口配置为幂等

同步刷盘和异步刷盘：刷盘将数据存储到硬盘重。同步刷盘需要返回ACK，异步不需要，提升了读写



重试队列：消费端回传RECONSUME_LATER，MQ将消息放入重试队列

消费16次后进入死信队列



Topic队列中的数据存储模式：

CommitLog：消息存储主体 大小固定 

ConsumeQueue：基于topic的commitlog索引文件，保存 CommitLog 中的起始物理偏移量 offset，消息大小 `size` 和消息 `Tag` 的 `HashCode` 值

多个queue对应一个单独的CommitLog文件，性能较kafka慢，但是在partition多后性能不下降。可靠性较kafka强

