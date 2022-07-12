# Java调优

## java调优工具

jps
显示当前所有java进程pid的命令

jstack
查看线程堆栈 用于排查线程是否死锁，查看内存使用情况

jmap
显示java内存信息，获得stack dump文件

jhat
导出dump文件的情况，启动一个服务器查看

jstat
jvm统计工具，查看jvm里的各个区的情况

jinfo
查看jvm的拓展参数

arthas 

调优工具

## java参数

- -Xmx 最大可用内存

- -Xms 初始内存。通常把最大、最小设置为相同的值

- -XX:NewRatio 新生代和老年代比例
- -XX:SurvivorRatio 新生代中 Eden 与 Survivor 的比值。默认值为 8

## 调优流程

**内存不足：**

jmap查看占用

jstat -gc 查看gc的情况

jstack查看线程堆栈，判断是否死锁

问题较大，则进行内存dump

**死锁**

`jps -l` 用来查看当前运行的进程号

再使用`jstack 进程号`命令查看当前进程的[堆栈](https://so.csdn.net/so/search?q=堆栈&spm=1001.2101.3001.7020)信息

## arthas

todo