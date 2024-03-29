# 第二章 第 2 节 了解 FPGA 主要技术方向

> 原文：[`www.nowcoder.com/tutorial/10045/120e72fb445c46d6a1b6e0c32358f280`](https://www.nowcoder.com/tutorial/10045/120e72fb445c46d6a1b6e0c32358f280)

# 

*   FPGA 岗主要的技术方向

在找工作之前，首先应当搞清楚自己想找的工作有什么工作内容，总的来说，企业招 PFGA 岗，想做的主要是这几个方向：

## 一、协议接口

由于在通信网络之间，甚至在同一个系统之内，不同的硬件设备之间使用的数据传输格式、数据传输速率可能会有所不同，很多时候需要对这些不同格式、不同速率的数据进行转换之后才能处理或者发送。这些转换有时候可能会比较复杂，而且对实时性的要求往往会比较高，很多时候会用到 FPGA。

## 二、数字信号处理

学过通信原理的同学应该知道，从信道接收到信号之后，往往还需要频移、滤波、下采样等操作才能还原出调制在上面的信号，此外由于信号在传输过程中往往会受到各种各样的损伤，因此可能还会需要引入一些补偿算法。虽然 FPGA 的最大时钟频率可能比不上 CPU，但由于其可以灵活并行展开，数据吞吐量较大，因此也常用于数字信号处理中。当然除了通信方面的数字信号处理，现在也有很多企业出于对实时性、性能、灵活性等方面的考虑，会将 FPGA 用到图像处理、自动驾驶、语音识别等方向。用一句话来说，就是在 FPGA 上面实现一些算法。

## 三、硬件方案

在这个方向中，FPGA 主要是满足一些定制需求，在硬件系统中扮演主控的角色。出于成本考虑，一般的系统中的主控会使用单片机，但有时候单片机的性能并不能满足要求，此时就会用上 FPGA。在该技术方向中，FPGA 除了做数据接口和数据处理方面的工作，可能会做比较多控制方面的工作。

# 

*   确定自己的技术方向

虽然同是 FPGA 岗，但在不同企业、甚至同一企业的不同部门之间，技术方向都会有所不同，因此在开始找工作之前，一方面，需要想清楚自己的兴趣是什么；另一方面，要考虑自己的现有的技能和经历是否能支撑自己去完成这些工作内容。可能有的同学不能判断自己的技能和经历是否能支撑自己完成工作内容，那么你可以问问自己：如果你的技术方向是做接口，那么你对常用的接口了解有多少？有没有在 FPGA 上实现过用 USB3.0、LVDS 之类的接口传输数据？能不能自己现场写一个异步 FIFO？如果你的技术方向是做数字信号处理，那常用的数字信号算法你会多少？FFT 算法 IP 核的控制逻辑有没有掌握？有没有在 FPGA 上实现过滤波、频移、FFT 之类的数字信号处理步骤？如果你的技术方向是做硬件方案，那么你做过的系统都具体实现了什么样的功能？在系统中 FPGA 和各个模块之间的通信接口、控制逻辑是怎样的？可以用状态机的形式描述 FPGA 在系统中的功能吗？上面的只是一些笼统的例子，自己适合哪个技术方向，还有哪些地方需要补强，只有自己多思考才会知道。

# 

*   确定方向对口的企业

确定好自己要做的方向之后，可以根据自己的技术方向来确定自己的对口企业，我这里可以举一些例子：做高速接口、通信算法，可以考虑通信类企业，如华为、中兴、海格。前面两家做的基站中涉及大量的信号转换和信号处理，后面一家的卫星通讯业务同样也大量涉及无线信号接收与处理。做图像处理算法，可以关注大疆创新、海康威视、商汤科技，前者绝大部分的无人机上都搭载了镜头，后面两家在安防领域有所建树，图像处理是它们的核心业务之一；做自动驾驶算法，可以考虑 Nvidia、小马智行，它们都有在无人驾驶方面的业务，而 FPGA 能满足无人驾驶对高实时性的要求；做人工智能算法，可以考虑寒武纪，人工智能加速芯片的研发过程中离不开大量的 FPGA 验证。上面只是一些例子，实际上 FPGA 在行业内的应用肯定不止这些。如果你的 FPGA 方面基础扎实，研究的方向又和企业的方向对口，那么你能拿到 Offer 的概率会大大提升，因此应该根据自身情况和企业情况，先定下一个对口企业列表，这个列表不用很大，优先为列表中的企业做准备，这是你校招中的基本盘，目标是把列表中的每个 offer 都拿下来。需要指出的是，由于真正意义上的 FPGA 岗并不是那么多，而就算有也不一定会和项目背景对口，在投递的时候也应该多关注数字 IC 设计岗，虽然很多数字 IC 岗的招聘要求中需要你掌握 Synopsys 一家的 EDA 工具，甚至要求流片，但实际上就算你不会，关系也不大(当然能懂一点是最好的)。因此只要你 FPGA 相关的知识扎实、做的东西和企业的业务方向对口，就算是数字 IC 岗也可以放到你的基本盘列表中。

# 

*   确定想挑战和尝试的企业

确定好自己的基本盘后，也可以考虑一下没那么对口，但自己又很想去的企业，要想去这种企业，一方面，要把自己的方向和经历“包装”得更加对口，通过一些表达上的技巧尽可能地把自己的方向与目标企业扯得更接近些；另一方面，必须补充相关的知识，光靠扯没点干货肯定还是不行的。针对这些不对口的企业，要正确评估其难度，不要抱太高的期待，假如说你连 CPU 的 Harzard 有几种都没搞清楚，就投了 Arm 的 Hardware Engineer，那就要做好一轮游的准备。校招的规划，可以总结成两句话**“准备要稳，投递要狠”**，确定好自己的基本盘和自己想挑战的企业，针对性地做好准备，然后大胆地去投。

# 

*   心态建议

其实校招和相亲差不多，都是一个双向选择的过程，最大的区别可能是校招中的海王和 PUA 会更多一些(这也是双向的，Offer 收割机对企业来说就是海王，哈哈)，所以心态要放平，没拿到 Offer 也不要太气馁，可能只是双方不合适罢了。现在国内 FPGA 开发和数字 IC 设计还是个小圈子，圈子里那几家企业互相跳槽也是很常见的事情，即使这次没拿到 Offer，以后只要还在这个圈子里，还是有很多机会的。