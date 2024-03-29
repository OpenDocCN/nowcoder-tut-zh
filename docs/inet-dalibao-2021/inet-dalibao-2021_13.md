# 第三章 第 5 节 测试方向优质面经合集

# **篇一：考研失败春招测试上岸微信、字节**补一篇超全面经（附答案）****

**作者：夜猫 shiwo

结果：春招收获腾讯微信质量管理工程师、字节跳动的测试开发、深信服的软件测试等

## **一、数据结构**

 **### **（一）数组和链表**

 **1、存储空间上：数组在内存中是连续的，从栈中分配空间；链表是可以不连续的，从堆中分配空间。

2、在查询、访问方式上：数组可以随机访问其中的元素，查找速度相对较快，链表则必须是顺序访问，不能随机访问。

3、空间的使用上：链表对内存空间的利用率较高，可扩展性高；数组则不能，数组的空间大小是固定的，不适合动态存储，不方便动态添加。

4、添加或删除元素时，数组比链表慢，因为数组要移动大量的元素，而链表只需修改指针即可。****  ****### **（二）堆和栈的区别**

 **1、申请方式：stack 由系统自动分配。 堆;需要程序员自己申请，并指明大小。

2、申请效率的比较：栈:由系统自动分配，速度较快。堆:是由 new 分配的内存，一般速度比较慢，而且容易产生内存碎片,不过用起来最方便。

3、申请大小的限制：栈顶的地址和栈的最大容量是系统预先规定好的，在 WINDOWS 下，栈的大小是 2M，超出剩余空间会溢出。堆的大小受限于计算机系统中有效的虚拟内存。

4、数据结构区别：堆可以被看成是一棵树，如：堆排序。栈：一种先进后出的数据结构。

5、缓存方式区别：栈使用的是一级缓存，它们通常都是被调用时处于存储空间中，调用完毕立即释放。堆则是存放在二级缓存中，生命周期由虚拟机的垃圾回收算法来决定。

### **（三）快排**

 **通常我们将数组的第一个元素定义为比较元素，通过一趟排序将要排序的数据分割成独立的两部分，其中一部分的所有数据都比另外一部分的所有数据都要小，然后再按此方法对这两部分数据分别进行快速排序，整个排序过程可以递归进行，以此达到整个数据变成有序序列。****  ****## **二、数据库**

 **### **（一）索引优化**

a.对较小的数据列使用索引,这样会使索引文件更小,同时内存中也可以装载更多的索引键。

b.对 where,on,group by,order by 中出现的列使用索引。

c.并不是所有索引对查询都有效，当索引列有大量数据重复时,查询可能不会去利用索引。

d.索引并不是越多越好。提高查询效率，但同时也降低了 insert 及 update 的效率，因为可能会重建索引。

e.应尽量避免在 where 子句中使用!=或<>操作符或使用 or 来连接条件，否则引擎放弃使用索引而进行全表扫描。

f.Where 后的查询字段尽量减少使用函数或者表达式操作，因为会造成索引失效。

g.尽量的扩展索引，不要新建索引。

### **（二）数据库优化**

### **（三）索引的最左优先原则**

 **### **（四）数据库左连、右连和内连**

左联：首先取出 a 表中所有数据,然后再加上与 a,b 匹配的的数据。

内联：两个表 a,b 相连接,要取出 id 相同的字段。

右联：指的是首先取出 b 表中所有数据,然后再加上与 a,b 匹配的的数据。

### **（五）存储引擎以及他们之间的区别**

InnoDB：提供了良好的事务处理、崩溃修复能力和并发控制。为了维护数据完整性，InnoDB 还支持外键完整性约束。

支持自动增加列 AUTO_INCREMENT（自动增长列）属性。InnoDB 提供行级锁，大幅度提高了多用户并发操作的性能。

缺点是读写效率较差，占用的数据空间相对较大。使用场景，如：银行（对事务的完整性要求比较高），售票（要求实现并发控制）。

MyISAM：使用 B+Tree 作为索引结构，叶节点的 data 域存放的是数据记录的地址。

拥有较高的插入、查询速度，但不支持事物。基于 MyISAM 存储引擎的表支持 3 种不同的存储格式。包括静态型、动态型和压缩型。其中，静态型是 MyISAM 的默认存储格式，它的字段是固定长度的；动态型包含变长字段，记录的长度不是固定的；压缩型需要用到 myisampack 工具，占用的磁盘空间较小。

### **（六）B+**

B+树是在 B 树的基础上改造，它的数据都在叶子节点，同时，叶子节点之间还加了指针形成链表。

B+树所有的数据都在叶子节点，不用跨层，同时有链表结构，只需要找到首尾，通过链表就能把所有数据查询出来。

### **（七）数据库索引有哪些类型**

 **### **（八）范式、第三范式**

 **### **（九）全文索引跟其他索引的区别，怎么实现的**

 **### **（十）查重复‘字段’，统计重复次数：**

 **SELECT [字段],COUNT(0) AS 重复次数 FROM [表名] GROUP BY [字段] HAVING COUNT([字段]) > 1 order by 重复次数。

### **（十一）查找薪资第二高：**

select (select DISTINCT Salary from Employee ORDER BY Salary DESC limit 1 offset 1) as Second。

### **（十二）一个文件夹目录，获取该目录下以’ppt‘结尾的文件：find dir -name "*.txt"**

查找目录：find /（查找范围）-name '查找关键字' -type d。

查找文件：find /（查找范围）-name 查找关键字-print。

### **（十三）inux 里 source、sh、bash、./有什么区别**

1、source：source a.sh，在当前 shell 内去读取、执行 a.sh，而 a.sh 不需要有"执行权限"。

2、sh/bash:打开一个 subshell 去读取、执行 a.sh，而 a.sh 不需要有"执行权限".通常在 subshell 里运行的脚本里设置变量，不会影响到父 shell 的。

3、 打开一个 subshell 去读取、执行 a.sh，但 a.sh 需要有"执行权限",可以用 chmod +x 添加执行权限。

### **（十四）LInux 的同步和异步锁**

 **### **（十五）grep 进行文件内容匹配工作是用到的参数主 zha 要有两个，分别是**

1.取出两个文件中的相同部分内容“-wf”参数。

2.取出两个文件中的不同部分内容“-wvf”参数。**************  ****## **三、计算机网络**

 **### **1、SSL 怎么加密的？**

非对称加密算法：RSA，DSA/DSS

对称加密算法：AES，RC4，3DES

HASH 算法：MD5，SHA1，SHA256

