# SpringCloud全家桶



## Nacos

https://nacos.io/zh-cn/docs/what-is-nacos.html

Nacos提供**服务发现和服务健康监测**和**动态配置服务**

### CAP

在Nacos的Instance(实例)中提供了一个ephemeral字段，这个字段是bool类型，这个字段和ZK中的含义差不多，都代表的是否是临时节点，在Nacos中如果是临时节点就会使用AP协议，如果不是临时节点就会走CP。当然在注册中心中所有的实例其实默认都是临时节点。

在Nacos中为了实现AP，自己定制了一套Distro协议。

注册中心中，Nacos中有一些数据需要持久化存储的，我们会使用Raft去进行数据的一致性同步存储，比如Service,命名空间的一些数据，Nacos认为实例是一个变化比较快的，临时的数据，不需要Raft这种一致性较高的协议，但是Service和命名空间是一个变化比较少的数据，适合做持久化存储。



## **Sentinel** 
