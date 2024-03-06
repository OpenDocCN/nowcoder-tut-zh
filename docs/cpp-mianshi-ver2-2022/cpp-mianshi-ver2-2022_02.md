# 第一章 第 1 节 语言基础-1

> 原文：[`www.nowcoder.com/tutorial/10069/cbbd53166e7240dba9ced2b3c03d89b8`](https://www.nowcoder.com/tutorial/10069/cbbd53166e7240dba9ced2b3c03d89b8)

### 1.1 语言基础

#### 1.1.1 简述下 C++语言的特点

**参考回答**

1.  C++在 C 语言基础上引入了**面对对象**的机制，同时也**兼容 C 语言**。
2.  C++有三大特性（1）封装。（2）继承。（3）多态；
3.  C++语言编写出的程序结构清晰、易于扩充，程序**可读性好**。
4.  C++生成的代码**质量高**，运行**效率高**，仅比汇编语言慢 10%～20%；
5.  C++更加安全，增加了 const 常量、引用、四类 cast 转换（static_cast、dynamic_cast、const_cast、reinterpret_cast）、智能指针、try—catch 等等；
6.  C++**可复用性**高，C++引入了**模板**的概念，后面在此基础上，实现了方便开发的标准模板库 STL（Standard Template Library）。
7.  同时，C++是**不断在发展**的语言。C++后续版本更是发展了不少新特性，如 C++11 中引入了 nullptr、auto 变量、Lambda 匿名函数、右值引用、智能指针。

#### 1.1.2 说说 C 语言和 C++的区别

**参考回答**

1.  C 语言是 C++的子集，C++可以很好兼容 C 语言。但是 C++又有很多**新特性**，如引用、智能指针、auto 变量等。
2.  C++是**面对对象**的编程语言；C 语言是**面对过程**的编程语言。
3.  C 语言有一些不安全的语言特性，如指针使用的潜在危险、强制转换的不确定性、内存泄露等。而 C++对此增加了不少新特性来**改善安全性**，如 const 常量、引用、cast 转换、智能指针、try—catch 等等；
4.  C++**可复用性**高，C++引入了**模板**的概念，后面在此基础上，实现了方便开发的标准模板库 STL。C++的 STL 库相对于 C 语言的函数库**更灵活、更通用**。

#### 1.1.3 说说 C++中 struct 和 class 的区别

**参考回答**

1.  struct 一般用于描述一个数据结构集合，而 class 是对一个对象数据的封装；

2.  struct 中默认的访问控制权限是 public 的，而 class 中默认的访问控制权限是 private 的，例如：

    ```cpp
    struct A{
        int iNum;    // 默认访问控制权限是 public
    }
    class B{
        int iNum;    // 默认访问控制权限是 private
    }
    ```

3.  在继承关系中，struct 默认是公有继承，而 class 是私有继承；

4.  class 关键字可以用于定义模板参数，就像 typename，而 struct 不能用于定义模板参数，例如：

    ```cpp
    template<typename T, typename Y>    // 可以把 typename 换成 class 
    int Func(const T& t, const Y& y) { 
        //TODO 
    }
    ```

**答案解析**

1.  C++ 中的 struct 是对 C 中的 struct 进行了扩充，它们在声明时的区别如下：

    |  | C | C++ |
    | :-- | :-: | :-: |
    | 成员函数 | 不能有 | 可以 |
    | 静态成员 | 不能有 | 可以 |
    | 访问控制 | 默认 public，不能修改 | public/private/protected |
    | 继承关系 | 不可以继承 | 可从类或者其他结构体继承 |
    | 初始化 | 不能直接初始化数据成员 | 可以 |

2.  使用时的区别：C 中使用结构体需要加上 struct 关键字，或者对结构体使用 typedef 取别名，而 C++ 中可以省略 struct 关键字直接使用，例如：

    ```cpp
    struct Student{
        int  iAgeNum;
        string strName;
    }
    typedef struct Student Student2;    //C 中取别名

    struct Student stu1;    // C 中正常使用
    Student2 stu2;            // C 中通过取别名的使用
    Student stu3;            // C++ 中使用
    ```