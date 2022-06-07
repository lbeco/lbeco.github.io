# Dubbo

http://dubbo.io/

Dubbo是一款高性能、轻量级的开源Java RPC框架，它提供了三大核心能力：

- 面向接口的远程方法调用
- 智能容错和负载均衡
- 以及服务自动注册和发现。

注册流程：

<img src="D:\study\lbeco\lbeco.github.io\mid\dubbo.assets\image-20220604194032666.png" alt="image-20220604194032666" style="zoom:50%;" />

## 层级

dubbo有十层，可以大致分为三层：

business 业务层，写业务代码

RPC层 进行远程调用

Remoting 网络传输，编解码，序列化

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190826143519959.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0owSGFu,size_16,color_FFFFFF,t_70)



头两层协议使用的是api，而后面都可以用spi进行自定义的实现。

## 传输协议

dubbo的传输功能基于netty

Dubbo3 提供了 Triple(Dubbo3)、Dubbo2 协议，这是 Dubbo 框架的原生协议。除此之外，Dubbo3 也对众多第三方协议进行了集成，并将它们纳入 Dubbo 的编程与服务治理体系， 包括 gRPC、Thrift、JsonRPC、Hessian2、REST 等。



## 服务发现

dubbo使用注册中心来实现服务发现，支持的注册中心有zookeeper，Redis等。默认推荐的就是zookeeper