其中非对称加密算法用于在握手过程中加密生成的密码，对称加密算法用于对真正传输的数据进行加密，而 HASH 算法用于验证数据的完整性。由于浏览器生成的密码是整个数据加密的关键，因此在传输的时候使用了非对称加密算法对其加密。

非对称加密算***生成公钥和私钥，公钥只能用于加密数据，因此可以随意传输，而网站的私钥用于对数据进行解密，所以网站都会非常小心的保管自己的私钥，防止泄漏。

### **2、HTTPS 的具体流程**

（1）浏览器将自己支持的一套加密规则发送给网站。

（2）网站从中选出一组加密算法与 HASH 算法，并将自己的身份信息以证书的形式发回给浏览器。证书里面包含了网站地址，加密公钥，以及证书的颁发机构等信息。

（3）获得网站证书之后浏览器要做以下工作：a)验证证书的合法性 b)如果证书受信任，或者是用户接受了不受信的证书，浏览器会生成一串随机数的密码，并用证书中提供的公钥加密。c)使用约定好的 HASH 计算握手消息，并使用生成的随机数对消息进行加密，最后将之前生成的所有信息发送给网站。

（4）网站接收浏览器发来的数据之后要做以下的操作：a)使用自己的私钥将信息解密取出密码，使用密码解密浏览器发来的握手消息，并验证 HASH 是否与浏览器发来的一致。b)使用密码加密一段握手消息，发送给浏览器。

（5）浏览器解密并计算握手消息的 HASH，如果与服务端发来的 HASH 一致，此时握手过程结束，之后所有的通信数据将由之前浏览器生成的随机密码并利用对称加密算法进行加密。

**三次握手：**

1）发送端首先发送一个带有 SYN 标志地数据包给接收方。

2）接收方接收后，回传一个带有 SYN/ACK 标志的数据包传递确认信息，表示我收到了。

3）最后，发送方再回传一个带有 ACK 标志的数据包，代表我知道了，表示’握手‘结束。

**四次挥手：**

1）第一次挥手：Client 发送一个 FIN，用来关闭 Client 到 Server 的数据传送，Client 进入 FIN_WAIT_1 状态。

2）第二次挥手：Server 收到 FIN 后，发送一个 ACK 给 Client，确认序号为收到序号+1（与 SYN 相同，一个 FIN 占用一个序号），Server 进入 CLOSE_WAIT 状态。

3）第三次挥手：Server 发送一个 FIN，用来关闭 Server 到 Client 的数据传送，Server 进入 LAST_ACK 状态。

4）第四次挥手：Client 收到 FIN 后，Client 进入 TIME_WAIT 状态，接着发送一个 ACK 给 Server，确认序号为收到序号+1，Server 进入 CLOSED 状态，完成四次挥手。

为什么是四次？当被动方收到主动方的 FIN 报文通知时，它仅仅表示主动方没有数据再发送给被动方了。但未必被动方所有的数据都完整的发送给了主动方，所以被动方不会马上关闭 SOCKET,它可能还需要发送一些数据给主动方后，再发送 FIN 报文给主动方，告诉主动方同意关闭连接，所以这里的 ACK 报文和 FIN 报文多数情况下都是分开发送的。

**TCP 与 UDP 区别总结：**

1、TCP 面向连接（如打电话要先拨号建立连接）;UDP 是无连接的，即发送数据之前不需要建立连接。

2、TCP 提供可靠的服务。也就是说，通过 TCP 连接传送的数据，无差错，不丢失，不重复，且按序到达;UDP 尽最大努力交付，即不保证可靠交付。

2、TCP 面向字节流，TCP 把数据看成一连串无结构的字节流;UDP 是面向报文的。

UDP 没有拥塞控制，因此网络出现拥塞不会使源主机的发送速率降低（对实时应用很有用，如 IP 电话，实时视频会议等）。

4、每一条 TCP 连接只能是点到点的;UDP 支持一对一，一对多，多对一和多对多的交互通信。

5、TCP 首部开销 20 字节;UDP 的首部开销小，只有 8 个字节。

6、TCP 的逻辑通信信道是全双工的可靠信道，UDP 则是不可靠信道。

**DHCP（动态主机配置协议）是一个局域网的网络协议。**指的是由服务器控制一段 lP 地址范围，客户机登录服务器时就可以自动获得服务器分配的 lP 地址和子网掩码。采用 udp 传输。

1、DHCP Client 以广播的方式发出 DHCP Discover 报文。

2、所有的 DHCP Server 都能够接收到 DHCP Client 发送的 DHCP Discover 报文，所有的 DHCP Server 都会给出响应，向 DHCP Client 发送一个 DHCP Offer 报文。

3、DHCP Client 只能处理其中的一个 DHCP Offer 报文。

3、DHCP Server 收到 DHCP Request 报文后，判断选项字段中的 IP 地址是否与自己的地址相同。如果相同，DHCP Server 就会向 DHCP Client 响应一个 DHCP ACK 报文。

5、DHCP Client 接收到 DHCP ACK 报文后，检查 DHCP Server 分配的 IP 地址是否能够使用。

6、DHCP Client 在成功获取 IP 地址后，随时可以通过发送 DHCP Release 报文释放自己的 IP 地址，DHCP Server 收到 DHCP Release 报文后，会回收相应的 IP 地址并重新分配。**  **## **四、算法题**

1、最长公共子序列；

2、两个链表的第一个公共节点；

3、输入一串由 ABCD 四个字随机组成的字符串和一个整数 k，返回字符串种前 k 个字的顺序重复了几次；

4、判断 ip 地址的合法性。针对编程的测试用例；

5、鸡兔同笼，一半的兔子伸起一半的脚，输入地上有几只脚，列出所有的可能性（兔子是基数则整除 2）；

6、自动混洗扑克牌；

7、手写单例模式；

8、手撕：动态规划摸小球怎么降低空间复杂度，怎么设计测试用例测试该程序；

9、数组中找到有没有三个数字从左到右大小依次递增，返回 true false；

10、就是找数组里面左边最大，右边最小的数字那道题。（两个数组实现，一个记录 leftMax，另一个记录 rightMin）；

11、长度为 n 的数组，求未出现的最小正整数；

12、每 k 个一组反转链表（力扣 15）；

13、堆排序；

14、Topk 问题：从 N 个数中找出第 K 大的数；

15、大数相加（剑指原题）；

16、字符串中只出现一次的字符、找出数组中最小的四个数字；

17、算法题：求出数组中出现次数最多且最大的数；

18、手撕：实现栈的功能；

19、给定一个 n * m 的矩阵 a，从左上角开始每次只能向右或者向下走，最后到达右下角的位置，路径上所有的数字累加起来就是路径和，输出所有的路径中最小的路径和。

