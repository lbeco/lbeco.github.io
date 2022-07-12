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

**timewait时收到请求：**

如果客户端的 SYN 的「序列号」比服务端「期望下一个收到的序列号」要大，并且SYN 的「时间戳」比服务端「最后收到的报文的时间戳」要大。那么就会**重用该四元组连接**，跳过 2MSL 而转变为 SYN_RECV 状态，接着就能进行建立连接过程。

如果客户端的 SYN 的「序列号」比服务端「期望下一个收到的序列号」要小，或者SYN 的「时间戳」比服务端「最后收到的报文的时间戳」要小。那么就会**再回复一个第四次挥手的 ACK 报文**，客户端收到后，发现并不是自己期望收到确认号，就回 RST 报文给服务端。


**处理TIMEWAIT**

修改配置，增大backlog队列

打开系统的TIMEWAIT重用和快速回收

backlog是用于保存TCP连接的队列，分为未连接和已建立连接两个队列。

http://imhuchao.com/800.html

**处理大量连接处于close_wait**

程序写出问题或者太忙，终止请求的接收方不发fin，debug即可

**处理sync攻击**（泛洪攻击）

sync攻击指的是大量发送连接请求，然后不回复。

解决方法：使用硬路由进行控制，加大backlog队列长度

**DDos**

增多静态资源，做好拦截，限定协议。

## UDP



UDP报文首部格式，伪首部只参与计算校验和

![img](https://images0.cnblogs.com/blog/153130/201307/31144013-d9a0f1873df5489a9b28b4df03ca93d2.png)



udp的可靠传输：

最简单的方式是在应用层模仿传输层TCP的可靠性传输。下面不考虑拥塞处理，可靠UDP的简单设计。

- 1、添加seq/ack机制，确保数据发送到对端。使用数据包+序列号建立应答机制
- 2、添加发送和接收缓冲区，主要是用户超时重传。
- 3、添加超时重传机制。



## DNS

递归查询：**一般客户机和本地DNS服务器之间属于递归查询**，即当客户机向DNS服务器发出请求后,若DNS服务器本身不能解析，则会向另外的DNS服务器发出查询请求，得到最终的肯定或否定的结果后转交给客户机。此查询的源和目标保持不变，为了查询结果只需要发起一次查询。

迭代查询：一般情况下(有例外)本地的DNS服务器向其它DNS服务器的查询属于迭代查询，如：若对方不能返回权威的结果，则它会向下一个DNS服务器(参考前一个DNS服务器返回的结果)再次发起进行查询，直到返回查询的结果为止。此查询的源不变，但查询的目标不断变化，为查询结果一般需要发起多次查询。


## HTTP

http 1.0 1.1 2.0<https://blog.csdn.net/ailunlee/article/details/97831912>

1.1相对1.0：支持长连接，在一个tcp连接上可以传送多个请求，引入了更多的缓存控制策略。HTTP1.0需要使用keep-alive参数来告知服务器端要建立一个长连接。

2.0相对1.1:多路复用技术，一个连接并发处理多个请求，压缩头部数据，实现服务器推送功能

http3 <https://zhuanlan.zhihu.com/p/143464334> Quic协议基于udp，避免请求丢失导致的队头阻塞

quic解决队头阻塞 <https://zhuanlan.zhihu.com/p/32553477>

#### 缓存

**强缓存**

强缓存的意思很简单，直接从浏览器缓存过的本地进行读取，不会去请求服务器。例如请求一个图片，当缓存后，第二次访问，直接从本地去拿，不会再去请求这个资源，可以节省服务器资源。可以通过三种方式来设置强缓存

**协商缓存**

协商缓存表示在使用本地的缓存之前，会先向服务器发一个请求，与服务器协商当前浏览器的缓存是否已经过期了，如果没过期，那么就使用本地的资源，如果过期了就去请求最新资源。

* http各个code

  200 成功

  301 永久重定向 302 临时重定向（重定向后请求不可预测） 307 临时重定向，不会改变请求

  400 请求语法错误 401 需要权限 403 拒绝执行 404 找不到 408 timeout 414 请求过长

  500 内部错误 502 504 网关问题

  PUT 有幂等性 多次发送相同的PUT不会影响结果



## 安全相关

### csrf

CSRF攻击的要点就是在向服务器发送请求的时候，如果相应网站是登录状态，相应的`cookie`会自动的发送给对应的服务器。造成服务器不知道这个请求是用户发起的还是伪造的。

服务端进行CSRF防御: 服务端渲染页面时添加一个随机数，在返回的表单上加上这个随机数。可以保证是这个页面提交的表单
搞验证码

cookie进行过期时间管理

### xss攻击

在目标网站html中插东西，发送错误请求

解决：做输入校验

### 防重放

<https://blog.csdn.net/weixin_39986856/article/details/82657808> 

所谓重放攻击就是攻击者发送一个目的主机已接收过的包，来达到欺骗系统的目的，主要用于身份认证过程。

解决：时间戳和token结合加密 

### 跨域请求

一个页面：

- 无法读取非同源网页的 Cookie、LocalStorage 和 IndexedDB
- 无法接触非同源网页的 DOM
- 无法向非同源地址发送 AJAX 请求（可以发送，但浏览器会拒绝接受响应）

所以在A网站中想要给B网站发请求就会被跨域阻挡，但是直接Post没问题

允许跨域：Access-Control-Allow-Origin *

### 中间人攻击

攻击者与通讯的两端分别创建独立的联系，并交换其所收到的数据，使通讯的两端认为他们正在通过一个私密的连接与对方直接对话，但事实上整个会话都被攻击者完全控制。在中间人攻击中，攻击者可以拦截通讯双方的通话并插入新的内容。

解决：https证书校验 多因素身份验证



## 七层体系

七层体系结构图：



![](file://C:\Users\Linbc\Desktop\CS\JavaGuide-master\docs\network\images\%E4%B8%83%E5%B1%82%E4%BD%93%E7%B3%BB%E7%BB%93%E6%9E%84%E5%9B%BE.png?lastModify=1653319433)



- 物理层：硬件协议
- 数据链路层：各种逻辑传输协议 ARP
- 网络层： 控制子网运行及路由选择等 ICMP IP
- 传输层：保证数据传输到对端 切分数据 TCP/IP
- 应用层： 
  - 会话层：建立会话 SSL TLS
  - 表示层：加解密加解压
  - 应用层：HTTP FTP SMTP




 ping使用的是icmp协议，处于网络层 <https://www.jianshu.com/p/e1795962ad76>

## 经典问题

**输入url到看到网页发生了什么**

属实是经典永流传

https://www.zhihu.com/question/34873227/answer/1657140394
