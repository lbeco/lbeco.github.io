# **网络相关**

## TCP

tcp报文格式https://blog.csdn.net/paincupid/article/details/79726795

![img](https://uploadfiles.nowcoder.com/compress/mw1000/images/20211213/3639882_1639387402651/71CE0BC5FB4BBBA2BD6B11ED67B35432)



### 重传机制

https://zhuanlan.zhihu.com/p/444058710

#### 超时重传

发送方在发送数据时设置一个定时器，当超过指定时间后如果还没有收到接收方的ACK响应，就会重发数据包。

弊端：超时周期可能相对较长，重传的等待时间可能过长。

#### 快速重传

快速重传不再以时间作为重传的标准，而是以数据作为重传的标准。

<img src="https://pic2.zhimg.com/80/v2-c3432af5fb6d64c79abbd5ca05dcfe89_1440w.jpg" alt="img" style="zoom: 25%;" />

发生三次同样的ACK后，进行重传。重传逻辑：

- SACK

  

- D-SACK

### 拥塞控制

<https://blog.csdn.net/qq_41431406/article/details/97926927>

出现拥塞的依据：发生重传

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190731155254165.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNDMxNDA2,size_16,color_FFFFFF,t_70)

cwnd：拥塞窗口

swnd: 发送窗口

ssthresh:慢开始门限

**慢开始** 

假设当前发送方拥塞窗口cwnd的值为1，而发送窗口swnd等于拥塞窗口cwnd，因此发送方当前只能发送一个数据报文段（拥塞窗口cwnd的值是几，就能发送几个数据报文段），接收方收到该数据报文段后，给发送方回复一个确认报文段，发送方收到该确认报文后，将拥塞窗口的值变为2。这一阶段是翻倍的

**拥塞避免**

超过ssthresh后，每个传输轮次，拥塞窗口cwnd只能线性加一

**快重传**（TCP RENO版本有 tahoe没有）



![在这里插入图片描述](https://img-blog.csdnimg.cn/20190731184314574.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNDMxNDA2,size_16,color_FFFFFF,t_70)



 **快恢复**（TCP RENO版本有 tahoe没有）

收到三个重复确认，得知只是丢失了个别的报文段。不启动慢开始算法，而执行快恢复算法

将ssthresh值调整到当前窗口的一半，从而进入拥塞避免状态

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190731184935595.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNDMxNDA2,size_16,color_FFFFFF,t_70)

### 三握四挥

三挥四握<https://www.cnblogs.com/onesea/p/13053697.html>

<img src="https://cooffeeli-blog.oss-cn-beijing.aliyuncs.com/TCP/established.png" alt="三次握手建立连接" style="zoom:67%;" />



<img src="https://cooffeeli-blog.oss-cn-beijing.aliyuncs.com/TCP/close.png" alt="四次挥手关闭连接" style="zoom:67%;" />




### 各种问题解决

各种问题<https://blog.csdn.net/scuzoutao/article/details/81774100>

timewait可以：

* 可靠地实现TCP全双工连接的终止

* 允许老的重复分节在网络中消逝 

短连接表示“业务处理+传输数据的时间 远远小于 TIMEWAIT超时的时间”的连接，过多短链接使得系统的连接资源不足
timewait相关<https://www.cnblogs.com/dadonggg/p/8778318.html>
处理TIMEWAIT：修改配置
打开系统的TIMEWAIT重用和快速回收,增大队列

大量处于close_wait：程序写出问题或者太忙，终止请求的接收方不发fin



## UDP



UDP报文首部格式，伪首部只参与计算校验和

![img](https://images0.cnblogs.com/blog/153130/201307/31144013-d9a0f1873df5489a9b28b4df03ca93d2.png)

## HTTP

http 1.0 1.1 2.0<https://blog.csdn.net/ailunlee/article/details/97831912>

1.1相对1.0：支持长连接，在一个tcp连接上可以传送多个请求

2.0相对1.1:多路复用技术，一个连接并发处理多个请求，压缩头部数据，实现服务器推送功能

http3 <https://zhuanlan.zhihu.com/p/143464334> Quic协议基于udp，避免请求丢失导致的队头阻塞

quic解决队头阻塞 <https://zhuanlan.zhihu.com/p/32553477>



* http各个code

  200 成功

  301 永久重定向 302 临时重定向（重定向后请求不可预测） 307 临时重定向，不会改变请求

  400 请求语法错误 401 需要权限 403 拒绝执行 404 找不到 408 timeout 414 请求过长

  500 内部错误 502 504 网关问题

  PUT 有幂等性 多次发送相同的PUT不会影响结果



## 安全相关

csrf:
服务端进行CSRF防御: 服务端渲染页面时添加一个随机数，在返回的表单上加上这个随机数。可以保证是这个页面提交的表单
搞验证码

xss攻击：在目标网站html中插东西

防重放：<https://blog.csdn.net/weixin_39986856/article/details/82657808> 时间戳和token结合加密 

## 七层体系

七层体系结构图：



![](file://C:\Users\Linbc\Desktop\CS\JavaGuide-master\docs\network\images\%E4%B8%83%E5%B1%82%E4%BD%93%E7%B3%BB%E7%BB%93%E6%9E%84%E5%9B%BE.png?lastModify=1653319433)



- 物理层：硬件协议
- 数据链路层：各种逻辑传输协议 ARP
- 网络层： 控制子网运行及路由选择等 ICMP IP
- 传输层：切分数据 TCP/IP
- 应用层： 各种应用 FTP HTTP



 ping使用的是icmp协议，处于网络层 <https://www.jianshu.com/p/e1795962ad76>