## **五、Java**

 **### **jvm 的内存结构：**

jvm 内存共分为虚拟机栈，堆，方法区，程序计数器，本地方法栈

1、虚拟机栈：线程私有，每个方法在执行的时候会创建一个栈帧，存储了局部变量表，操作数帧，动态连接，方法返回地址等；每个方法从调用到执行完毕，对应一个栈帧在虚拟机中的出栈和入栈。

2、堆：线程共享，被所有线程共享的一块内存区域，在虚拟机启动时创建，用于存放对象实例。

3、方法区：线程共享；被所有线程共享的一块内存区域；用于存储已被虚拟机加载的类信息，常量，静态变量等。

4、程序计数器：线程私有，是当前线程所执行的字节码的行号指示器，每条线程都有一个独立的线程计数器，这类内存也被称为"线程私有"的内存。

5、本地方法栈：线程私有；主要为虚拟机使用到的 Native 方法服务。

### **JVM 中堆和栈的区别：**

1、最主要的区别就是栈内存用来存储局部变量和方法调用。

而堆内存用来存储 Java 中的对象。无论是成员变量，局部变量，还是类变量，它们指向的对象都存储在堆内存中。

2、独有还是共享：

栈内存归属于单个线程，每个线程都会有一个栈内存，其存储的变量只能在其所属线程中可见，即栈内存可以理解成线程的私有内存。而堆内存中的对象对所有线程可见。堆内存中的对象可以被所有线程访问。

3、异常错误：如果栈内存没有可用的空间存储方法调用和局部变量，JVM 会抛出 java.lang.StackOverFlowError。而如果是堆内存没有可用的空间存储生成的对象，JVM 会抛出 java.lang.OutOfMemoryError。

4、空间大小：栈的内存要远远小于堆内存，如果你使用递归的话，那么你的栈很快就会充满。如果递归没有及时跳出，很可能发生 StackOverFlowError 问题。可以通过-Xss 选项设置栈内存的大小。-Xms 选项可以设置堆的开始时的大小，-Xmx 选项可以设置堆的最大值。

**控制反转（IoC），获得依赖对象的方式反转**

一种设计原则，可以用来减低计算机代码之间的耦合度。其中最常见的方式叫做依赖注入（反射，它允许程序在运行的时候动态的生成对象、执行对象的方法、改变对象的属性，spring 就是通过反射来实现注入的）。

我的理解是，创建对象的控制权进行转移，以前创建对象的主动权和创建时机是由自己把控的，而现在这种权力转移到第三方，比如转移交给了 IoC 容器，它就是一个专门用来创建对象的工厂，你要什么对象，它就给你什么对象，有了 IoC 容器，依赖关系就变了，原先的依赖关系就没了，它们都依赖 IoC 容器了，通过 IoC 容器来建立它们之间的关系。

**aop：可以通过预编译方式和运行期动态代理实现在不修改源代码的情况下给程序动态统一添加功能的一种技术。****  ****Java 的内部类和静态内部类的区别？

线程的实现方式有哪些？

**平常遇到过哪些异常？捕获异常有哪些方法？**

 **1\. JVM(Java 虚拟机)异常：由 JVM 抛出的异常或错误。例如：NullPointerException 类，ArrayIndexOutOfBoundsException 类，ClassCastException 类。

2.程序级异常：由程序或者 API 程序抛出的异常。例如 IllegalArgumentException 类，IllegalStateException 类。**  **## **六、字节跳动面试题复盘**

 **### **一面：（二面、三面忘记记录了）**

**1、拥塞控制的场景以及解决方法（四种）。**

一般原理：发生拥塞控制的原因：资源(带宽、交换节点的缓存、处理机)的需求>可用资源。

作用：拥塞控制就是为了防止过多的数据注入到网络中，这样可以使网络中的路由器或者链路不至于过载。拥塞控制要做的都有一个前提：就是网络能够承受现有的网络负荷。

对比流量控制：拥塞控制是一个全局的过程，涉及到所有的主机、路由器、以及降低网络相关的所有因素。流量控制往往指点对点通信量的控制，是端对端的问题。

慢启动和拥塞避免：发送方维持一个叫做拥塞窗口 cwnd，初始值为 1，每经过一个传输伦次（RTT 时间），cwnd 加倍（指数增长），为了防止拥塞窗口 cwnd 增长过大而引起网络拥塞，设置一个慢开始门限 ssthresh。

（1）当 cwnd 算法；

（2）当 cwnd>ssthresh，停止使用慢开始，使用拥塞避免算法（线性增长每次加一）；

（3）当 cwnd==ssthresh，两者都可以使用。**  ****快重传：**当接收方收到了一个失序的报文，马上报告给发送方，我没收到，赶紧重传。发送方一连收到三个重复的 ACK,那么不必等待重传计时器到期，立即重传。

**快恢复：**当发送方连续收到三个重复确认，执行乘法减小，ssthresh 减半。然后执行拥塞避免算法，使拥塞窗口缓慢增大。

**2、HTTPS 的加密过程（SSL）。**

非对称加密算法：RSA，DSA/DSS

对称加密算法：AES，RC4，3DES

HASH 算法：MD5，SHA1，SHA256

非对称加密算法用于在握手过程中加密生成的密码，对称加密算法用于对真正传输的数据进行加密，而 HASH 算法用于验证数据的完整性。由于浏览器生成的密码是整个数据加密的关键，因此在传输的时候使用了非对称加密算法对其加密。

非对称加密算***生成公钥和私钥，公钥只能用于加密数据，因此可以随意传输，而网站的私钥用于对数据进行解密。

**具体过程：**

（1）浏览器将自己支持的一套加密规则发送给网站。

（2）网站从中选出一组加密算法与 HASH 算法，并将自己的身份信息以证书的形式发回给浏览器。证书里面包含了网站地址，加密公钥，以及证书的颁发机构等信息。

（3）获得网站证书之后浏览器要做以下工作：a)验证证书的合法性 b)如果证书受信任，或者是用户接受了不受信的证书，浏览器会生成一串随机数的密码，并用证书中提供的公钥加密。c)使用约定好的 HASH 计算握手消息，并使用生成的随机数对消息进行加密，最后将之前生成的所有信息发送给网站。

（4）网站接收浏览器发来的数据之后要做以下的操作：a)使用自己的私钥将信息解密取出密码，使用密码解密浏览器发来的握手消息，并验证 HASH 是否与浏览器发来的一致。b)使用密码加密一段握手消息，发送给浏览器。

（5）浏览器解密并计算握手消息的 HASH，如果与服务端发来的 HASH 一致，此时握手过程结束，之后所有的通信数据将由之前浏览器生成的随机密码并利用对称加密算法进行加密。

