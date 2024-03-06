# 第四章 第 2 节 面试常见问题及解析

> 原文：[`www.nowcoder.com/tutorial/10077/ac82a21503f841888d4bf0acaa57dca8`](https://www.nowcoder.com/tutorial/10077/ac82a21503f841888d4bf0acaa57dca8)

## 3.2 面试常见问题及解析

### 3.2.1 请详细叙述对象初始化的过程

问题剖析：当面试官提出这个问题时，主要考察你对对象初始化全过程的理解。回答这个问题切勿背诵，要有条理性地组织语言并讲出自己的理解。比如，对象初始化的目的是什么？什么条件下才能执行对象初始化？对象初始化与内存的哪块进行交互？

**回答模板：**

*   先讲讲对象初始化的目的
*   然后讲讲对象初始化的先决条件
*   然后讲分配内存那部分
    *   在内存哪个位置分配内存?一定在堆中分配内存么？展开聊聊逃逸分析
    *   有几种分配方式?可以展开聊聊 CAS 和 TLAB
    *   内存分配线程安全么？
*   然后讲讲初始化
    *   结合对象内存布局聊聊 MarkWord，Klass Pointer 和实例数据
    *   在 MarkWord 的技术上聊聊和多线程、锁相关的内容
    *   可以再拓展讲讲一个对象到底占多少内存？基本类型和引用类型的字段到底占多少内存？最好自己有实践并测试过

### 3.2.2 所有对象都在堆内存上分配使用空间么？

问题剖析：当面试官提出这个问题时，主要考察你对对象实例化中内存分配的理解。这部分内容可以从两个角度出发，先谈在堆内存上分配的场景，然后谈特殊情况，即不在堆上分配的情况。

回答模板：

*   先讲讲对象分为哪几类？数组对象和非数组对象
*   然后讲在堆中分配内存的情况
    *   在 Eden 区分配的情况
    *   在 TLAB 分配的情况？TLAB 满了怎么办？
*   然后讲不在堆分配的情况
    *   啥时候不在堆分配？
    *   扩展讲讲逃逸分析 JIT

### 3.2.3 对象实例化时能保证线程安全么？

问题剖析：当面试官提出这个问题时，主要考察你对对象实例化中内存分配的细节是否掌握。

回答模板：不能。先说一下 JVM 是否天然支持多线程场景分配内存的线程安全问题，然后给出一个实例讲解如何发生线程不安全，最后讲讲解决思路。

**相关理论：**
代码在线上运行时，必须要保证创建对象的线程安全。考虑如下一种场景，对象 A 正在分配内存，然而指针还没修改时对象 B 完成了同一块内存的分配并修改了指针。虚拟机一般采用两种方式来保证这一阶段的线程安全：

*   CAS + 失败重试
*   TLAB

CAS 是无锁并发的典型实现。CAS 不进行加锁而是假设没有冲突而去完成某项操作。如果因为冲突失败就自旋重试，直到成功为止。虚拟机采用 CAS+失败自旋重试的方式保证更新操作的原子性。

### 3.2.4 请谈谈你对对象头的理解，是否知道每个部分所占字节数？

问题剖析：当面试官提出这个问题时，主要考察你对对象头初始化的细节是否掌握。

回答模板：

*   先讲对象头是什么？可以结合对象内存模型展开分析
    *   对象头有几部分？
    *   数组和非数组在对象头有啥区别？
*   然后以 32 位 JVM 为例分析对象头的组成结构
    *   MarkWord 都有哪些属性，每个属性占多少个 bit？
    *   MarkWord 在多线程场景下，锁粗化时发生了什么变化？
    *   Klass Pointer 是什么？

### 3.2.5 一个对象到底占多少空间

言多无用，不如实操一下。这里引用了一个包用于测试不同对象所占的内存空间：

```cpp
<dependency>
  <groupId>org.openjdk.jol</groupId>
  <artifactId>jol-core</artifactId>
  <version>0.8</version>
</dependency>
```

下面给出多个例子检验对象实际占用空间大小。

##### 空对象

```cpp
public static void main(String[] args) {

    System.out.println("sizeOf(new Object()) = " + ClassLayout.parseInstance(new Object()).toPrintable());
}
```

在打开指针压缩时将会输出(-XX:+UseCompressedOops)：

```cpp
sizeOf(new Object()) = java.lang.Object object internals:
 OFFSET  SIZE   TYPE DESCRIPTION                               VALUE
      0     4        (object header)                           01 00 00 00 (00000001 00000000 00000000 00000000) (1)
      4     4        (object header)                           00 00 00 00 (00000000 00000000 00000000 00000000) (0)
      8     4        (object header)                           e5 01 00 20 (11100101 00000001 00000000 00100000) (536871397)
     12     4        (loss due to the next object alignment)
Instance size: 16 bytes
Space losses: 0 bytes internal + 4 bytes external = 4 bytes total
```

打开指针压缩时，对象头部总计 16bytes：

*   MarkWord 占 8byte
*   KlassPointer 占 4byte
*   对齐填充部分占 4byte

在关闭指针压缩时将会输出(-XX:-UseCompressedOops)：

```cpp
sizeOf(new Object()) = java.lang.Object object internals:
 OFFSET  SIZE   TYPE DESCRIPTION                               VALUE
      0     4        (object header)                           01 00 00 00 (00000001 00000000 00000000 00000000) (1)
      4     4        (object header)                           00 00 00 00 (00000000 00000000 00000000 00000000) (0)
      8     4        (object header)                           00 1c 4b 17 (00000000 00011100 01001011 00010111) (390798336)
     12     4        (object header)                           00 00 00 00 (00000000 00000000 00000000 00000000) (0)
Instance size: 16 bytes
Space losses: 0 bytes internal + 0 bytes external = 0 bytes total
```

在关闭指针压缩时，对象头部总计 16bytes：

*   MarkWord 占 8byte
*   KlassPointer 占 8byte
*   无对齐填充部分

##### 数组对象

默认打开指针压缩。

先来看看`int[]`的实际情况

```cpp
public static void main(String[] args) {

    int[] data = new int[5];
    System.out.println("sizeOf(int[]) = " + ClassLayout.parseInstance(data).toPrintable());
}
```

```cpp
sizeOf(int[]) = [I object internals:
 OFFSET  SIZE   TYPE DESCRIPTION                               VALUE
      0     4        (object header)                           01 00 00 00 (00000001 00000000 00000000 00000000) (1)
      4     4        (object header)                           00 00 00 00 (00000000 00000000 00000000 00000000) (0)
      8     4        (object header)                           6d 01 00 20 (01101101 00000001 00000000 00100000) (536871277)
     12     4        (object header)                           05 00 00 00 (00000101 00000000 00000000 00000000) (5)
     16    20    int [I.<elements>                             N/A
     36     4        (loss due to the next object alignment)
Instance size: 40 bytes
Space losses: 0 bytes internal + 4 bytes external = 4 bytes total
```

对象总计 40byte：

*   MarkWord 占 8byte
*   KlassPointer 占 4byte
*   数组长度占 4byte
*   实例数据占 20byte
*   对齐填充占 4byte

再来看看`boolean[]`的实际情况

```cpp
public static void main(String[] args) {

    boolean[] data = new boolean[5];
    System.out.println("sizeOf(int[]) = " + ClassLayout.parseInstance(data).toPrintable());
}
```

```cpp
sizeOf(int[]) = [Z object internals:
 OFFSET  SIZE      TYPE DESCRIPTION                               VALUE
      0     4           (object header)                           01 00 00 00 (00000001 00000000 00000000 00000000) (1)
      4     4           (object header)                           00 00 00 00 (00000000 00000000 00000000 00000000) (0)
      8     4           (object header)                           05 00 00 20 (00000101 00000000 00000000 00100000) (536870917)
     12     4           (object header)                           05 00 00 00 (00000101 00000000 00000000 00000000) (5)
     16     5   boolean [Z.<elements>                             N/A
     21     3           (loss due to the next object alignment)
Instance size: 24 bytes
Space losses: 0 bytes internal + 3 bytes external = 3 bytes total
```

和`int[]`不同`boolean[]`实例部分每个元素仅占 1byte，这是需要注意的点！！！

##### 复杂对象

下面给出一个较为复杂的例子，请大家自行分析对象内存占用情况并在复杂的条件下进行测试。

```cpp
public class SizeOfAgentTest {

    public static void main(String[] args) {

        System.out.println("sizeOf(int[]) = " + ClassLayout.parseInstance(new D()).toPrintable());
    }
}

class A {
    int size;
    Integer height;

    A() {
        this.size = 0;
        this.height = 127;
    }
}

class B {
    int size;
    Integer height;

    B() {
        this.size = 0;
        this.height = 128;
    }
}

class C{
    A a;
    B[] b = new B[2];
    int c;

    C() {
        a = new A();
        for (int i = 0; i < b.length; i++) {
            b[i] = new B();
        }
    }
}

class D extends C {
    byte d;
    byte[] ds;
    String str;
    double[] areas;

    D() {
        this.d = 0;
        ds = new byte[5];
        str = new String("");
        areas = new double[5];
    }
}
```

```cpp
sizeOf(int[]) = com.zx.service.impl.D object internals:
 OFFSET  SIZE                      TYPE DESCRIPTION                               VALUE
      0     4                           (object header)                           01 00 00 00 (00000001 00000000 00000000 00000000) (1)
      4     4                           (object header)                           00 00 00 00 (00000000 00000000 00000000 00000000) (0)
      8     4                           (object header)                           82 c1 00 20 (10000010 11000001 00000000 00100000) (536920450)
     12     4                       int C.c                                       0
     16     4     com.zx.service.impl.A C.a                                       (object)
     20     4   com.zx.service.impl.B[] C.b                                       [(object), (object)]
     24     1                      byte D.d                                       0
     25     3                           (alignment/padding gap)                  
     28     4                    byte[] D.ds                                      [0, 0, 0, 0, 0]
     32     4          java.lang.String D.str                                     (object)
     36     4                  double[] D.areas                                   [0.0, 0.0, 0.0, 0.0, 0.0]
Instance size: 40 bytes
Space losses: 3 bytes internal + 0 bytes external = 3 bytes total
```

### 3.2.6 Hello World 全过程

```cpp
public static void main(String[] args) {    
    System.out.println("Hello World!"); 
}
```

一段大家很熟悉的代码，但是想把这段代码到底执行力哪些操作讲清楚是一件很难的事情。这个问题是面试常见问题，回答的好容易得到面试官的青睐。因此，大家在学习 JVM 的时候，要经常反问自己，结合实践不断打磨自己的答案。本题没有标准答案，这里仅抛砖引玉给出一些个人见解。

*   .java 编译成.class 文件（通过 javac）
*   JVM 执行类加载
    *   根据命令行传入的参数找到.class 文件
        *   通过文件系统
        *   通过 zip/jar 压缩包
        *   通过网络 IO
    *   .class 文件校验
        *   文件格式校验：魔数、主副版本号、常量池、访问标志、类索引、父类索引、接口索引
        *   元数据校验
        *   字节码校验
        *   符号链接校验
    *   准备（初始化零值）、解析、初始化
    *   可以拓展讲下类加载器、双亲委派模型以及运行时的类加载
    *   可以拓展讲下 JVM 内存模型，类加载重点分析方法区，再结合不同版本中方法区的调整变化进行叙述
*   JVM 实例化对象
    *   分配内存
        *   指针碰撞、空闲列表
        *   线程安全问题、TLAB
    *   初始化，注意此处与类加载中初始化的区别
    *   设置对象头
        *   讲讲对象头中的内存如何分配
        *   拓展讲下与多线程和锁相关的字段
        *   拓展讲下开启/关闭指针压缩的区别
*   讲下 HelloWorld 中涉及到哪些变量和对象
    *   String[]在哪存放？
    *   "HelloWorld"在哪存放？初始化的时机，可以拓展讲讲方法区字符串常量池，以及其随 JDK 版本演化发生的变化，以及`String.intern()`
*   讲下`System.out.println("Hello World!")`涉及了哪些类的加载？方法调用时如何在类中进行搜索？讲下方法调用涉及的栈和栈帧，是否会出现爆栈的情况？

讲完 HelloWorld，可以再将场景复杂化，比如在微服务场景，程序长期运行，必然会涉及到 GC，再对 GC 进行展开介绍。

## 3.3 总结

对象实例化部分涉及理论和实践，需要大家多动手多思考。与对象实例化相关的其他 JVM 知识如下：

*   类加载
*   JVM 内存模型
*   多线程、锁

最后希望大家多思考`HelloWorld`的执行流程，这个问题没有标准答案，大家也可以自己总结一份答案。在面试时心中务必要有一条明确的线索，将各个知识点串联起来，这样面试官才会感觉到你真的有所思考。