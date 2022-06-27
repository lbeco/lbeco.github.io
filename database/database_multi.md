# 多数据库

## 主从同步

master与slave之间实现整个复制过程主要由三个线程完成：两个（SQL线程和IO线程）在slave端，一个（IO线程）在master端。

slave端的IO线程负责读取master的**binlog**内容到中继日志relay log里；

slave端的SQL线程负责从relay log日志里读出binlog内容，并更新到slave的数据库里。

master端线程负责发送binlog

## 分布式数据库

tidb

基于paxos的数据库