**3、Java 多线程实现的方法，各自的优劣对比。**

（1）继承 Thread 类，启动线程通过 Thread 类的 start()实例方法。start()方法是一个 native 方法，它将启动一个新线程，并执行 run()方法。优点：代码简单。缺点：该类无法继承别的类。

（2）实现 Runnable 接口，优点：继承其他类。同一实现该接口的实例可以共享资源。缺点：代码复杂。

（3）实现 Callable 接口，通过 FutureTask 包装器来创建 Thread 线程：优点：可以获得异步任务的返回值。

（4）线程池：实现自动化装配，易于管理，循环利用资源。

**4、接口和抽象类的区别。**

（1）抽象类和接口都不能被实例化。

（2）两者都是用来抽象具体对象的，但接口的抽象级别更高。

（3）类可以实现多个接口，但只能继承一个抽象类。

（4）抽象类可以有具体的属性和方法，但接口只能有抽象方法和不可变常量（定义的常量都是公共的静态常量，用 final 来修饰）。

（5）抽象类可以用 protected、public 来修饰，但接口只能由 public 来修饰。

（6）接口是设计的结果（注重功能实现），抽象类是重构的结果（实现代码复用）。

**5、垃圾回收原理以及 gc 算法（三种），复制算法的原理。**

（1）随着程序运行，内存中存在的实例对象、变量等信息占据内存越来越多，如果不及时进行垃圾回收，程序性能下降，甚至会由于内存不足出现系统异常。回收的时间点：1）该类的实例对象都已被回收；2）加载该类的 classloader 已经被回收；3）该类对应的反射类 java.lang.class 对象没有被任何地方引用。

（2）需要回收的区：堆和方法区。

（3）常见的回收算法：三个算法都基于根搜索算法去判断一个对象是否应该被回收，而支撑根搜索算法可以正常工作的理论依据，就是语法中变量作用域的相关内容。

**1）标记-清除算法：**将进行回收的做标记，之后扫描清除。

**2）复制算法：**将内存划分为两个区间，在任意时间点，所有动态分配的对象都只能分配在其中一个区间（称为活动区间），而另外一个称为空闲区间。当有效内存空间耗尽时，JVM 将暂停程序运行，开启复制算法 GC 线程。接下来 GC 线程会将活动区间内的存活对象，全部复制到空闲区间，且严格按照内存地址依次排列，与此同时，GC 线程将更新存活对象的内存引用地址指向新的内存地址。此时，空闲区间已经与活动区间交换，而垃圾对象现在已经全部留在了原来的活动区间，也就是现在的空闲区间。事实上，在活动区间转换为空间区间的同时，垃圾对象已经被一次性全部回收。

**3）标记-整理算法：**标记：遍历 GC Roots，然后将存活的对象标记。整理：移动所有存活的对象，且按照内存地址次序依次排列，然后将末端内存地址以后的内存全部回收。

**6、Java 深复制浅复制区别。**

 ****7、Java 并发控制实现方式。**

 ****8、索引概念、使用位置及优化。**

**9、数据库内连接、左连和右连区别。**

**10、进程间的通信方式。**

 ****11、****进程调度算法。**

 ****12、算法题：**

（1）输出比给定数字大的下一个数字（力扣原题）

比如：数字：1234， 输出 1243

比如 1243，则输出 1324

（2）定义：给定 N 个数，称出现次数最多的数为众数，若某数出现的次数大于 N/2 称为绝对众数。如：A={1, 2, 1, 3, 2}中，1 和 2 都是众数，但都不是绝对众数。

如：A={1,2,1,3,1}中，1 是绝对众数。求给定数列的绝对众数。

**concurrenthashmap 介绍**

 ****线程池参数以及作用**

二面计算题：输出所有子集，三面计算题：大数相加**********  ****## **七、腾讯面经**

 **### **WXG 质量管理一面**

1、简单自我介绍，介绍个最近的项目，项目的期望是完成一个怎样的东西，你做了哪些工作，得到了哪些收获？

2、申请的专利是什么？怎么研究的？

3、针对项目问了具体的函数实现（流程模拟可视化，具体是怎么样的功能）、数据清洗过程、数据库建表（举例说明）。为什么查询用到 having？这个 join 你具体怎么用的，写一下？你的项目来都建了哪些表具体说一下？怎么把它们分成子表的？他们各自的主键是啥？你具体的任务量是多少？

4、项目网页测试咋测的？APP 这个测试怎么做的？边界值分析具体怎么用的（文字输入和发送消息数目）？除了边界值分析还运用到什么方法？用了哪个测试工具（MonkeyRunner）？介绍一下具体使用过程（脚本函数、录制、回放）。你觉得他和手工测试有什么区别？遇到的 bug？怎么排查的问题？（AndroidManifest 未注册事件、Activity（界面）事件中出现了空指针）

5、数据库主键了解吗？有那几种类型？左右连接是什么？区别？

6、了解什么语言？JAVA 实现并发的方式？有开发经验吗？了解哪些锁？讲讲你刚刚说的 synchronized 和乐观锁、悲观锁？

7、数据结构了解哪些？介绍下链表，循环链表是什么？怎么判断是循环链表？

8、讲讲网络常识，TCP、UDP 的区别？他们在哪一层？HTTP 是什么？在哪一层？他们三者有什么关系？HTTPS 呢？SSL 加密是哪一层？SSL 具体过程？加密算法能分别举例吗？

9、操作系统学的咋样？进程线程的区别？了解线程池吗？讲讲它的原理。

10、Linux 有接触吗？了解哪些？说说自己会的命令（说了 15 条以上并给了具体例子）。Linux 系统启动过程是什么？

11、现在微信大面积用户登录不上你怎么排查？你说的这么多情况你觉得最可能是哪个原因？

12、质量管理和测试的区别你有了解过吗？

### **质量管理二面（交叉面）**

1、了解项目，深度学习图像识别提升精度具体操作。

2、平常捕获异常用什么方法？

3、Sql 语句查找最新 10 条记录。

4、APP 性能调优。

5、数据库性能调优。

6、索引的概念以及使用场景、优化。

7、Java 的反射、类加载过程。

8、Linux 指令：（1）在不知道子目录的情况下找到某个文件。（2）查看进程使用内存的状态。

### **三面：**

大数相加。项目问题。

### **四面和五面：**

微信两轮面委会：每轮三道算法题（1 简单+1medium+1hard）。

项目问的巨细，写了多少行代码，技术博客。为什么有了字节还想来腾讯？**  **# 篇二：已上岸，字节，奇安信等回馈牛油的面经以及基础知识整理

