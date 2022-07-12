

# Raft算法

raft算法基本操作：

![image-20220518141821221](D:\study\lbeco\lbeco.github.io\pic\image-20220518141821221.png)



## 主旨

三个角色：Leader Follower Candidate





figure8问题：

![image-20220705013705737](D:\study\lbeco\lbeco.github.io\分布式\raft.assets\image-20220705013705737-16569562272341.png)

leader不允许提交之前term的日志。

上图中：

a：S1挂掉

b：S5当选

c：S1复活，分发两个log后commit了log，挂掉。注意此时S1是任期4了

d：S5覆盖掉log2，导致S1的commit白给

e：故按照**leader不允许提交之前term的日志**定律，新建一个log4才能commit，这样也能commit掉log2，并且阻止S5当选。
