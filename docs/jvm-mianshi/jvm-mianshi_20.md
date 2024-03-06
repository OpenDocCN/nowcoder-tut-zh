# 第六章 第 3 节 常见面试题解析

> 原文：[`www.nowcoder.com/tutorial/10077/33c0012584e24efca53d5a2cafc8b29f`](https://www.nowcoder.com/tutorial/10077/33c0012584e24efca53d5a2cafc8b29f)

GC 是 JVM 中极高频会被问到的问题，一方面是因为 GC 这块儿的确具有较多的理论基础，另一方面在于其具有极高的实践场景，在业务中经常遇到棘手的 fullGC 需要处理。

## 3.1 请谈谈在老年代和新生代层面的 GC

### 3.1.1 Minor GC

在新生代空间（包括 Eden 和 Survivor 区域）回收内存被称为 Minor GC。这一定义既清晰又易于理解。但是，当发生 Minor GC 事件的时候，有一些有趣的地方需要注意到：

*   当 JVM 无法为一个新的对象分配空间时会触发 Minor GC，比如当 Eden 区满了。所以分配率越高，越频繁执行 Minor GC
*   内存池被填满的时候，其中的内容全部会被复制，指针会从 0 开始跟踪空闲内存。Eden 和 Survivor 区进行了标记和复制操作，取代了经典的标记、扫描、压缩、清理操作。所以 Eden 和 Survivor 区不存在内存碎片。写指针总是停留在所使用内存池的顶部
*   执行 Minor GC 操作时，不会影响到永久代。从永久代到年轻代的引用被当成 GC roots，从年轻代到永久代的引用在标记阶段被直接忽略掉
*   质疑常规的认知，所有的 Minor GC 都会触发"全世界的暂停（stop-the-world）"，停止应用程序的线程。对于大部分应用程序，停顿导致的延迟都是可以忽略不计的。其中的真相就是，大部分 Eden 区中的对象都能被认为是垃圾，永远也不会被复制到 Survivor 区或者老年代空间。如果正好相反，Eden 区大部分新生对象不符合 GC 条件，Minor GC 执行时暂停的时间将会长很多。

### 3.1.2 Full GC

Full GC 的触发条件：

*   调用 System.gc()
*   老年代空间不足，常见场景为前文所讲的大对象直接进入老年代、长期存活的对象进入老年代等
*   空间分配担保失败，使用复制算法的 Minor GC 需要老年代的内存空间作担保，如果担保失败会执行一次 Full GC
*   JDK 1.7 及以前的永久代空间不足

这里补充一下与 System.gc() 相关的内容：
当调用 System.gc()的时候，其实并不会马上进行垃圾回收，甚至不一定会执行垃圾回收。

```cpp
public static void gc() {
    boolean shouldRunGC;
    synchronized(lock) {
        shouldRunGC = justRanFinalization;
        if (shouldRunGC) {
            justRanFinalization = false;
        } else {
            runGC = true;
        }
    }
    if (shouldRunGC) {
        Runtime.getRuntime().gc();
    }
}
```

也就是 justRanFinalization=true 的时候才会执行。查找发现当调用 runFinalization()的时候 justRanFinalization 变为 true。

```cpp
public static void runFinalization() {
    boolean shouldRunGC;
    synchronized(lock) {
        shouldRunGC = runGC;
        runGC = false;
    }
    if (shouldRunGC) {
        Runtime.getRuntime().gc();
    }
    Runtime.getRuntime().runFinalization();
    synchronized(lock) {
        justRanFinalization = true;
    }
}
```

直接调用 System.gc() 只会把这次 gc 请求记录下来，等到 runFinalization=true 的时候才会先去执行 GC，runFinalization=true 之后会在允许一次 system.gc()。之后在 call System.gc() 还会重复上面的行为。所以 System.gc() 要跟 System.runFinalization() 一起搭配使用:

```cpp
static void gcAndFinalize() {
    final VMRuntime runtime = VMRuntime.getRuntime();
    System.gc();
    runtime.runFinalizationSync();
    System.gc();
}
```

java.lang.System.gc()只是 java.lang.Runtime.getRuntime().gc()的简写，两者的行为没有任何不同。唯一的区别就是 System.gc()写起来比 Runtime.getRuntime().gc()简单点。GC 本身是会周期性的自动运行的,由 JVM 决定运行的时机,而且现在的版本有多种更智能的模式可以选择,还会根据运行的机器自动去做选择,就算真的有性能上的需求,也应该去对 GC 的运行机制进行微调,而不是通过使用这个命令来实现性能的优化。

### 3.1.3 Major GC 与 Full GC 的区别

*   Major GC 是清理老年代
*   Full GC 是清理整个堆空间—包括年轻代和老年代
*   执行 CMS GC 的过程中同时有对象要放入老年代，而此时老年代空间不足

很不幸，实际上它还有点复杂且令人困惑。首先，许多 Major GC 是由 Minor GC 触发的，所以很多情况下将这两种 GC 分离是不太可能的。另一方面，许多现代垃圾收集机制会清理部分永久代空间，所以使用“cleaning”一词只是部分正确。

## 3.2 请谈谈你对 GC 分类标准的理解

针对 HotSpot VM 的实现，它里面的 GC 其实准确分类只有两大种：

*   Partial GC：并不收集整个 GC 堆的模式
*   *   Young GC：只收集新生代的 GC
*   *   Old GC：只收集老年代的 GC。只有 CMS 的 concurrent collection 是这个模式
*   *   Mixed GC：收集整个新生代以及部分老年代的 GC。只有 G1 有这个模式
*   Full GC：收集整个堆，包括新生代、老年代、perm gen（如果存在的话）等所有部分的模式

Major GC 通常是跟 Full GC 是等价的，收集整个 GC 堆。但因为 HotSpot VM 发展了这么多年，外界对各种名词的解读已经完全混乱了，当有人说“major GC”的时候一定要问清楚他想要指的是上面的 Full GC 还是 Old GC。

最简单的分代式 GC 策略，按 HotSpot VM 的 serial GC 的实现来看，触发条件是：

*   young GC：当新生代 中的 eden 区分配满的时候触发。注意 young GC 中有部分存活对象会晋升到老年代 ，所以 young GC 后 老年代 的占用量通常会有所升高。
*   full GC：当准备要触发一次 young GC 时，如果发现之前 young GC 的平均晋升大小比目前 老年代 剩余的空间大，则不会触发 young GC 而是转为触发 full GC（因为 HotSpot VM 的 GC 里，除了 CMS 的 concurrent collection 之外，其它能收集 老年代 的 GC 都会同时收集整个 GC 堆，包括 新生代，所以不需要事先触发一次单独的 young GC）；或者，如果有 perm gen 的话，要在 perm gen 分配空间但已经没有足够空间时，也要触发一次 full GC；或者 System.gc()、heap dump 带 GC，默认也是触发 full GC 。

## 3.3 进阶知识点与思考

### 3.3.1 如何判断变量和类无用

假如在常量池中存在字符串 "abc"，如果当前没有任何 String 对象引用该字符串常量的话，就说明常量 "abc" 就是废弃常量，如果这时发生内存回收的话而且有必要的话，"abc" 就会被系统清理出常量池。

判定一个常量是否是“废弃常量”比较简单，而要判定一个类是否是“无用的类”的条件则相对苛刻许多。类需要同时满足下面 3 个条件才能算是 “无用的类” ：

*   该类所有的实例都已经被回收，也就是 Java 堆中不存在该类的任何实例
*   加载该类的 ClassLoader 已经被回收。
*   该类对应的 java.lang.Class 对象没有在任何地方被引用，无法在任何地方通过反射访问该类的方法。

### 3.3.2 SafePoint 与 SafeRegion

safepoint 可以用在不同地方，比如 GC、 Deoptimization ，在 Hotspot VM 中，GC safepoint 比较常见，需要一个数据结构记录每个线程的调用栈、寄存器等一些重要的数据区域里什么地方包含了 GC 管理的指针。从线程角度看，safepoint 可以理解成是在代码执行过程中的一些特殊位置，当线程执行到这些位置的时候，说明虚拟机当前的状态是安全的，如果有需要，可以在这个位置暂停，比如发生 GC 时，需要暂停暂停所以活动线程，但是线程在这个时刻，还没有执行到一个安全点，所以该线程应该继续执行，到达下一个安全点的时候暂停，等待 GC 结束。safepoint 指的特定位置主要有:

*   循环的末尾 (防止大循环的时候一直不进入 safepoint，而其他线程在等待它进入 safepoint)
*   方法返回前
*   调用方法的 call 之后
*   抛出异常的位置

之所以选择这些位置作为 safepoint 的插入点，主要的考虑是“避免程序长时间运行而不进入 safepoint”，比如 GC 的时候必须要等到 Java 线程都进入到 safepoint 的时候 VM Thread 才能开始执行 GC ，如果程序长时间运行而没有进入 safepoint ，那么 GC 也无法开始，JVM 可能进入到 Freezen 假死状态。在 stackoverflow 上有人提到过一个问题，由于 BigInteger 的 pow 执行时 JVM 没有插入 safepoint ,导致大量运算时线程一直无法进入 safepoint，而 GC 线程也在等待这个 Java 线程进入 safepoint 才能开始 GC，结果 JVM 就 Freezen 了。

SafeRegion 是指在一段代码片段中，引用关系不会发生变化。在这个区域中的任意地方开始 GC 都是安全的。可以吧 SafeRegion 看做被扩展了的 SafePoint 。在线程执行到 SafeRegion 时，首先标识自己进入了 SafeRegion ，在这段时间内如果 JVM 要发起 GC ，就不用管表示自己为 SafeRegion 状态的线程了。在线程要离开 SafeRegion 时，它要检查系统是否已完成了根节点枚举(或者整个 GC 过程)，如果完成了那线程就继续执行，否则必须等待直到收到可以安全离开 SafeRegion 的信号为止。

### 3.3.3 OopMap 是什么？

SafePoint 与 SafeRegion 定义了何时进行可达性分析， OopMap 则是加速可达性分析的一种手段。这里对 GC 的基本分类再进行适当介绍。

如果 JVM 不记录任何类型的数据，那么就无法区分内存某个位置上的数据到底是引用类型、整型或其他基本类型。这种条件下的 GC 被称为是 **保守式 GC**。在进行 GC 的时候，JVM 会从一些已知位置开始（例如说 JVM 栈）扫描内存，对于每隔扫描到的数字判断是否是执行 GC 堆的指针。这个过程涉及上下边界检查（GC 堆的上下界已知）以及对齐检查（能被 4 整除的数字一定不是指针）。这种扫描方式存在一定问题，比如存在数字的值和一个地址的值相同，这样这个地址上的对象本来应该被回收，但是由于这个栈的数字巧合的和它的地址值相同，导致这个对象不会被回收。保守式 GC 的好处是相对来说实现简单些，而且可以方便的用在对 GC 没有特别支持的编程语言里提供自动内存管理功能。保守式 GC 的缺点有：

*   会有部分对象本来应该已经死了，但有疑似指针指向它们，使它们逃过 GC 的收集
*   由于不知道疑似指针是否真的是指针，所以它们的值都不能改写；移动对象就意味着要修正指针，换言之，对象就不可移动了。虽然可以使用句柄池迂回解决问题，但是二次访问的开销有时访问速度

当然，JVM 可以在对象上记录类型信息。这样扫描到 GC 堆内的对象时因为对象带有足够类型信息，JVM 就能够判断出在该对象内什么位置（偏移）的数据是否是引用类型。这种是方式被称之为半**保守式 GC**，也称为**根上保守**。本质上来说，这是一种简单的查表方式，有利于提高枚举根节点的速度并允许对象移动。为了支持半保守式 GC ，运行时需要在对象上带有足够的元数据。对于 JVM 而言，这些数据可能在类加载器或者对象模型的模块里计算得到，但不需要 JIT 编译器的特别支持。

准确式 GC，意味着给定内存某个数据，能准确知道其是否是引用类型。要实现这样的 GC ，JVM 就要能够判断出所有位置上的数据是不是指向 GC 堆里的引用，包括活动记录（栈+寄存器）里的数据。主流 JVM 采用从外部记录类型信息并生成映射表，HotSpot 管这样的数据结构叫做 OopMap 。在解释执行时或 JIT 时，记录下栈上某个数据对应的数据类型，比如**地址 1**上的值**12344**是一个堆上地址引用，数据类型为 `com.aaaa.aaa.AAA` 。使用这样的映射表一般有两种方式：

*   每次都遍历原始的映射表，循环的一个个偏移量扫描过去；这种用法也叫解释式
*   为每个映射表生成一块定制的扫描代码，以后每次要用映射表就直接执行生成的扫描代码；这种用法也叫编译式

在 HotSpot 中，对象的类型信息里有记录自己的 OopMap ，记录了在该类型的对象内什么偏移量上是什么类型的数据。所以从对象开始向外的扫描可以是准确的；这些数据是在类加载过程中计算得到的。可以把 oopMap 简单理解成是调试信息。 在源代码里面每个变量都是有类型的，但是编译之后的代码就只有变量在栈上的位置了。oopMap 就是一个附加的信息，告诉你栈上哪个位置本来是什么类型的变量。这个信息是在 JIT 编译时跟机器码一起产生的。因为只有编译器知道源代码跟产生的代码的对应关系。每个方法可能会有若干个 oopMap ，safepoint 把一个方法的代码分成若干段，每一段代码一个 oopMap ，所以 oopMap 的作用域自然也仅限于这一段代码。循环中引用多个对象，意味着会有多个变量，编译后占据栈上的多个位置。那这段代码的 oopMap 就会包含多条记录。每个被 JIT 编译过后的方法也会在一些特定的位置记录下 OopMap ，记录了执行到该方法的某条指令的时候，栈上和寄存器里哪些位置是引用。这样 GC 在扫描栈的时候就会查询这些 OopMap 就知道哪里是引用了。这些特定的位置主要在：

*   循环的末尾
*   方法临返回前 / 调用方法的 call 指令后
*   可能抛异常的位置

这种位置被称为安全点。之所以要选择一些特定的位置来记录 OopMap ，是因为如果对每条指令（的位置）都记录 OopMap 的话，开销过大。

### 3.3.4 其他语言的 GC

除了 Java 语言提供 GC 机制，其他如 Python、Go 语言也提供 GC 机制。

Python 语言主要使用**引用计数**来实现 gc。

```cpp
typedef struct_object {
 int ob_refcnt;
 struct_typeobject *ob_type;
} PyObject;
```

每一个 Python 对象都有一个引用计数 ob_refcnt，当引用计数=0 时，对象被回收。我们知道引用计数存在循环引用的问题，Python 还使用标记清除算法来进行垃圾回收。

Go 语言主要是采用标记清除算法进行垃圾回收的。

### 3.3.5 fullGC 的危害

FullGC 由于是对整个堆内存进行垃圾回收，因此耗时比较多，这严重影响用户程序的性能。并且，如果 Full GC 发生频繁，这表明你的内存分配机制存在问题。有可能是发生了内存泄露，导致老年代中不断产生大量垃圾；有可能是你的大对象（缓存）过多；有可能是你的参数设置不好，minor GC 清理不掉内存，导致每次 minor GC 都会触发 Full GC；还有可能是你的老年代大小参数设置错误，老年代过小等原因。借助工具仔细排查并优化代码才能解决 FullGC 的问题。

### 3.3.6 针对 GC 的 JVM 调参

参数的设置是一个很有技术的活，绝不是一两篇文章能讲清楚的，天下也没有放之四海皆好用的参数设置，因此本节只说明最常用的参数。

首先我们可以根据系统关注的性能目标选择不同的收集器。jvm 提供 client 和 server 两种模式，这两种模型默认的 gc 方式如下：

*   client 模式下，新生代选择的是串行 gc，旧生代选择的是串行 gc
*   server 模式下，新生代选择的是并行回收 gc，旧生代选择的是并行 gc

我们还可以对内存空间进行设置：

*   -Xms：初始堆内存大小，默认为物理内存的 1/64(<1GB)；
*   -Xmx：堆最大大小，
*   -Xmn：新生代的内存空间大小
*   -Xss：每个线程的堆栈大小。
*   -XX:PermSize：永久代初始大小。默认值为物理内存的 1/64。
*   -XX:MaxPermSize：设置永久代最大值。默认值为物理内存的 1/4。

不同的垃圾收集器有一些独立的参数，如：

*   -XX:+ParallelCMSThreads: 设定 CMS 收集器的线程数量。
*   -XX:CMSFullGCsBeforeCompaction：设置 CMS 多少次后进行内存压缩。由于 CMS 收集器会产生内存碎片，因此可以设置 CMS 运行多少次后进行内存压缩。
*   -XX:+MaxGCPauseMills：设置 G1 收集器的最大垃圾收集停顿时间。
*   -XX:+GCPauseIntervalMills:设置 G1 收集器的停顿间隔时间