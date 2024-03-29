# 第一章 第 1 节 Java 基础-1

> 原文：[`www.nowcoder.com/tutorial/10070/49f97d1622cf4d83ad413ca845b82f15`](https://www.nowcoder.com/tutorial/10070/49f97d1622cf4d83ad413ca845b82f15)

## 1\. Java 基础

#### 1.1 为什么 Java 代码可以实现一次编写、到处运行？

**参考答案**

JVM（Java 虚拟机）是 Java 跨平台的关键。

在程序运行前，Java 源代码（.java）需要经过编译器编译成字节码（.class）。在程序运行时，JVM 负责将字节码翻译成特定平台下的机器码并运行，也就是说，只要在不同的平台上安装对应的 JVM，就可以运行字节码文件。

同一份 Java 源代码在不同的平台上运行，它不需要做任何的改变，并且只需要编译一次。而编译好的字节码，是通过 JVM 这个中间的“桥梁”实现跨平台的，JVM 是与平台相关的软件，它能将统一的字节码翻译成该平台的机器码。

**注意事项**

1.  编译的结果是生成字节码、不是机器码，字节码不能直接运行，必须通过 JVM 翻译成机器码才能运行；
2.  跨平台的是 Java 程序、而不是 JVM，JVM 是用 C/C++开发的软件，不同平台下需要安装不同版本的 JVM。

#### 1.2 一个 Java 文件里可以有多个类吗（不含内部类）？

**参考答案**

1.  一个 java 文件里可以有多个类，但最多只能有一个被 public 修饰的类；
2.  如果这个 java 文件中包含 public 修饰的类，则这个类的名称必须和 java 文件名一致。

#### 1.3 说一说你对 Java 访问权限的了解

**参考答案**

Java 语言为我们提供了三种访问修饰符，即 private、protected、public，在使用这些修饰符修饰目标时，一共可以形成四种访问权限，即 private、defalut、protected、public，注意在不加任何修饰符时为 default 访问权限。

在修饰成员变量/成员方法时，该成员的四种访问权限的含义如下：

*   private：该成员可以被该类内部成员访问；
*   defalut：该成员可以被该类内部成员访问，也可以被同一包下其他的类访问；
*   protected：该成员可以被该类内部成员访问，也可以被同一包下其他的类访问，还可以被它的子类访问；
*   public：该成员可以被任意包下，任意类的成员进行访问。

在修饰类时，该类只有两种访问权限，对应的访问权限的含义如下：

*   defalut：该类可以被同一包下其他的类访问；
*   public：该类可以被任意包下，任意的类所访问。

#### 1.4 介绍一下 Java 的数据类型

**参考答案**

Java 数据类型包括基本数据类型和引用数据类型两大类。

基本数据类型有 8 个，可以分为 4 个小类，分别是整数类型（byte/short/int/long）、浮点类型（float/double）、字符类型（char）、布尔类型（boolean）。其中，4 个整数类型中，int 类型最为常用。2 个浮点类型中，double 最为常用。另外，在这 8 个基本类型当中，除了布尔类型之外的其他 7 个类型，都可以看做是数字类型，它们相互之间可以进行类型转换。

引用类型就是对一个对象的引用，根据引用对象类型的不同，可以将引用类型分为 3 类，即数组、类、接口类型。引用类型本质上就是通过指针，指向堆中对象所持有的内存空间，只是 Java 语言不再沿用指针这个说法而已。

**扩展阅读**

对于基本数据类型，你需要了解每种类型所占据的内存空间，面试官可能会追问这类问题：

*   byte：1 字节（8 位），数据范围是 `-2⁷ ~ 2⁷-1`。

*   short：2 字节（16 位），数据范围是 `-2¹⁵ ~ 2¹⁵-1`。

*   int：4 字节（32 位），数据范围是 `-2³¹ ~ 2³¹-1`。

*   long：8 字节（64 位），数据范围是 `-2⁶³ ~ 2⁶³-1`。

*   float：4 字节（32 位），数据范围大约是 `-3.4*10³⁸ ~ 3.4*10³⁸`。

*   double：8 字节（64 位），数据范围大约是 `-1.8*10³⁰⁸ ~ 1.8*10³⁰⁸`。

*   char：2 字节（16 位），数据范围是 `\u0000 ~ \uffff`。

*   boolean：Java 规范没有明确的规定，不同的 JVM 有不同的实现机制。

对于引用数据类型，你需要了解 JVM 的内存分布情况，知道引用以及引用对象存放的位置，详见 JVM 部分的题目。

#### 1.5 int 类型的数据范围是多少？

**参考答案**

int 类型占 4 字节（32 位），数据范围是 `-2³¹ ~ 2³¹-1`。