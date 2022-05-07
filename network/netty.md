https://nyimac.gitee.io/2021/04/25/Netty%E5%9F%BA%E7%A1%80/#Netty



## eventloop

eventLoopGroup事件循环组 里面有数个工具线程（eventLoop）

典型的就是 NioEventLoopGroup 和 DefaultEventLoopGroup 

DefaultEventLoopGroup  不能处理io事件

EventLoop默认的进程数是核心数×2，最少是1

可以划分不同任务的不同eventloopGroup（boss和worker）在.group中指定即可。boss管理channel上的事件，worker负责读写

可以指定单独的EventLoopGroup来处理

![image-20220116163157840](C:/Users/linbc/AppData/Roaming/Typora/typora-user-images/image-20220116163157840.png)



一个channel会和对应的EventLoop绑定





netty 最简单的demo:



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





channel 

主要方法：

close() 关闭

closeFuture()处理channel的关闭

pipeline 添加处理器

write()写入缓冲区

wrtieAndFlush() 写入后刷出



channelFuture

使用 .sync 方法等待channelFuture建立完成后同步处理结果

或者使用addListener回调





## Future 和 promise

netty 的 Future 继承自 jdk 的 Future，而 Promise 又对 netty Future 进行了扩展

- jdk Future 只能同步等待任务结束（或成功、或失败）才能得到结果
- netty Future 可以同步等待任务结束得到结果，也可以异步方式得到结果，但**都是要等任务结束**
- netty Promise 不仅有 netty Future 的功能，而且脱离了任务独立存在，**只作为两个线程间传递结果的容器**



Netty小问题：

Netty 为什么这么快： 

Netty采用io多路复用非阻塞io模型，数据存储在ByteBuffer 堆外内存中，可以进行零拷贝以加快io

Netty的线程模型是无锁化的，可以减少锁从而获取更好的性能。

Netty的数据存在哪里：

netty将数据存储在ByteBuf中，Netty 使用 reference-counting(引用计数)来判断何时可以释放 ByteBuf 或 ByteBufHolder 和其他相关资源。用完要记得释放

ByteBuf默认是存储在堆外内存中