作者：我太想要个 offer 

国际惯例，讲一下楼主的基本情况，教育部直属 211 本科毕业，非科班，专业偏硬件，秋招考研总分差点，饮恨考场。技术栈的话就会 C/C++，matlab（毕设用的），linux 系统，mysql 数据库语句，项目就是一个在学校和同学做的课设（和科班比差太多），无实习，在国内外疫情这么严重的情况下还要进入互联网公司，综合考量之后决定从一名测试人员做起，从 2 月 20 日至今拿到了奇安信（软件测试工程师），字节（游戏测试工程师，base 北京），三七玩估计也是能过得（游戏测试工程师），剩下的还没面试或者面试中的就不继续进行了，已经签字节跳动了（开森） 。

这个时候发面经已经有点晚了，所以现在打算让这篇面经主要面向即将秋招的童鞋，当然现在还在春招的童鞋也可以看哈，我和大家一起聊聊关于测试的二三事。

## **一、什么是测试工程师？**

以软件开发为例，宏观上说测试工程师就是软件质量的把关者，在一个软件开发流程中测试人员要与开发人员一起对软件的研发进行负责，软件进行质量把关，开发进行功能实现，相辅相成。

测试又分为传统的测试工程师和测试开发工程师，测试开发工程师（以下简称测开）就要在上述的基础上最少在掌握一门脚本语言，两门及以上更好，脚本语言包括但不局限于 Python，golang，shell 等等因为测试开发工程师其实是造轮子的，换言之就是开发测试用的各种工具；测试工程师对代码要求较测开低一些，但不是一窍不通（我面试的时候都会遇到代码问题），不懂代码层次的问题的话其实就不好进行测试分析这个后面再解释为什么。

## **二、测试工程师的工作是什么？**

测试工程师简单点说就是找 bug，然后反馈给开发人员，不要小看这个工作。

首先很明显的 bug 开发人员有时候自己就能找到，测试人员要有比开发人员更加全面的想法才能找到深层次的问题点。

其次，要端正一个态度就是测试人员不是一个电脑小白，相反国外顶级的测试开发工程师同时也是顶级的开发工程师甚至更厉害，所以作为一个测试工程师一定要有继续学习的精神和心态，然后明白测试工程师要会写测试用例，这很重要，文本写的好坏会直接影响到后期的维护成本。

这段时间问自动化测试的童鞋比较多，那我就来聊聊我对自动化测试的理解（个人愚见）。

首先说一下自动化测试是什么？自动化测试从职能上讲就是去在一定的框架下去开发一些自动化测试脚本来实现 QA 所做不到的事情，拿 fgo 来举栗子比较合适，众所周知 fgo 核心玩法之一就是抽卡，这个抽卡是有概率的（欧皇请自动左滑），而 QA 是不可能去抽几十万次甚至上百万次来验证概率问题，这时候就要自动化脚本来模拟抽卡去抽，看看概率算法是否有问题，本来自动化测试其实在某种意义上是一种灰盒测试，但是现在很多公司会把自动化测试归到测开里面去，所以现在自动化就偏白盒一些。

第二个要说的就是语言，现在自动化测试流行的语言就是 Python，shell，golang，（据说要是 cpp 厉害的也可以），不是很死板，但主流一定是脚本语言。

就先讲这么多吧，也算是一个小总结。欢迎各位大佬对我提出意见或者交流经验，鞠躬。

游戏 QA 的话要求会更高，因为传统软件测试要看的方面游戏要测试，而传统软件不作为重点的地方游戏也要测试，所以游戏测试工程师相对来说是工作量会大一些。具体的测试流程的话可以自行百度或者 Google，里面讲的比我要好很多。

## **三、聊聊测试方法吧**

测试有一种分类方式可以简单的分成黑盒测试和白盒测试，举个栗子，黑盒测试常见的测试方法有等价类划分方法、边界值分析方法、错误推测方法、因果图方法、判定表驱动分析方法、正交实验设计方法、功能图分析方法、场景设计方法等等，具体的话要看你所要测试的具体内容而定。

白盒测试，先讲一下，白盒测试其实应该是测试开发工程师，要懂代码，要看的懂代码层次的逻辑和架构，然后去寻找代码层次出现的 bug 问题，**方法具体有：**

**语句覆盖，**主要特点：语句覆盖是最起码的结构覆盖要求，语句覆盖要求设计足够多的测试用例，使得程序中每条语句至少被执行一次。

**判定覆盖，**主要特点：判定覆盖又称为分支覆盖，它要求设计足够多的测试用例，使得程序中每个判定至少有一次为真值，有一次为假值，即：程序中的每个分支至少执行一次。每个判断的取真、取假至少执行一次。

**条件覆盖，**主要特点：条件覆盖要求设计足够多的测试用例，使得判定中的每个条件获得各种可能的结果，即每个条件至少有一次为真值，有一次为假值。

**判定/条件覆盖，**主要点：设计足够多的测试用例，使得判定中每个条件的所有可能结果至少出现一次，每个判定本身所有可能结果也至少出现一次。

**组合覆盖，**主要特点：要求设计足够多的测试用例，使得每个判定中条件结果的所有可能组合至少出现一次。

**路径覆盖，**主要特点：设计足够的测试用例，覆盖程序中所有可能的路径。

从方法上不难看出如果测试人员不懂代码会给工作带来一定的压力，也正好的解释了第一章里最后问题，具体的也希望大家去进行一下自学。

当然我只是去罗列一下一些测试方法，百度也可得，我只是做了一下整理，真正变成自己的还需要去实践，当你自己去亲身测一段代码或者去测一测软件的某个功能，你就会去融会贯通。

 ## 四、说了这么多再聊聊面经吧（这里我只讲一下字节和奇安信的）

**（一）字节跳动游戏测试工程师**

 ****一面（45min）、二面（30min）综合讲**

1、自我介绍，项目介绍，然后让测一下项目里的内容；

2、讲一讲自己的游戏史，因为我的简历里写了我有丰富的游戏史；

3、从自己说的游戏中面试官挑了几个点进行测试；

4、聊了聊 fps 游戏的中测试内容和外挂问题（ps：因为我谈了我对外挂很感兴趣）；

5、聊了聊为什么要当测试以及游戏测试和测试人员的不同；

6、讲了讲测试用例应该有什么，要注意什么；

7、讲了讲一个单链表中查找第 K 个节点能用的方法，时间复杂度是多少；

8、智力题：两个桶来取水的问题，重点看实现过程；

9、聊了聊对测试职能的理解；

10、反问环节；

ps 两面的面试官都超级温柔，有问题会耐心解答，体验极佳。

