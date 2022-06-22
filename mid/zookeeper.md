# zookeeper

ZooKeeper主要服务于分布式系统，可以实现统一配置管理、统一命名服务、分布式锁、集群管理。

## 数据结构

ZNode是zookeeper的基本结构，Znode分为**两种**类型：

- **短暂/临时(Ephemeral)**：当客户端和服务端断开连接后，所创建的Znode(节点)**会自动删除**
- **持久(Persistent)**：当客户端和服务端断开连接后，所创建的Znode(节点)**不会删除**

client可以监听事件的变化

## ZAB算法

- Leader：通过选举确定一台机器，为客户端提供读写功能。
- Follower：提供读功能，参与选举。
- Observer：提供读功能，不参与选举，也不参与过半成功策略。因此observer是在不影响写性能下，提升集群的读性能。



三个阶段：

- discovery： 进行选举，选出leader
- sync：进行状态同步，leader获取
- broadcast：leader进行广播，正常工作

### zxid

zxid是zookeeper事务的标识id

包含了leaderID（类似于raft的term）和事务id（类似于index）

### zab的特点

选举时无法提供服务，故实现的是CP

和raft算法的差别：直接选主选就完了，不需要像raft一样判断谁的log比较新。

和nacos的算法的差别：nacos保证ap，始终可用。

## 监听

![img](https://img-blog.csdnimg.cn/3adf6d2686a24f3a9fe3ea158adba96b.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAWl9ISEhIX1o=,size_20,color_FFFFFF,t_70,g_se,x_16)

客户端首先将 Watcher注册到服务端，同时将 Watcher对象保存到客户端的watch管理器中。当Zookeeper服务端监听的数据状态发生变化时，服务端会主动通知客户端，接着客户端的 Watch管理器会触发相关 Watcher事件来回调相应处理逻辑，从而完成整体的数据 发布/订阅流程。
