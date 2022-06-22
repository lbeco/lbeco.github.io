https://nyimac.gitee.io/2021/04/25/Netty%E5%9F%BA%E7%A1%80/#Netty

# Netty

## 线程模型与EventLoop

Netty的线程模型是主从多线程模型

eventLoopGroup里面有数个工具线程（eventLoop）

典型的就是 NioEventLoopGroup 和 DefaultEventLoopGroup，DefaultEventLoopGroup 啥都不能干，就能run

一般我们创建两个NioEventLoopGroup，用于Acceptor和NIO I/O线程

EventLoop默认的进程数是核心数×2，最少是1

一般来说，划分不同任务的不同eventloopGroup（boss和worker）在.group中指定即可。boss管理channel上的事件，worker负责读写

可以指定单独的EventLoopGroup来处理



一个channel会和对应的EventLoop绑定，单个线程就无需设计锁机制，是无锁化的串行设计

## ChannelPipeline

ChannelPipeline是ChannelHandler的容器，负责管理handler和事件拦截与调度

![image-20220515234503628](D:\study\lbeco\lbeco.github.io\pic\image-20220515234503628.png)

使用BootStrap时，Netty会为每个连接创建一个独立的pipeline。对于使用者而言，只需要将自定义的拦截器加入pipeline即可。

pipeline是线程安全的，但是用户需要保证handler的线程安全。如果有Sharable说明是线程安全的。

解决沾包问题：使用解码器

LineBasedFrameDecoder 分隔符解码器

LengthFieldBasedFrameDecoder 数据长度 长度+消息

FixedLengthFrameDecoder 定长消息

## demo

```java
// 1. 启动器，负责组装 netty 组件，启动服务器
        new ServerBootstrap()
            // 2. BossEventLoop, WorkerEventLoop(selector,thread), group 组
            .group(new NioEventLoopGroup())
            // 3. 选择 服务器的 ServerSocketChannel 实现
            .channel(NioServerSocketChannel.class) // OIO BIO
            // 4. boss 负责处理连接 worker(child) 负责处理读写，决定了 worker(child) 能执行哪些操作（handler）
            .childHandler(
                    // 5. channel 代表和客户端进行数据读写的通道 Initializer 初始化，负责添加别的 handler
                new ChannelInitializer<NioSocketChannel>() {
                @Override
                protected void initChannel(NioSocketChannel ch) throws Exception {
                    // 6. 添加具体 handler
                    ch.pipeline().addLast(new LoggingHandler());
                    ch.pipeline().addLast(new StringDecoder()); // 将 ByteBuf 转换为字符串
                    ch.pipeline().addLast(new ChannelInboundHandlerAdapter() { // 自定义 handler
                        @Override // 读事件
                        public void channelRead(ChannelHandlerContext ctx,Object msg) throws Exception {
                            System.out.println(msg); // 打印上一步转换好的字符串
                        }
                    });
                }
            })
```



## channel 

主要方法：

close() 关闭

closeFuture()处理channel的关闭

pipeline 添加处理器

write()写入缓冲区

wrtieAndFlush() 写入后刷出



channelFuture

使用 .sync 方法等待channelFuture建立完成后同步处理结果

或者使用addListener回调



## ByteBuf

java原生提供的ByteBuffer长度固定，api功能有限，容易出问题，故netty实现了ByteBuf。ByteBuf可以扩容，提供更多api功能

ByteBuf提供了一个readerIndex和writerIndex：

![image-20220515114921827](D:\study\lbeco\lbeco.github.io\pic\image-20220515114921827.png)

写时write指针后移，读时readerIndex后移。

调用discardReadBytes可以整理内存

#### ByteBuf内存

ByteBuf可以被分配到直接内存或堆内存中。

![image-20220515145543394](D:\study\lbeco\lbeco.github.io\pic\image-20220515145543394.png)

**堆内存**

优点：内存分配和回收快，可以被JVM自动回收，

缺点：如果进行io读写，需要额外做一次内存复制，将堆内存对应的缓冲区复制到内核channel中

**直接内存**：非堆内存，

优点：和socket channel可以直接读写，速度较快

缺点：在堆外进行内存分配，分配和回收速度会慢一些。

经验表明，io通信线程的读写缓冲区应当使用DirectByteBuf，而后端业务消息的编解码模块应当使用HeapByteBuf

**内存池**

PooledByteBuf从PollArena中获取内存，其是多个chunk和page组成的大块内存区域

每一个chunk会将page组织为一棵avl树，采取深度遍历的方法读写。

**引用计数**

AbstractReferenceCountedByteBuf 实现了ReferenceCounted方法，其提供了：

- 字段refCntUpdater（一个AtomicIntegeFieldUpdater类型变量）保证对refCnt的更新是原子化的

- 第二个是REFCNT_FIELD_OFFSET用于跟踪cefCnt的内存地址（Direct必须这样获取）

- 字段refCnt(有volatile修饰)用于跟踪引用次数


直接内存不在jvm中，需要进行主动释放。当对ByteBuf的引用变为0，则释放该对象。

堆内的ByteBuf可能被jvm回收，可能产生内存泄漏。这里的内存泄漏指的是内存无法归还给缓冲池

**零拷贝**

Netty的零拷贝体现在以下三个方面：

1.Netty接受和发送ByteBuffer采用DIRECT BUFFERS，使用堆外内存直接进行socket读写，不需要先从堆内存拷贝到直接内存中。

2.CompositeByteBuf对外将多个ByteBuf封装成一个ByteBuf，就可以避免在拼合数据的时候还得拷贝

3.文件传输使用零拷贝，调用nio中FileChannel的TransferTo方法(会调用linux中的sendfile)

## Future 和 promise

netty 的 Future 继承自 jdk 的 Future，而 Promise 又对 netty Future 进行了扩展

- jdk Future 只能同步等待任务结束（或成功、或失败）才能得到结果
- netty Future 可以同步等待任务结束得到结果，也可以异步方式得到结果，但**都是要等任务结束**
- netty Promise 不仅有 netty Future 的功能，而且脱离了任务独立存在，**只作为两个线程间传递结果的容器**



## Netty小问题

### Netty 为什么这么快： 

Netty采用io多路复用非阻塞io模型，数据存储在ByteBuffer 堆外内存中，可以进行零拷贝以加快io

Netty的线程模型是无锁化的，可以减少锁从而获取更好的性能。

### Netty的数据存在哪里：

netty将数据存储在ByteBuf中，Netty 使用 reference-counting(引用计数)来判断何时可以释放 ByteBuf 或 ByteBufHolder 和其他相关资源。用完要记得释放

ByteBuf默认是存储在堆外内存中



## 序列化协议

**protobuf**

语言无关的序列化协议

![img](https://pics0.baidu.com/feed/3ac79f3df8dcd1004cbfd579b965ad19b8122fbb.jpeg?token=01c9630731371aea3229124dc8582e86)
