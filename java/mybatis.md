# Mybatis

#{}是参数占位符 ${}是变量占位符，进行静态文本替换



### 基本原理：

https://www.cnblogs.com/jingzh/p/14507385.html

`mybatis`应用程序通过`SqlSessionFactoryBuilder`从`mybatis-config.xml`配置文件中构建出`SqlSessionFactory`，然后，`SqlSessionFactory`的实例直接开启一个`SqlSession`，再通过`SqlSession`实例获得`Mapper对象`并运行`Mapper`映射的`SQL语句`，完成对数据库的`CRUD`和事务提交，之后关闭`SqlSession`。如下图所示：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210309085006377.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIwNjAwMzM=,size_16,color_FFFFFF,t_70)