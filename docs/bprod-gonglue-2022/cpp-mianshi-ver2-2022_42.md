# 第五章 第 2 节 C++设计模式-2

> 原文：[`www.nowcoder.com/tutorial/10069/ffd309965a844982b5fa5571fc829038`](https://www.nowcoder.com/tutorial/10069/ffd309965a844982b5fa5571fc829038)

### 5.2 简述一下单例设计模式的懒汉式和饿汉式，如何保证线程安全

**参考回答**

1.  懒汉式设计模式

    懒汉模式的特点是延迟加载，比如配置文件，采用懒汉式的方法，配置文件的实例直到用到的时候才会加载，不到万不得已就不会去实例化类，也就是说在第一次用到类实例的时候才会去实例化。

2.  饿汉模式

    单例类定义的时候就进行实例化。因为 main 函数执行之前，全局作用域的类成员静态变量 m_Instance 已经初始化，故**没有多线程的问题**。

**答案解析**

懒汉设计模式两种实现方式线程不安全问题的解决：

（1）**懒汉模式实现一：静态指针 + 用到时初始化**

```cpp
//代码实例（线程不安全）
template<typename T>
class Singleton
{
public:
static T& getInstance()
{
    if (!value_)
    {
        value_ = new T();
    }
    return *value_;
}
private:
    Singleton();
    ~Singleton();
    static T* value_;
};
template<typename T>
T* Singleton<T>::value_ = NULL;
```

在单线程中，这样的写法是可以正确使用的，但是在多线程中就不行了，该方法是**线程不安全**的。
a. 假如线程 A 和线程 B, 这两个线程要访问 getInstance 函数，线程 A 进入 getInstance 函数，并检测 if 条件，由于是第一次进入，value 为空，if 条件成立，准备创建对象实例。
b. 但是，线程 A 有可能被 OS 的调度器中断而挂起睡眠，而将控制权交给线程 B。
c. 线程 B 同样来到 if 条件，发现 value 还是为 NULL，因为线程 A 还没来得及构造它就已经被中断了。此时假设线程 B 完成了对象的创建，并顺利的返回。
d. 之后线程 A 被唤醒，继续执行 new 再次创建对象，这样一来，两个线程就构建两个对象实例，这就破坏了唯一性。
另外，还存在内存泄漏的问题，new 出来的东西始终没有释放，下面是一种饿汉式的一种线程安全的改进。

```cpp
//代码实例（线程安全）
emplate<typename T>
class Singleton
{
public:
static T& getInstance()
{
    if (!value_)
    {
        value_ = new T();
    }
    return *value_;
}
private:
     class CGarbo     
    {    
    public:    
        ~CGarbo()    
        {    
            if(Singleton::value_)    
                delete Singleton::value_;    
        }    
    };    
    static CGarbo Garbo;    
    Singleton();
    ~Singleton();
    static T* value_;
};
template<typename T>
T* Singleton<T>::value_ = NULL;
```

在程序运行结束时，系统会调用 Singleton 的静态成员 Garbo 的析构函数，该析构函数会删除单例的唯一实例。使用这种方法释放单例对象有以下特征：

a. 在单例类内部定义专有的嵌套类；
b. 在单例类内定义私有的专门用于释放的静态成员；
c. 利用程序在结束时析构全局变量的特性，选择最终的释放时机。

（2）**懒汉模式实现二：局部静态变量**

```cpp
//代码实例（线程不安全）
template<typename T>
class Singleton
{
public:
static T& getInstance() 
{
    static T instance;
    return instance;
}

private:
    Singleton(){};
    Singleton(const Singleton&);
    Singleton& operator=(const Singleton&);
};
```

同样，静态局部变量的实现方式也是**线程不安全**的。如果存在多个单例对象的析构顺序有依赖时，可能会出现程序崩溃的危险。
对于局部静态对象的也是一样的。因为 static T instance；语句不是一个原子操作，在第一次被调用时会调用 Singleton 的构造函数，而如果构造函数里如果有多条初始化语句，则初始化动作可以分解为多步操作，就存在多线程竞争的问题。
为什么存在多个单例对象的析构顺序有依赖时，可能会出现程序崩溃的危险？

原因：由于静态成员是在第一次调用函数 GetInstance 时进行初始化，调用构造函数的，因此构造函数的调用顺序时可以唯一确定了。对于析构函数，我们只知道其调用顺序和构造函数的调用顺序相反，但是如果几个 Singleton 类的析构函数之间也有依赖关系，而且出现类似单例实例 A 的析构函数中使用了单例实例 B，但是程序析构时是先调用实例 B 的析构函数，此时在 A 析构函数中使用 B 时就可能会崩溃。

```cpp
//代码实例（线程安全）
#include <string>
#include <iostream>
using namespace std;
class Log
{
public:
    static Log* GetInstance()
    {
        static Log oLog;
        return &oLog;
    }

    void Output(string strLog)
    {
        cout<<strLog<<(*m_pInt)<<endl;
    }
private:
    Log():m_pInt(new int(3))
    {
    }
    ~Log()
    {cout<<"~Log"<<endl;
        delete m_pInt;
        m_pInt = NULL;
    }
    int* m_pInt;
};

class Context
{
public:
    static Context* GetInstance()
    {
        static Context oContext;
        return &oContext;
    }
    ~Context()
    {
        Log::GetInstance()->Output(__FUNCTION__);
    }

    void fun()
    {
        Log::GetInstance()->Output(__FUNCTION__);
    }
private:
    Context(){}
    Context(const Context& context);
};

int main(int argc, char* argv[])
{
    Context::GetInstance()->fun();
    return 0;
}
```

在这个反例中有两个 Singleton: Log 和 Context，Context 的 fun 和析构函数会调用 Log 来输出一些信息，结果程序 Crash 掉了，该程序的运行的序列图如下（其中画红框的部分是出问题的部分）：

![单例模式程序运行序列图](img/12f0c223a3b8c834bbed0c9b9fc68226.png)

**解决方案：**对于析构的顺序，我们可以用一个容器来管理它，根据单例之间的依赖关系释放实例，对所有的实例的析构顺序进行排序，之后调用各个单例实例的析构方法，如果出现了循环依赖关系，就给出异常，并输出循环依赖环。