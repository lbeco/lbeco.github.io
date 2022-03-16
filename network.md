* **网络相关**
  tcp相关：
  拥塞控制<https://blog.csdn.net/qq_41431406/article/details/97926927>
  慢开始 拥塞避免
  快重传 快恢复（RENO版本有 tahoe没有）
  三挥四握<https://www.cnblogs.com/onesea/p/13053697.html>
  各种问题<https://blog.csdn.net/scuzoutao/article/details/81774100>
  ping 网络层协议 <https://www.jianshu.com/p/e1795962ad76>
  http 1.0 1.1 2.0<https://blog.csdn.net/ailunlee/article/details/97831912>
  http3 <https://zhuanlan.zhihu.com/p/143464334>
  http各个code详见书

  tcp协议格式https://blog.csdn.net/u011425939/article/details/103091636

  

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

防重放：<https://blog.csdn.net/weixin_39986856/article/details/82657808> 时间戳和token结合加密 

quic解决队头阻塞 <https://zhuanlan.zhihu.com/p/32553477>

