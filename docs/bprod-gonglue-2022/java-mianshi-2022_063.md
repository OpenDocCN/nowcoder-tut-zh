# 第七章 第 9 节 Java-计算机网络-9

> 原文：[`www.nowcoder.com/tutorial/10070/3bdc53d3788f45ff81c4dd8723c8b88a`](https://www.nowcoder.com/tutorial/10070/3bdc53d3788f45ff81c4dd8723c8b88a)

#### 1.52 有大量的 TIME_WAIT 状态怎么办？

**参考回答**

1.  time_wait 状态的影响

    ​ TCP 连接中，主动发起关闭连接的一端，会进入 time_wait 状态，time_wait 状态，默认会持续 2 MSL（报文的最大生存时间），一般是 2x2 mins，time_wait 状态下，TCP 连接占用的端口，无法被再次使用，TCP 端口数量，上限是 6.5w（65535，16 bit），**大量 time_wait 状态存在，会导致新建 TCP 连接会出错，address already in use : connect 异常**。

2.  **解决办法**

    （1）**客户端**：HTTP 请求的头部，connection 设置为 keep-alive，保持存活一段时间：现在的浏览器，一般都这么进行了 。

    （2）**服务器端**

    ​ a. 允许 time_wait 状态的 socket 被**重用**

    ​ b. 缩减 time_wait 时间，设置为 1 MSL（即，2 mins）

**答案解析**

​ 无

#### 1.53 请介绍 socket 通信的具体步骤。

**参考回答**

​ sockets（套接字）编程有三种：**流式套接字（SOCK_STREAM），数据报套接字（SOCK_DGRAM），原始套接字（SOCK_RAW）**；基于 TCP 的 socket 编程是采用的流式套接字。

1.  服务器端编程的步骤

    （1）加载套接字库，创建套接字(WSAStartup()/socket())；

    （2）绑定套接字到一个 IP 地址和一个端口上(bind())；

    （3）将套接字设置为监听模式等待连接请求(listen())；

    （4）请求到来后，接受连接请求，返回一个新的对应于此次连接的套接字(accept())；

    （5）用返回的套接字和客户端进行通信(send()/recv())；

    （6）返回，等待另一连接请求；

    （7）关闭套接字，关闭加载的套接字库(closesocket()/WSACleanup())。

2.  客户端编程的步骤：

    （1）加载套接字库，创建套接字(WSAStartup()/socket())；

    （2）向服务器发出连接请求(connect())；

    （3）和服务器端进行通信(send()/recv())；

    （4）关闭套接字，关闭加载的套接字库(closesocket()/WSACleanup())。

**答案解析**

```cpp
//代码实例（服务器）
#include <stdio.h>
#include <Winsock2.h>
void main()
{
 WORD wVersionRequested;
 WSADATA wsaData;
 int err;

 wVersionRequested = MAKEWORD( 1, 1 );

 err = WSAStartup( wVersionRequested, &wsaData );
 if ( err != 0 ) {
  return;
 }

 if ( LOBYTE( wsaData.wVersion ) != 1 ||
        HIBYTE( wsaData.wVersion ) != 1 ) {
  WSACleanup( );
  return;
 }
 SOCKET sockSrv=socket(AF_INET,SOCK_STREAM,0);

 SOCKADDR_IN addrSrv;
 addrSrv.sin_addr.S_un.S_addr=htonl(INADDR_ANY);
 addrSrv.sin_family=AF_INET;
 addrSrv.sin_port=htons(6000);

 bind(sockSrv,(SOCKADDR*)&addrSrv,sizeof(SOCKADDR));

 listen(sockSrv,5);

 SOCKADDR_IN addrClient;
 int len=sizeof(SOCKADDR);
 while(1)
 {
  SOCKET sockConn=accept(sockSrv,(SOCKADDR*)&addrClient,&len);
  char sendBuf[50];
  sprintf(sendBuf,"Welcome %s to here!",inet_ntoa(addrClient.sin_addr));
  send(sockConn,sendBuf,strlen(sendBuf)+1,0);
  char recvBuf[50];
  recv(sockConn,recvBuf,50,0);
  printf("%s\n",recvBuf);
  closesocket(sockConn);
 }

}
```

```cpp
//代码实例（客户端）
#include <stdio.h>
#include <Winsock2.h>
void main()
{
 WORD wVersionRequested;
 WSADATA wsaData;
 int err;

 wVersionRequested = MAKEWORD( 1, 1 );

 err = WSAStartup( wVersionRequested, &wsaData );
 if ( err != 0 ) {
  return;
 }

 if ( LOBYTE( wsaData.wVersion ) != 1 ||
        HIBYTE( wsaData.wVersion ) != 1 ) {
  WSACleanup( );
  return;
 }
 SOCKET sockClient=socket(AF_INET,SOCK_STREAM,0);

 SOCKADDR_IN addrSrv;
 addrSrv.sin_addr.S_un.S_addr=inet_addr("127.0.0.1");
 addrSrv.sin_family=AF_INET;
 addrSrv.sin_port=htons(6000);
 connect(sockClient,(SOCKADDR*)&addrSrv,sizeof(SOCKADDR));
 send(sockClient,"hello",strlen("hello")+1,0);
 char recvBuf[50];
 recv(sockClient,recvBuf,50,0);
 printf("%s\n",recvBuf);

 closesocket(sockClient);
 WSACleanup();
}
```

#### 1.54 服务端怎么提高处理 socket 连接的性能？

**参考回答**

​ 提高处理 socket 连接的性能，请遵循以下技巧：

1.  最小化报文传输的延时。

2.  最小化系统调用的负载。

3.  为 Bandwidth Delay Product 调节 TCP 窗口。

4.  动态优化 GNU/Linux TCP/IP 栈。

**答案解析**

1.  最小化报文传输的延时。

    ​ 在通过 TCP socket 进行通信时，数据都拆分成了数据块，这样它们就可以封装到给定连接的 TCP payload（指 TCP 数据包中的有效负荷）中了。TCP payload 的大小取决于几个因素（例如最大报文长度和路径），但是这些因素在连接发起时都是已知的。为了达到最好的性能，我们的目标是使用尽可能多的可用数据来填充每个报文。当没有足够的数据来填充 payload 时（也称为最大报文段长度（maximum segment size）或 MSS），TCP 就会采用 Nagle 算法自动将一些小的缓冲区连接到一个报文段中。这样可以通过最小化所发送的报文的数量来提高应用程序的效率，并减轻整体的网络拥塞问题。

2.  最小化系统调用的负载。

    ​ 任何时候通过一个 socket 来读写数据时，都是在使用一个系统调用（system call）。这个调用（例如 read 或 write）跨越了用户空间应用程序与内核的边界。另外，在进入内核之前，该调用会通过 C 库来进入内核中的一个通用函数（system_call()）。从 system_call()中，这个调用会进入文件系统层，内核会在这儿确定正在处理的是哪种类型的设备。最后，调用会进入 socket 层，数据就是在这里进行读取或进行排队从而通过 socket 进行传输的（这涉及数据的副本）。

    ​ 这个过程说明**系统调用不仅仅是在应用程序和内核中进行操作的，而且还要经过应用程序和内核中的很多层次。**这个过程耗费的资源很高，因此调用次数越多，通过这个调用链进行的工作所需要的时间就越长，应用程序的性能也就越低。由于我们**无法避免这些系统调用**，因此**唯一的选择是最小化使用这些调用的次数**。

3.  为 Bandwidth Delay Product 调节 TCP 窗口。

    ​ TCP 的性能取决于几个方面的因素。两个最重要的因素是链接带宽（link bandwidth）（报文在网络上传输的速率）和 往返时间（round-trip time） 或 RTT（发送报文与接收到另一端的响应之间的延时）。这两个值确定了称为 Bandwidth Delay Product（BDP）的内容。

    ​ 给定链接带宽和 RTT 之后，就可以计算出 BDP 的值了，不过这代表什么意义呢？BDP 给出了一种简单的方法来计算理论上最优的 TCP socket 缓冲区大小（其中保存了排队等待传输和等待应用程序接收的数据）。如果缓冲区太小，那么 TCP 窗口就不能完全打开，这会对性能造成限制。如果缓冲区太大，那么宝贵的内存资源就会造成浪费。如果设置的缓冲区大小正好合适，那么就可以完全利用可用的带宽。

4.  动态优化 GNU/Linux TCP/IP 栈。

    ​ 标准的 GNU/Linux 发行版试图对各种部署情况都进行优化。这意味着标准的发行版可能并没有对现有的环境进行特殊的优化。GNU/Linux 提供了很多可调节的内核参数，可以使用这些参数为自己的操作系统进行动态配置。

#### 1.55 介绍一下流量控制和拥塞控制。

**参考回答**

1.  流量控制和拥塞控制定义

    **流量控制**

    ​ 如果发送方把数据发送得过快，接收方可能会来不及接收，这就会造成数据的丢失。**流量控制**就是让发送方慢点，要让接收方来得及接收。

    **拥塞控制**

    ​ 拥塞控制就是防止过多的数据注入到网络中，这样可以使网络中的路由器或链路不致过载。

2.  流量控制和拥塞控制区别

    ​ 流量控制是**端到端**的控制，例如 A 通过网络给 B 发数据，A 发送的太快导致 B 没法接收(B 缓冲窗口过小或者处理过慢)，这时候的控制就是流量控制，原理是通过滑动窗口的大小改变来实现。

    ​ 拥塞控制是 A 与 B 之间的网络发生堵塞导致传输过慢或者丢包，来不及传输。防止过多的数据注入到网络中，这样可以使网络中的路由器或链路不至于过载。拥塞控制是一个**全局性**的过程，涉及到所有的主机、路由器，以及与降低网络性能有关的所有因素。

3.  TCP 流量控制解决方法

    ​ TCP 的流量控制是利用**滑动窗口机制**实现的，接收方在返回的数据中会包含自己的接收窗口的大小，以控制发送方的数据发送。

4.  TCP 拥塞控制解决方法

    ​ TCP 拥塞控制的四种算法：**慢开始、拥塞避免、快重传、快恢复。**

    （1）**慢开始算法：**当主机开始发送数据时，并不清楚网络的负载情况，所以由小到大逐渐增大拥塞窗口，每经过一个传输轮次没有出现超时就将拥塞窗口加倍。同时还需要设置一个慢开始门限，在拥塞窗口小于慢开始门限时使用慢开始算法，大于慢开始门限时，使用拥塞避免算法；

    （2）**拥塞避免算法：**在拥塞窗口大于慢开始门限时，让拥塞窗口按线性规律缓慢增长。即每经过一个传输轮次，拥塞窗口增大一个 MSS 最大报文段尺寸。（拥塞避免并非完全能够避免拥塞，只是使网络比较不容易出现拥塞）

    （3）**快重传算法：**使发送方今早知道发生了个别报文段丢失，并不是出现网络拥塞。

    要求接受不要登塞自己发送数据时才进行捎带确认，而是立即发送确认，即使收到了失序的报文段也要立即发出对已收到报文段的重复确认。而发送方一旦受到三个连续的重读确认，就将相应的报文段立即重传。

    （4）**快恢复算法：**发送方知道只有个别报文段丢失而不是网络拥塞时，不启动慢开始算法，而是执行快恢复算法，将慢开始门限和拥塞窗口值调整为当前窗口的一半，开始执行拥塞避免算法

**答案解析**

​ 无