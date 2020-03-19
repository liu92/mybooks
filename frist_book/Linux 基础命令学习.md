## Linux 基础命令学习

1、pwd 查看路径

2、jps 查看有哪些进程在运行

3、top查看cup使用情况

4、ps aux|grep java 查询运行服务

5、ls 显示目录内容， ls -a 显示所以内容，包括隐藏文件

6、**du---显示文件或目录大小**

7、**df---  显示档案系统的状况；主要用来了解系统中已经挂载的各个文件系统的磁盘使用情况**

-h 显示更易读的容量单位
-T 显示文件系统的类型

8、





100、现代处理器一般具有多个层次的高速缓存。 使用lscup 查看处理器的高速缓存层次

相应的高速缓存通常被称为一级缓存(L1 Cache), 二级缓存(L2 Cache), 三级缓存(L3 Cache). 一级缓存通常包含两部分，其中一部分用于存储指令(Lli)，另一部分用于存储数据(Lld).  距离处理器越近的高速缓存，其存取速率越快，制作成本越高，因此其容量也越小。距离处理器越远的高速缓存，其存储速率越慢，而存储容量则相应的增大。

```
[root@re195 ~]# lscpu
Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0-7
Thread(s) per core:    1
Core(s) per socket:    2
Socket(s):             4
NUMA node(s):          1
Vendor ID:             GenuineIntel
CPU family:            6
Model:                 63
Stepping:              2
CPU MHz:               1899.998
BogoMIPS:              3799.99
Hypervisor vendor:     VMware
Virtualization type:   full
L1d cache:             32K
L1i cache:             32K
L2 cache:              256K
L3 cache:              15360K
NUMA node0 CPU(s):     0-7
```

