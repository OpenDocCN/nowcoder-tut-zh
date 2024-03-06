# 第二章 第 1 节 C 语言简介及环境设置

> 原文：[`www.nowcoder.com/tutorial/10031/c64c94e9243d41e197017cab2be7e8be`](https://www.nowcoder.com/tutorial/10031/c64c94e9243d41e197017cab2be7e8be)

## **一、****前言**

### **1.1**** C 语言简介**

C 语言是一种通用的高级语言，最初是由丹尼斯·里奇在贝尔实验室为开发 UNIX 操作系统而设计的。C 语言最开始是于 1972 年在 DEC PDP-11 计算机上被首次实现。

在 1978 年，布莱恩·柯林汉（Brian Kernighan）和丹尼斯·里奇（Dennis Ritchie）制作了 C 的第一个公开可用的描述，现在被称为 K&R 标准。

UNIX 操作系统，C 编译器，和几乎所有的 UNIX 应用程序都是用 C 语言编写的。由于各种原因，C 语言现在已经成为一种广泛使用的专业语言。

*   易于学习。
*   结构化语言。
*   它产生高效率的程序。
*   它可以处理底层的活动。
*   它可以在多种计算机平台上编译。

### **1.2**** C 语言环境设置**

#### **1.2.1**** 本地环境设置**

如果您想要设置 C 语言环境，您需要确保电脑上有以下两款可用的软件，文本编辑器和 C 编译器。

#### **1.2.2**** 文本编辑器**

1、这将用于输入您的程序。文本编辑器包括 Windows Notepad、OS Edit command、Brief、Epsilon、EMACS 和 vim/vi。

2、文本编辑器的名称和版本在不同的操作系统上可能会有所不同。例如，Notepad 通常用于 Windows 操作系统上，vim/vi 可用于 Windows 和 Linux/UNIX 操作系统上。

3、通过编辑器创建的文件通常称为源文件，源文件包含程序源代码。C 程序的源文件通常使用扩展名".c"。

4、在开始编程之前，请确保您有一个文本编辑器，且有足够的经验来编写一个计算机程序，然后把它保存在一个文件中，编译并执行它。

#### **1.2.3**** C 编译器**

1、写在源文件中的源代码是人类可读的源。它需要"编译"，转为机器语言，这样 CPU 可以按给定指令执行程序。

2、C 语言编译器用于把源代码编译成最终的可执行程序。这里假设您已经对编程语言编译器有基本的了解了。

3、最常用的免费可用的编译器是 GNU 的 C/C++ 编译器，如果您使用的是 HP 或 Solaris，则可以使用各自操作系统上的编译器。

4、以下部分将指导您如何在不同的操作系统上安装 GNU 的 C/C++编译器。这里同时提到 C/C++，主要是因为 GNU 的 gcc 编译器适合于 C 和 C++编程语言。

#### **1.2.4**** UNIX/Linux 上的安装**

如果您使用的是 Linux 或 UNIX，请在命令行使用下面的命令来检查您的系统上是否安装了 GCC：

```cpp
$ gcc -v
```

如果您的计算机上已经安装了 GNU 编译器，则会显示如下消息：

```cpp
Using built-in specs.
Target: i386-redhat-linux
Configured with: ../configure --prefix=/usr .......
Thread model: posix
gcc version 4.1.2 20080704 (Red Hat 4.1.2-46)
```

如果未安装 GCC，那么请按照[`gcc.gnu.org/install`](http://gcc.gnu.org/install)上的详细说明安装 GCC。本教程是基于 Linux 编写的，所有给定的实例都已在 Cent OS Linux 系统上编译过。

#### **1.2.5**** Mac OS 上的安装**

如果您使用的是 Mac OS X，最快捷的获取 GCC 的方法是从苹果的网站上下载 Xcode 开发环境，并按照安装说明进行安装。一旦安装上 Xcode，您就能使用 GNU 编译器。

Xcode 目前可从[developer.apple.com/technologies/tools](http://developer.apple.com/technologies/tools)上下载。

#### **1.2.6**** Windows 上的安装**

第一步、为了在 Windows 上安装 GCC，您需要安装 MinGW。为了安装 MinGW，请访问 MinGW 的主页[www.mingw.org](http://www.mingw.org)，进入 MinGW 下载页面，下载最新版本的 MinGW 安装程序，命名格式为 MinGW-<version>.exe。

第二步、当安装 MinWG 时，您至少要安装 gcc-core、gcc-g++、binutils 和 MinGW runtime，但是一般情况下都会安装更多其他的项。

第三步、添加您安装的 MinGW 的 bin 子目录到您的 PATH 环境变量中，这样您就可以在命令行中通过简单的名称来指定这些工具。

第四步、当完成安装时，您可以从 Windows 命令行上运行 gcc、g++、ar、ranlib、dlltool 和其他一些 GNU 工具。