**三面的话（35min）**

比上述内容多了一些内容，就是聊了聊 moba 和 fps 具体的一些测试，然后出了道足球赛的赛制问题；再就多了和我聊了聊自己的性格和特点之类的。

反问环节和面试官聊了蛮多的关于游戏方面的知识，面试官逻辑超强，讲的很棒，自己受益颇多。

**hr 面（15min）**

**就很正常的问了一些个人性格，经历等等，跟牛客网上大佬们的面经大同小异，不在赘述。****  ****### **（二）奇安信测试工程师**

 ****一面（30min）**

1、自我介绍，项目介绍；

2、重点讲一下项目；

3、对项目里的每一项功能进行测试，不断锤炼，不断反问（20min）；

4、反问；

**二面（30min）**

1、自我介绍，项目介绍；

2、再测一遍自己的项目（没错，就是再来一遍）；

3、设计场景进行测试；

4、问了问自己平时有没有学一些别的技术比如网页开发之类的，然后我又说外挂。。。。（10min）；

5、反问；

**HR 面（20min）**

 ****就很正常的问了一些个人性格，经历等等，跟牛客网上大佬们的面经大同小异，不在赘述。******  ******## **五、最后再写点碎碎念**

个人对于工作和考研的态度就是：完全看个人发展，开发和测试对经验看中一些，算法等对学术要求高一点，具体还是看个人，不过有一点要声明，找工作趁早。

招聘期间多注意公司的 JD 然后自行匹配，努力让自己更加贴合 JD 会增加简历通过概率，JD 其实也是帮助我们自己看看自己还有那些知识要注意,要利用好。

大三或者研二条件允许的情况下一定找实习，这会让你先看看行业内容，进而判断自己是不是适合这份工作。

**欢迎想做测试的童鞋们和我探讨测试的问题，真心想聊的童鞋可以私信，但是晚上统一回复，要不然太忙了，大佬体谅下。（我在晚上查看我的消息记录，会逐一回复，别慌啊）**

 ****最后感谢牛油大佬的一路陪伴，鞠躬感谢，祝诸君前程似锦，一帆风顺！****  ****# 篇三：字节跳动测试

作者：求一个 offer 啊

更新！更新！上次的图片看不清，我把题目复制上来

更新！我凉了，为了回馈牛客，把我这段时间整理的面经奉上

1、TCP 和 UDP 的区别

2、http 的交互过程

3、知道哪些数据结构

4、链表和数组的区别

5、MySQL 中把 A 插入到 B 的语句

6、索引的作用

7、假如你是哇哈哈的老板，如何对哇哈哈设计测试用例

8、假如你是一个组的队长，你的队员不按时完成任务，你怎么办

9、代码题:给一个数组，里面有一些数字, 把这些数字排序，使得拼出来的数字最大

10、智力题:考察的是二分法，8 个球有一个比较轻, 有一个天平, 最少称几次

11、测试题例: (1) 测试发送图片的功能; (2) 视频加载不出来，从哪里测试

12、GET POST 区别，POST 安全性更高为什么

13、三次握手

14、输入网址到获得页面的过程

15、tokin session，用什么方法和后台交互的

16、计算机网络：cookie 和 session 区别

17、应用层你了解哪些协议（说了 http https dns）然后问 http 和 https 的区别

18、操作系统: 进程线程区别

19、死锁（发生原因如何解决）

20、java 基础:泛型

21、继承和多态

22、垃圾回收机制

23、类加载

24、数据库: 主键索引（为什么要有索引）

25、测试:怎么测滴滴打车

26、给一个数 n 找出相加等于这个数的所有素数组合中，素数最少的

27、你对测试工作的认识                            

28、多线程相关的 thread runable 那些的互相有什么区别

29、测试微信群发红包拼手气红包

30、某个页面加载不出来，分析可能的原因

31、对加锁的理解加锁方式以及有哪些锁  

32、撕代码:一个数组找出出现次数为奇数的两个数

33、get，post 区别

34、代码题：一个数组怎么调顺序合起来最大

35、http 和 https 区别

36、Linux 命令考察

37、java 垃圾回收机制

38、内存泄漏和内存溢出

39、江浙沪一带抖音用户出现问题，什么原因？

40、手机 APP 使用未响应，什么原因？

41、智力题：8 个球其中一个轻球，分几次称找出来

42、TCP 连接

43、地址栏输入网址按回车的过程

44、对一个矿泉水瓶编写测试用例

45、网页状态码知道哪些

46、然后是在一个场景下问了一些问题（场景是抖音直播时送礼物）， 包含了一些计算机网络知识、测试用例设计，比如像主播送礼物发生了什么，发了礼物但是主播那边没有显示可能有什么原因， 然后针对这个场景设计测试用例等

47、三次握手

48、 http，https

49、数据库相关内连接、左连接、右连接，做题

50、算法做了一个最长无重复字符串

51、进程线程、区别等

52、然后问了四次挥手

53、线程同步

54、做了一个大数加减法类型（输入输出都是一个字符串）

55、状态码

56、继承、虚拟机

57、Linux 搜索一个字符

58、Linux 常用指令

59、618 你买了个手机你怎么快速知道你的手机没有问题

60、左连接、右连接

61、链表

62、进程和线程的区别

63、 http 和 https 的区别

64、linux 命令，找出关键字出现的次数

65、数据库，查找一个学生两门功课都大于 80 分的姓名

66、浏览器中输入一个地址，按下回车后发生了什么

67、 tcp 三次握手和四次挥手

68、拥塞控制

69、微信发红包测试用例

70、写代码，类似高考成绩，一个表中有很多数据（无序的），给你一个成绩， 查出在表中的排名

71、智力题，两个不同容量的水杯倒出固定容量的水

72、算法，两个链表，找出这两个链表是否有相交的点

73、测试用例编写 app 扫码过地铁如何测试这个 app

74、数组和链表区别，栈和队列

75、linux 指令

76、 jvm 分区

77、一个网页的前进和返回上一页，用到什么数据结构，代码实现一下

78、网页卡顿原因排查

79、写一个 sql 题

80、编码题，一个无序数组里存在一个元素出现的次数超过数组长度一半，找出这个数（时间复杂度要求 O(N)）

81、智力题，4 分钟沙漏和 7 分钟沙漏怎么漏出 9 分钟

82、智力题：赛马

83、智力题：5 个囚犯，100 颗豆，每个人随意抓，最多最少的会被处死，哪个存活概率最高

84、编码：青蛙跳台阶的变种

85、内存泄漏和内存溢出

86、网页状态码知道哪些

87、为什么选择测开，你的理解

88、测试看重什么能力

