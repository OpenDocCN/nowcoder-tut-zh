# 第一章 第 9 节 C++面向对象-2

> 原文：[`www.nowcoder.com/tutorial/10069/7d3d13332c404d7cba1bec215f056c8b`](https://www.nowcoder.com/tutorial/10069/7d3d13332c404d7cba1bec215f056c8b)

#### 1.3.6 说说构造函数有几种，分别什么作用

**参考答案**

C++中的构造函数可以分为 4 类：默认构造函数、初始化构造函数、拷贝构造函数、移动构造函数。

1.  默认构造函数和初始化构造函数。 在定义类的对象的时候，完成对象的初始化工作。

    ```cpp
    class Student
    {
    public:
        //默认构造函数
        Student()
        {
           num=1001;
           age=18;       
        }
        //初始化构造函数
        Student(int n,int a):num(n),age(a){}
    private:
        int num;
        int age;
    };
    int main()
    {
        //用默认构造函数初始化对象 S1
        Student s1;
        //用初始化构造函数初始化对象 S2
        Student s2(1002,18);
        return 0;
    }

    ```

    有了有参的构造了，编译器就不提供默认的构造函数。

2.  拷贝构造函数

    ```cpp
    #include "stdafx.h"
    #include "iostream.h"

    class Test
    {
        int i;
        int *p;
    public:
        Test(int ai,int value)
        {
            i = ai;
            p = new int(value);
        }
        ~Test()
        {
            delete p;
        }
        Test(const Test& t)
        {
            this->i = t.i;
            this->p = new int(*t.p);
        }
    };
    //复制构造函数用于复制本类的对象
    int main(int argc, char* argv[])
    {
        Test t1(1,2);
        Test t2(t1);//将对象 t1 复制给 t2。注意复制和赋值的概念不同
        return 0;
    }
    ```

    赋值构造函数默认实现的是值拷贝（浅拷贝）。

3.  移动构造函数。用于将其他类型的变量，隐式转换为本类对象。下面的转换构造函数，将 int 类型的 r 转换为 Student 类型的对象，对象的 age 为 r，num 为 1004.

    ```cpp
     Student(int r)
     ｛
         int num=1004；
         int age= r；

      ｝

    ```

#### 1.3.7 只定义析构函数，会自动生成哪些构造函数

**参考答案**

只定义了析构函数，编译器将自动为我们生成拷贝构造函数和默认构造函数。

默认构造函数和初始化构造函数。 在定义类的对象的时候，完成对象的初始化工作。

```cpp
class Student
{
public:
    //默认构造函数
    Student()
    {
       num=1001;
       age=18;       
    }
    //初始化构造函数
    Student(int n,int a):num(n),age(a){}
private:
    int num;
    int age;
};
int main()
{
    //用默认构造函数初始化对象 S1
    Student s1;
    //用初始化构造函数初始化对象 S2
    Student s2(1002,18);
    return 0;
}

```

有了有参的构造了，编译器就不提供默认的构造函数。

拷贝构造函数

```cpp
#include "stdafx.h"
#include "iostream.h"

class Test
{
    int i;
    int *p;
public:
    Test(int ai,int value)
    {
        i = ai;
        p = new int(value);
    }
    ~Test()
    {
        delete p;
    }
    Test(const Test& t)
    {
        this->i = t.i;
        this->p = new int(*t.p);
    }
};

//复制构造函数用于复制本类的对象

int main(int argc, char* argv[])
{
    Test t1(1,2);
    Test t2(t1);//将对象 t1 复制给 t2。注意复制和赋值的概念不同。

    return 0;
}
```

赋值构造函数默认实现的是值拷贝（浅拷贝）。

**答案解析**

示例如下：

```cpp
class HasPtr
{
public:
    HasPtr(const string& s = string()) :ps(new string(s)), i(0) {}
    ~HasPtr() { delete ps; }
private:
    string * ps;
    int i;
};
```

如果类外面有这样一个函数：

```cpp
HasPtr f(HasPtr hp)
{
    HasPtr ret = hp;
    ///... 其他操作
    return ret;

}
```

当函数执行完了之后，将会调用 hp 和 ret 的析构函数，将 hp 和 ret 的成员 ps 给 delete 掉，但是由于 ret 和 hp 指向了同一个对象，因此该对象的 ps 成员被 delete 了两次，这样产生一个未定义的错误，所以说，如果一个类定义了析构函数，那么它要定义自己的拷贝构造函数和默认构造函数。

#### 1.3.8 说说一个类，默认会生成哪些函数

**参考答案**

定义一个空类

```cpp
class Empty
{
};
```

默认会生成以下几个函数

1.  无参的构造函数

    在定义类的对象的时候，完成对象的初始化工作。

```cpp
Empty()
{
}
```

2.  拷贝构造函数

    拷贝构造函数用于复制本类的对象

```cpp
Empty(const Empty& copy)
{
}
```

3.  赋值运算符

```cpp
Empty& operator = (const Empty& copy)
{
}
```

4.  析构函数（非虚）

```cpp
~Empty()
{
}
```