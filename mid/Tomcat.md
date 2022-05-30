# Tomcat

![img](https://img-blog.csdnimg.cn/20190419092713330.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NzYxODMx,size_16,color_FFFFFF,t_70)

1、Tomcat中最顶层的容器是Server，代表着整个服务器，一个Server可以包含至少一个Service，用于具体提供服务。

2、Service主要包含两个部分：Connector和Container。 Tomcat 的心脏就是这两个组件，这两个组件的作用：Connector用于处理连接相关的事情，并提供Socket与Request和Response相关的转化;Container用于封装和管理Servlet，以及具体处理Request请求。

3、一个Tomcat中只有一个Server，一个Server可以包含多个Service，一个Service只有一个Container，但是可以有多个Connectors，因为一个服务可以有多个连接，如同时提供Http和Https链接，也可以提供向相同协议不同端口的连接。

4、多个 Connector 和一个 Container 就形成了一个 Service，有了 Service 就可以对外提供服务了，但是 Service 还要一个生存的环境，必须要有人能够给她生命、掌握其生死大权，那就非 Server 莫属了！所以整个 Tomcat 的生命周期由 Server 控制。另外，上述的包含关系或者说是父子关系，都可以在tomcat的conf目录下的server.xml配置文件中看出。

5、Server标签设置的端口号为8005，shutdown=”SHUTDOWN” ，表示在8005端口监听“SHUTDOWN”命令，如果接收到了就会关闭Tomcat。一个Server有一个Service，当然还可以进行配置，一个Service有多个，Service左边的内容都属于Container的，Service下边是Connector。