89、TCP HTTP Linux 指令数据结构那一套（快速排序 两个队列实现栈）

90、问大厦里的电梯怎么测试

91、然后编程题我比较菜 两道都很简单 但是我一个没做出来一个只通过

92、最后我问面试官能不能评价一下我，他说答得不错就是在线编程有待加强

93、http 和 HTTPS 的区别

94、数组和链表的区别，它们插入和删除数据的操作

95、Java 的 exception 知道的有哪些

96、内存泄漏

97、测试一下抖音 app

98、淘宝购物的时候有一个界面打不开有些什么原因

99、编程题：输入数组中和为 k 的两个数，比如[2,7,5,1]，k 为 9，则输出 i. [2,7]

100、智力题：有 7 克、2 克砝码各一个，天平一只，如何只用这些物品五次内将 140 克的盐分成 50、90 克各一份？

101、然后反问，问完跟我说通过一面了，后面会再约二面的时间

102、数据链路层上是哪两层，分别有什么协议

103、IP 地址有多少位？有别的情况吗

104、TCP 和 UDP 适用的场景，为什么

105、知道 java 中的栈吗？栈和队列的区别，如何用两个栈实现一个队列

106、编程题：输入一个数组，数组中有一个数有重复，把它输出来,时间复杂度，如果有多个数都重复，输出重复次数最多的那个

107、平时用什么 APP 用的多？答：微信和微博。测试一下微信刷朋友圈

108、TCP 三次握手

109、DNS 劫持是什么

110、线程和进程的区别

111、问了 Linux 的命令 ps 有关

112、网页的登录框界面，有账号、密码、验证码三个输入框，用户输入后，会对哪个输入框率先判断，为什么

113、测试朋友圈评论功能，给了五分钟时间

114、两个智力题：蚊香和证明地球是弧形

115、TCP、UDP 的区别

116、TCP、UDP 应用场景，为什么这么用

117、TCP 三次握手、为什么是三次而不是 2 或者 4 次

118、http 和 https 的区别

119、http 有哪些状态码

120、static 关键字怎么用的

121、java 垃圾回收机制(后来回答了 python 的垃圾回收)

122、c++的内存分配机制，回收机制

123、进程和线程的区别

124、微信抢红包测试

125、王者荣耀中外挂，比如某个用户能看到整个战场，他可能是怎么做到的

126、你要如何测试一个游戏的安全，对它做一个安全测试

127、代码题：冒泡排序+改进

128、MySQL 了解吗

129、文本文件的数据类型，存储大小

130、char 和 varchar 的区别

131、时间类型有哪些

132、MySQL 存储数据的数据结构

133、python 字符串怎么连接

134、python 零碎的字符串用+拼接会比较慢，有什么办法吗)

135、post 比 get 安全是对的吗？在 https 协议里，也是 post 比 get 安全吗？

136、http1.0 和 http1.1 的区别

137、http 报文的结构

138、http 的 header 的内容

139、测试相关的：连续登录领取奖励的功能测试(提示了每天登录去测试吗？)

140、代码题：孤岛问题+链表反转

141、技术路线/熟悉的技术？

142、代码题：实现一个装饰器

143、微信输入框输入到发送接收这个过程测试用例的设计

144、往数据库插入 100 万条数据怎么插

145、微信点赞功能测试用例

146、如何模拟压力测试，如何模拟弱网环境

147、Python 浅拷贝，深拷贝

148、数据库如何进行表的排序

149、计算机的组成，输入程序到 exe 执行输出编译过程

150、Post 输入后到打开页面的过程

151、二叉树中序遍历

152、排序方法

153、事务的特性

154、数据攻击

155、数据库中锁的类型

156、数据库删除语句

157、事务的特性

158、进程通信方式

**一面面经，应该是凉了，问的一些问题触及知识盲区**

 **lz 跨专业面试岗位，就 10 天的时间复习，时间有点不够啊**  **1、爱奇艺原本有一个月，三个月，一年的会员，如今上线 6 个月的会员，请你测试一下；

2、内存泄漏，表现形式；

3、栈和队列在内存管理方面的区别；

4、DNS；

5、http 和 https，http 是无状态的，https 也是吗；

6、tcp 为啥握手是三次，挥手是四次；

7、七层模型及其网络协议；

8、tcp，udp 区别；

9、数据库的三大范式；

10、数据库左连接，右链接；

11、黑盒，白盒的测试方法，你用等价类划分法对之前测过的爱奇艺进行划分；

12、进程和线程的区别；

13、代码题，数据组合出最大数字；

14、你有什么问我的吗？我问您对我有什么建议吗？回答：应届生面试测试岗，主要考察测试的发散思维以及写代码的能力及逻辑思维能力。好吧，我码代码能力属实不行；

15 本来说面试都结束了？又问我题外话，问我平时除了科研工作还有什么兴趣爱好。

## 篇四：华为无线解决方案测试面经许愿 offer

作者：lebron236

终于走完笔试测评面试流程了，都说牛客许愿很灵，那就把自己的一些心得面经和 timeline 发出来，各位对华为或者无线感兴趣的小伙伴可以参考一哈，不喜勿喷哈（本人祖安文科状元）。

## **1、投递**

 **华为最近的招聘政策（某些部门）属于点对点招生（来自投递部门工作 5 年的工程师的科普），也就是说好比我投递的是无线产品线 4g/5g 部门，那么笔试通过后就会有本部门的工程师和本部门的主管进行面试（起码我是这样），面试通过后即可进入本部门工作，不同于以往的招聘方法：所有学生统一面试，过了的人统一泡池子最后按岗位随机分配部门。

补充一点以免误导大家：解测确实是客观题，选择题啥的。但是经过协商后，工程师让我投递软测岗，享受软测的笔试（就是考察我编程能力），然后后续面试内容或者工作岗位和部门都是解测的。**  **## **2、笔试（7.1）**

由于我本科读的是电子研究所读的是网络，编程基础，天赋，兴趣都不太强，所以投递了一个以后工作和编程相关性很小的岗位。

在刷了牛客华为笔试题库一半后，我就参加了笔试。笔试难度完全看运气，按理说三道题一共 600 分（第一题 100 第二题 200 第三题 300），过 100 分就算通过，但是其实不好好准备真的有可能会挂。

我当时的题目第一题很简单，就是简单的字符串变换，20/30 行 py 就 ac 了。但是第二题第三题真的难哭了，因为光读题理解题每道题都花了我 15min。也有的同学三道题的难度很平均，换句话说，可能一道题都做不出来。

