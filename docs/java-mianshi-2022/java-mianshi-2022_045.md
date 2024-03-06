# 第六章 第 3 节 Java-操作系统-3

> 原文：[`www.nowcoder.com/tutorial/10070/fdf40a8fa001481c862d4085ca8fdc12`](https://www.nowcoder.com/tutorial/10070/fdf40a8fa001481c862d4085ca8fdc12)

#### 1.11 Linux 中，如何通过端口查进程，如何通过进程查端口？

**参考回答**

1.  **linux 下通过进程名查看其占用端口**：
    （1）先查看进程 pid

    ```cpp
    ps -ef | grep 进程名
    ```

    （2）通过 pid 查看占用端口

    ```cpp
    netstat -nap | grep 进程 pid
    ```

2.  **linux 通过端口查看进程**：

    ```cpp
    netstat -nap | grep 端口号
    ```

#### 1.12 请你说说 ping 命令？

**参考回答**

Linux ping 命令用于检测主机。

**执行 ping 指令会使用 ICMP 传输协议，发出要求回应的信息，若远端主机的网络功能没有问题，就会回应该信息，因而得知该主机运作正常。**

**答案解析**

语法：

```cpp
ping [-dfnqrRv][-c<完成次数>][-i<间隔秒数>][-I<网络界面>][-l<前置载入>][-p<范本样式>][-s<数据包大小>][-t<存活数值>][主机名称或 IP 地址]
```

参数说明：

```cpp
-d 使用 Socket 的 SO_DEBUG 功能。
-c<完成次数> 设置完成要求回应的次数。
-f 极限检测。
-i<间隔秒数> 指定收发信息的间隔时间。
-I<网络界面> 使用指定的网络接口送出数据包。
-l<前置载入> 设置在送出要求信息之前，先行发出的数据包。
-n 只输出数值。
-p<范本样式> 设置填满数据包的范本样式。
-q 不显示指令执行过程，开头和结尾的相关信息除外。
-r 忽略普通的 Routing Table，直接将数据包送到远端主机上。
-R 记录路由过程。
-s<数据包大小> 设置数据包的大小。
-t<存活数值> 设置存活数值 TTL 的大小。
-v 详细显示指令的执行过程。
```

实例：

```cpp
检测是否与主机连通

# ping www.w3cschool.cc //ping 主机
PING aries.m.alikunlun.com (114.80.174.110) 56(84) bytes of data.
64 bytes from 114.80.174.110: icmp_seq=1 ttl=64 time=0.025 ms
64 bytes from 114.80.174.110: icmp_seq=2 ttl=64 time=0.036 ms
64 bytes from 114.80.174.110: icmp_seq=3 ttl=64 time=0.034 ms
64 bytes from 114.80.174.110: icmp_seq=4 ttl=64 time=0.034 ms
64 bytes from 114.80.174.110: icmp_seq=5 ttl=64 time=0.028 ms
64 bytes from 114.80.174.110: icmp_seq=6 ttl=64 time=0.028 ms
64 bytes from 114.80.174.110: icmp_seq=7 ttl=64 time=0.034 ms
64 bytes from 114.80.174.110: icmp_seq=8 ttl=64 time=0.034 ms
64 bytes from 114.80.174.110: icmp_seq=9 ttl=64 time=0.036 ms
64 bytes from 114.80.174.110: icmp_seq=10 ttl=64 time=0.041 ms

--- aries.m.alikunlun.com ping statistics ---
10 packets transmitted, 30 received, 0% packet loss, time 29246ms
rtt min/avg/max/mdev = 0.021/0.035/0.078/0.011 ms

//需要手动终止 Ctrl+C
指定接收包的次数

# ping -c 2 www.w3cschool.cc
PING aries.m.alikunlun.com (114.80.174.120) 56(84) bytes of data.
64 bytes from 114.80.174.120: icmp_seq=1 ttl=54 time=6.18 ms
64 bytes from 114.80.174.120: icmp_seq=2 ttl=54 time=15.4 ms

--- aries.m.alikunlun.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1016ms
rtt min/avg/max/mdev = 6.185/10.824/15.464/4.640 ms

//收到两次包后，自动退出
多参数使用

# ping -i 3 -s 1024 -t 255 g.cn //ping 主机
PING g.cn (203.208.37.104) 1024(1052) bytes of data.
1032 bytes from bg-in-f104.1e100.net (203.208.37.104): icmp_seq=0 ttl=243 time=62.5 ms
1032 bytes from bg-in-f104.1e100.net (203.208.37.104): icmp_seq=1 ttl=243 time=63.9 ms
1032 bytes from bg-in-f104.1e100.net (203.208.37.104): icmp_seq=2 ttl=243 time=61.9 ms

--- g.cn ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 6001ms
rtt min/avg/max/mdev = 61.959/62.843/63.984/0.894 ms, pipe 2
[root@linux ~]# 

//-i 3 发送周期为 3 秒 -s 设置发送包的大小 -t 设置 TTL 值为 255
```

#### 1.13 什么是协程？

**参考回答**

**协程**：协程是微线程，在子程序内部执行，可在子程序内部中断，转而执行别的子程序，在适当的时候再返回来接着执行。

**答案解析**

1.  **线程与协程的区别：**

    （1）协程执行效率极高。协程直接操作栈基本没有内核切换的开销，所以上下文的切换非常快，切换开销比线程更小。

    （2）协程不需要多线程的锁机制，因为多个协程从属于一个线程，不存在同时写变量冲突，效率比线程高。

    （3）一个线程可以有多个协程。

2.  **协程的优势：**

    （1）**协程调用跟切换比线程效率高**：协程执行效率极高。协程不需要多线程的锁机制，可以不加锁的访问全局变量，所以上下文的切换非常快。

    （2）**协程占用内存少**：执行协程只需要极少的栈内存（大概是 4～5KB），而默认情况下，线程栈的大小为 1MB。

    （3）**切换开销更少**：协程直接操作栈基本没有内核切换的开销，所以切换开销比线程少。

#### 1.14 为什么协程比线程切换的开销小？

**参考回答**

（1）协程执行效率极高。协程直接操作栈基本没有内核切换的开销，所以上下文的**切换非常快**，切换开销比线程更小。

（2）协程不需要多线程的锁机制，因为多个协程从属于一个线程，不存在同时写变量冲突，效率比线程高。**避免了加锁解锁的开销。**

#### 1.15 线程和进程的区别？

**参考回答**

（1）一个线程从属于一个进程；一个进程可以包含多个线程。

（2）一个线程挂掉，对应的进程挂掉；一个进程挂掉，不会影响其他进程。

（3）进程是系统资源调度的最小单位；线程 CPU 调度的最小单位。

（4）进程系统开销显著大于线程开销；线程需要的系统资源更少。

（5）进程在执行时拥有独立的内存单元，多个线程共享进程的内存，如代码段、数据段、扩展段；但每个线程拥有自己的栈段和寄存器组。

（6）进程切换时需要刷新 TLB 并获取新的地址空间，然后切换硬件上下文和内核栈，线程切换时只需要切换硬件上下文和内核栈。

（7）通信方式不一样。

（8）进程适应于多核、多机分布；线程适用于多核