* **网络相关**
  tcp相关：

  tcp报文格式https://blog.csdn.net/paincupid/article/details/79726795

  ![img](https://uploadfiles.nowcoder.com/compress/mw1000/images/20211213/3639882_1639387402651/71CE0BC5FB4BBBA2BD6B11ED67B35432)

  UDP报文首部格式，伪首部只参与计算校验和

  ![img](https://images0.cnblogs.com/blog/153130/201307/31144013-d9a0f1873df5489a9b28b4df03ca93d2.png)
  
  拥塞控制<https://blog.csdn.net/qq_41431406/article/details/97926927>

  出现拥塞的依据：发生重传

  慢开始 拥塞避免
  快重传 快恢复（两个方法RENO版本有 tahoe没有）
  三挥四握<https://www.cnblogs.com/onesea/p/13053697.html>

  <img src="https://cooffeeli-blog.oss-cn-beijing.aliyuncs.com/TCP/established.png" alt="三次握手建立连接" style="zoom:67%;" />
  
  
  
  <img src="https://cooffeeli-blog.oss-cn-beijing.aliyuncs.com/TCP/close.png" alt="四次挥手关闭连接" style="zoom:67%;" />
  
  各种问题<https://blog.csdn.net/scuzoutao/article/details/81774100>
  ping <https://www.jianshu.com/p/e1795962ad76>

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

timewait可以：
可靠地实现TCP全双工连接的终止
允许老的重复分节在网络中消逝 
短连接表示“业务处理+传输数据的时间 远远小于 TIMEWAIT超时的时间”的连接，过多短链接使得系统的连接资源不足
timewait相关<https://www.cnblogs.com/dadonggg/p/8778318.html>
处理TIMEWAIT：修改配置
打开系统的TIMEWAIT重用和快速回收,增大队列

close_wait：程序写出问题或者太忙，终止请求的接收方不发fin

csrf:
服务端进行CSRF防御: 服务端渲染页面时添加一个随机数，在返回的表单上加上这个随机数。可以保证是这个页面提交的表单
搞验证码

xss攻击：在目标网站html中插东西

防重放：<https://blog.csdn.net/weixin_39986856/article/details/82657808> 时间戳和token结合加密 



  