所以在这里我推荐大家不仅要刷华为题库的题，也最好刷一下牛客题霸的题目。这是因为华为题库的题目描述都偏短，可以重点练一下题目描述偏长的题型，以免不适应。华为归根结底还是通信公司，数据结构算法啥的考的不多，主要刷下字符串和数组就 ok。

## **3、一，二面（7.17）**

我的一面二面是连在一起的，中间只休息了十分钟。

一面的面试官感觉是搞软开的，一直问我会不会数据库会不会 linux 数据结构学的咋样，压根没问项目也没问测试的原理。

二面面试官就和蔼的多，问了计算机网络，通信原理，测试基础知识，项目等。

总结下两次面试的问题

**计算机网络的问题有：**七层结构，三次握手，四次挥手，拥塞控制，滑动窗口，tcp/udp 区别，打视频电话属于 tcp 还是 udp，DNS 如何解析等。

**通信原理的问题有：**4g 上下行的技术分别是什么（scofdm 和 ofdm），区别，数字通信系统画一下，isi 是什么，画一下 qpsk 星座图，ofdm 工作原理等。

**测试知识，**测试流程是什么，你觉得哪一步最重要，你为啥要干测试不做开发。

**手撕：**第一个是随机输入四个数，输入加减乘除，使结果等于 24。第二题是简单的随机生成数字，然后排序，输出第 2 大的数字。

**项目**不会让你从头开始讲，会问你觉得做项目中遇到的最大的困难是什么。

## **4、三面（8.13）**

由于部长一直出差约不上，最后约到了 8.13 进行三面，三面就是简历从头到尾开始问，项目经历，你担当的角色，如何协调，收获了什么，怎么改进的，有什么创新点，你的特长是什么，愿意加班吗，对华为的理解，对无线的理解，反问。

希望看了这篇帖子的小伙伴们有所收获，也希望大家都能拿到心仪的 offer。

最后都说牛客灵，许愿 offer！

# 篇五：测试工程师面经汇总

作者：青时梁浅

分享一波面经，攒个运气（冲鸭）

## **一、奇安信测试工程师（一面）**

 **1、自我介绍；

2、为什么想做测试而不是开发？

3、实习项目各种问（印象最深刻的 bug 有哪些？最后怎么解决的？性能测试？共发现了多少 bug？浏览器低版本兼容性验证？等等）

4、输入字符串例如“_1234_”，要求输出“1234”，即去掉下划线

5、为什么想来北京？工作强度怎么看待？

6、对加班这种现象的看法？

7、最近在看什么书？有没有动手做什么项目？

8、题库 app 的测试用例（测试点）？

9、有什么想问我的或者想向我展示的点？

暂时想起来的就是这些，主要是实习那块问的比较详细，后面的有点类似于闲聊。

面试官亲和力好评，所以没有很紧张。

结果等待通知。。。。。。**  **## **二、三七互娱游戏测试（一面）**

1、先自我介绍一下。

2、着重问了一下实习的内容、自己负责的模块、用过哪些工具等等。（这块问的比较详细具体一些，包括测试用例、测某个模块的一些思路。）

3、你之前学过 java 嘛，那你实习期间在哪里有用到它吗？

4、你有写过一些脚本执行吗？用的什么语言呢？

5、说说令你印象最深刻的一个 bug。

6、从发现 bug 提交到缺陷管理系统到 bug 修复期间需要注意哪些？

7、如果你发现了一个 bug，但是开发说自己按照步骤操作之后验证通过了，说这个不算是一个 bug，你要怎么办？

8、浏览器不兼容问题要怎么考虑测试？

9、说说你实习期间的收获？自己觉得有什么不足吗？那以后要怎么做呢？

10、为什么想做游戏测试呢？你平时玩游戏吗？玩的什么游戏？你觉得它吸引你的点是什么或者它为什么这么流行？

11、如果我们现在新出了一个游戏人物，比如王者荣耀里的谁谁谁（因为我说了玩王者荣耀），然后你是测试人员的话你会怎么测试呢？

13、软件测试和游戏测试的区别是什么？

14、你有了解过性能测试吗？了解程度是？

## **三、华为测试工程师**

### **（一面）**

 **1、自我介绍；

2、你的专业是？那你们学的课程都有哪些？学过 C++吗？（因为我笔试有一道是用 C++编写的）

3、你讲一下信息安全？（因为我是信息安全专业的）你比较熟悉的是哪里？讲一下？

4、手撕代码：自守数（一个数的平方的尾数等于该数自身的自然数，例如：11=1、55=25、6*6=36，要求，输入一个数 n，输出 n 以内的自守数，考虑 n 足够大，比如 n=655365。）

5、实习期间你负责什么内容？印象最深的的一个 bug？有用过什么工具？

6、现在给你一个登录模块，你要怎么测试？（从界面、功能、安全性和性能方面考虑测试点）

7、黑盒白盒测试了解吗？区别是？

8、白盒的测试方法有哪些？解释一下？

9、你实习总共发现了多少 bug？bug 的等级怎么确定？

10、数据库有接触吗？事务的四大特性是什么？

11、操作系统学过吗？主要讲的是什么？

12、你有什么问题问我吗？

### **（二面）**

 **1、自我介绍；

2、手撕代码：输入一个有符号整数，数据范围是[-321, 321+1]，然后输出是他的反转，超出范围之后输出 0，比如输入 23，输出 32。

3、可以说一下你实习具体负责什么吗？

4、那你们像 bug、测试用例这些东西是怎么保管的呢？

5、测试方法都有哪些？讲一下？

6、性能测试、压力测试了解吗？讲一下？

7、你还了解测试的那些内容？可以详细说一下吗？

8、我看你简历上有写挑战杯二等奖，可以具体讲一下吗？你从中收获了什么？

9、社团活动中你扮演的角色？具体负责哪些工作？

10、大学期间有什么让你印象深刻的吗？

11、你有什么问题问我吗？

### **（终面）**

 **1、为什么想做测试？

2、讲一下测试的流程？你觉得哪些测试是重要的？为什么？

3、你实习的公司主要是做什么业务的？

4、自动化测试有了解过吗？有没有用过相关的测试工具？

5、如果你发现了一个 BUG，但是开发说这不是一个 bug，你要怎么办？

6、黑盒测试的方法？解释一下（举例说明）？

7、安全性测试从哪些方面考虑？怎么验证？或者说怎么设计测试用例？

8、假设现在有一个政府的门户网站给你测试，你要从哪些方面、哪些测试点入手？

9、浏览器兼容性测试是做什么的？

10、家在哪里？在校期间获得奖学金的情况？为什么没有考研？

11、你觉得之前两轮面试有哪里没表现好吗？

12、现在手里有 offer 吗？你还有什么问题问我吗？********************************************************