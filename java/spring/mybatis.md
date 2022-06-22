# Mybatis

**#{}是参数占位符** 

**${}是变量占位符，进行静态文本替换**



### 基本原理：

https://www.cnblogs.com/jingzh/p/14507385.html

`mybatis`应用程序通过`SqlSessionFactoryBuilder`从`mybatis-config.xml`配置文件中构建出`SqlSessionFactory`，然后，`SqlSessionFactory`的实例直接开启一个`SqlSession`，再通过`SqlSession`实例获得`Mapper对象`并运行`Mapper`映射的`SQL语句`，完成对数据库的`CRUD`和事务提交，之后关闭`SqlSession`。如下图所示：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210309085006377.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIwNjAwMzM=,size_16,color_FFFFFF,t_70)



## 缓存

### 一级缓存

![在这里插入图片描述](D:\study\lbeco\lbeco.github.io\java\spring\mybatis.assets\watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6KW_6LSi5b2t5LqO5pmP,size_20,color_FFFFFF,t_70,g_se,x_16.png)

每个SqlSession中持有一个执行器Executor，每个执行器中有一个Local Cache，当用户发起查询时，mybatis根据当前执行的语句生成mapperStatement,在Local Cache中进行查询，如果缓存命中的话，直接返回给用户，如果没有命中，查询数据库，结果写入Local Cache中，最后返回给用户。

如果 sqlSession 执行了commit操作(增删改) ，则mybatis会清空一级缓存的数据 ，这样就能让我们的数据是最新的，避免产生脏读。

### 二级缓存

mybatis中二级缓存是mapper级别的缓存，默认是关闭的。
对于一个mapper下的不同的SQLSession可以共享二级缓存，不同的mapper是相互隔离的。
二级缓存的特点是需要打开二级缓存的配置，并且需要映射的java类需要实现序列化。序列化是因为二级的缓存的存储方式多种多样，有可能存储在内存中，也可能储存到磁盘中。

## 映射

todo