# Docker

docker利用Namespace、CGroup进行隔离，创造高性能的容器

### 常见命令

 docker pull:拉取或者更新指定镜像； docker push:将镜像推送至远程仓库； docker images:列出所有镜像； docker rmi:删除镜像；  

### namespace

namespace是linux中pid独有的命名空间，PID,IPC,Network属于一个特定的namespace，从操作系统上看这些都是透明的



### CGroup

只有namespace的话无法控制资源的消耗，所以使用cgroups （controll groups）来限制容器中的进程允许使用的系统资源，实践中可以用来限制进程的资源，分配内存/带宽/磁盘



### 和虚拟机的对比

docker相比虚拟机，速度更快，占用资源少。但是其共用内核，所以内核出问题会造成崩溃。