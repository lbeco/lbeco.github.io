子进程继承父进程<https://blog.csdn.net/ouyangjinbin/article/details/51107135>
线程/进程in linux<https://zhuanlan.zhihu.com/p/93553600>
线程只是和父进程共享了打开的文件和文件系统信息，共享了地址空间和信号处理函数。本质上和进程没有区别
linux cfs调度<https://blog.csdn.net/qq_31098037/article/details/78125893>
tlb<https://zhuanlan.zhihu.com/p/108425561>

僵尸进程 <https://www.cnblogs.com/chenjianhong/p/4144513.html> 该文十分搞笑

通知父进程该进程应当结束，或直接结束其父进程

Linux进程的六种状态（R、S、D、T、Z、X） <https://blog.csdn.net/wjd_231/article/details/81432999>

R --- TASK_RUNNING（可执行状态）
S --- TASK_INTERRUPTIBLE（可中断的睡眠状态）
D --- TASK_UNINTERRUPTIBLE（不可中断的睡眠状态）
T --- TASK_STOPPED或TASK_TRACED（暂停状态或跟踪状态）
Z --- TASK_DEAD - EXIT_ZOMBIE（退出状态，进程成为僵尸进程）
X --- TASK_DEAD - EXIT_DEAD（退出状态，进程即将被销毁）



伙伴算法https://blog.csdn.net/csdn_kou/article/details/82355452

