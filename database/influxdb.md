# influxdb

## 基础结构

measurement相当于表名

由 time， field key 和 tag key作为表头

time：时间戳

field key：不可索引的信息，必须要有

tag key：可索引的信息

![image-20220604192232296](D:\study\lbeco\lbeco.github.io\database\influxdb.assets\image-20220604192232296.png)



命令举例：

```sql
SELECT * FROM "census" WHERE "butterflies" = 1
```



## 设计原理

https://zhuanlan.zhihu.com/p/415799237

优：增、删、改操作飞快，写吞吐量极大。

缺：读操作性能相对被弱化；不擅长区间范围的读操作； 归并操作较耗费资源。

在一开始，influxdb使用的是LSM树，LSM树能让我们进行顺序写磁盘，从而大幅提升写操作，作为代价的是牺牲了一些读性能。



influxdb中文文档https://jasper-zhang1.gitbooks.io/influxdb/content/

influxdb引擎https://blog.csdn.net/cymm_liu/article/details/88344215

influx使用的是TSM(Time-Structured Merge Tree）存储引擎，以代替LSM