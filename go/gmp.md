# go



Go 语言基于GMP 模型实现用户态线程
• G：表示goroutine，每个goroutine 都有自己的栈空间，定时器，初始化的栈空间在2k 左右，空间会随着需求增长。
• M：抽象化代表内核线程，记录内核线程栈信息，当goroutine 调度到线程时，使用该goroutine 自己的栈信息。
• P：代表调度器，负责调度goroutine，维护一个本地goroutine 队列，M 从P 上获得goroutine 并执行，同时还负责部分内存的管理。


![img](https://pic3.zhimg.com/80/v2-d1e28a8bf349b6a904ac393b8ab415b2_1440w.jpg)