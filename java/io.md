# IO

## 操作系统五大IO

阻塞式IO模型
非阻塞式IO模型
IO复用模型
信号驱动IO模型
异步IO模型

## Java三大IO

BIO
BIO （Blocking I/O）：同步阻塞I/O模式，来一个请求开一个线程处理，数据的读取写入必须阻塞在一个线程内等待其完成。

NIO：
NIO （New I/O）：同时支持阻塞与非阻塞模式。线程轮询多个socket以确认其是否完成 非阻塞IO复用

**Epoll Bug**

空轮询问题：selector.select()莫名其妙就被响应，导致cpu疯狂轮询直接冲到99%。Netty解决方法：加个计数器， 超过N就重建selector，将原SocketChannel从旧的Selector上去除注册，重新注册到新的Selector上，并将原来的Selector关闭。

AIO：
异步火星科技，1.7开始，没人用

NIO：
三大件：Channel Buffer Selector

selector 监听channel的事件 accept connect read write

零拷贝<https://zhuanlan.zhihu.com/p/258513662> 注意大小文件的差别

select 
遍历所有io，有数量限制

poll
遍历所有io，采用链表，没有数量限制

epoll
有限制，但是限制很大。
事件提醒，同时提供零拷贝功能，内核和用户空间共享

详见os中关于io的部分。

