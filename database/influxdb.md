influxdb

measurement相当于表名

由 time， field key 和 tag key作为表头

time：时间戳

field key：不可索引的信息，必须要有

tag key：可索引的信息

命令举例：

```sql
SELECT * FROM "census" WHERE "butterflies" = 1
```

优先考虑create和read数据的性能而不是update和delete



influxdb中文文档https://jasper-zhang1.gitbooks.io/influxdb/content/

influxdb引擎https://blog.csdn.net/cymm_liu/article/details/88344215