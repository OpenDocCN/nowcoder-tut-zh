# 第一章 第 20 节 C++新特性-2

> 原文：[`www.nowcoder.com/tutorial/10069/325e349e1cd3429e872c2b8ad071da98`](https://www.nowcoder.com/tutorial/10069/325e349e1cd3429e872c2b8ad071da98)

#### 1.5.4 简述 C++ 右值引用与转移语义

**参考回答**

1.  右值引用

    一般来说，不能取地址的表达式，就是右值引用，能取地址的，就是左值。

```cpp
class A { };
A & r = A(); //error,A()是无名变量，是右值
A && r = A(); //ok,r 是右值引用
```

2.  转移语义

    ​ move 本意为 "移动"，但该函数并不能移动任何数据，它的功能很简单，就是将某个左值强制转化为右值。基于 move() 函数特殊的功能，其常用于实现移动语义。

**答案解析**

1.  右值引用

​ C++98/03 标准中就有引用，使用 "&" 表示。但此种引用方式有一个缺陷，即正常情况下只能操作 C++ 中的左值，无法对右值添加引用。举个例子：

```cpp
int num = 10;
int &b = num; //正确
int &c = 10; //错误
```

​ 如上所示，编译器允许我们为 num 左值建立一个引用，但不可以为 10 这个右值建立引用。因此，C++98/03 标准中的引用又称为左值引用。

​ **注意：**虽然 C++98/03 标准不支持为右值建立非常量左值引用，但允许使用常量左值引用操作右值。也就是说，常量左值引用既可以操作左值，也可以操作右值，例如：

```cpp
int num = 10;
const int &b = num;
const int &c = 10;
```

​ 我们知道，右值往往是没有名称的，因此要使用它只能借助引用的方式。这就产生一个问题，实际开发中我们可能需要对右值进行修改（实现移动语义时就需要），显然左值引用的方式是行不通的。

​ 为此，C++11 标准新引入了另一种引用方式，称为右值引用，用 "&&" 表示。

​ **注意：**和声明左值引用一样，右值引用也必须立即进行初始化操作，且只能使用右值进行初始化，比如：

```cpp
int num = 10;
//int && a = num;  //右值引用不能初始化为左值
int && a = 10;
```

​ 和常量左值引用不同的是，右值引用还可以对右值进行修改。例如：

```cpp
int && a = 10;
a = 100;
cout << a << endl;
/*    程序运行结果：        
        100    
*/          
```

​ 另外值得一提的是，C++ 语法上是支持定义常量右值引用的，例如：

```cpp
const int&& a = 10;//编译器不会报错
```

​ 但这种定义出来的右值引用并无实际用处。一方面，右值引用主要用于移动语义和完美转发，其中前者需要有修改右值的权限；其次，常量右值引用的作用就是引用一个不可修改的右值，这项工作完全可以交给常量左值引用完成。

2.  move 语义

```cpp
//程序实例
#include <iostream>
using namespace std;
class first {
public:
    first() :num(new int(0)) {
        cout << "construct!" << endl;
    }
    //移动构造函数
    first(first &&d) :num(d.num) {
        d.num = NULL;
        cout << "first move construct!" << endl;
    }
public:    //这里应该是 private，使用 public 是为了更方便说明问题
    int *num;
};
class second {
public:
    second() :fir() {}
    //用 first 类的移动构造函数初始化 fir
    second(second && sec) :fir(move(sec.fir)) {
        cout << "second move construct" << endl;
    }
public:    //这里也应该是 private，使用 public 是为了更方便说明问题
    first fir;
};
int main() {
    second oth;
    second oth2 = move(oth);
    //cout << *oth.fir.num << endl;   //程序报运行时错误
    return 0;
}

/*    程序运行结果：
          construct!
        first move construct!
        second move construct
*/            
```

#### 1.5.5 简述 C++ 中智能指针的特点

**参考回答**

1.  C++中的智能指针有 4 种，分别为：**shared_ptr、unique_ptr、weak_ptr、auto_ptr**，其中 auto_ptr 被 C++11 弃用。

2.  **为什么要使用智能指针**：智能指针的作用是管理一个指针，因为存在申请的空间在函数结束时忘记释放，造成内存泄漏的情况。使用智能指针可以很大程度上避免这个问题，因为智能指针就是一个类，当超出了类的作用域时，类会自动调用析构函数，自动释放资源。

3.  四种指针各自特性

    **（1）auto_ptr**

    ​ auto 指针存在的问题是，两个智能指针同时指向一块内存，就会两次释放同一块资源，自然报错。

    **（2）unique_ptr**

    ​ unique 指针规定一个智能指针独占一块内存资源。当两个智能指针同时指向一块内存，编译报错。

    ​ **实现原理：**将拷贝构造函数和赋值拷贝构造函数申明为 private 或 delete。不允许拷贝构造函数和赋值操作符，但是支持移动构造函数，通过 std:move 把一个对象指针变成右值之后可以移动给另一个 unique_ptr

    **（3）shared_ptr**

    ​ 共享指针可以实现多个智能指针指向相同对象，该对象和其相关资源会在引用为 0 时被销毁释放。

    ​ **实现原理：**有一个引用计数的指针类型变量，专门用于引用计数，使用拷贝构造函数和赋值拷贝构造函数时，引用计数加 1，当引用计数为 0 时，释放资源。

**注意：**weak_ptr、shared_ptr 存在一个问题，当两个 shared_ptr 指针相互引用时，那么这两个指针的引用计数不会下降为 0，资源得不到释放。因此引入 weak_ptr，weak_ptr 是弱引用，weak_ptr 的构造和析构不会引起引用计数的增加或减少。

**答案解析**

​ 无

#### 1.5.6 weak_ptr 能不能知道对象计数为 0，为什么？

**参考回答**

​ 不能。

​ weak_ptr 是一种不控制对象生命周期的智能指针，它指向一个 shared_ptr 管理的对象。进行该对象管理的是那个引用的 shared_ptr。weak_ptr 只是提供了对管理 对象的一个访问手段。weak_ptr 设计的目的只是为了配合 shared_ptr 而引入的一种智能指针，配合 shared_ptr 工作，它只可以从一个 shared_ptr 或者另一个 weak_ptr 对象构造，**它的构造和析构不会引起计数的增加或减少**。

**答案解析**
无

#### 1.5.7 weak_ptr 如何解决 shared_ptr 的循环引用问题？

**参考回答**

​ 为了解决循环引用导致的内存泄漏，引入了弱指针 weak_ptr，weak_ptr 的构造函数不会修改引用计数的值，从而不会对对象的内存进行管理，其类似一个普通指针，但是不会指向引用计数的共享内存，但是可以检测到所管理的对象是否已经被释放，从而避免非法访问。

**答案解析**

​ 参见 1.2.10

#### 1.5.8 share_ptr 怎么知道跟它共享对象的指针释放了

**参考回答**

​ 多个 shared_ptr 对象可以同时托管一个指针，系统会维护一个托管计数。当无 shared_ptr 托管该指针时，delete 该指针。

**答案解析**
无

#### 1.5.9 说说智能指针及其实现，shared_ptr 线程安全性，原理

**参考回答**

​ 1\. C++里面的**四个智能指针**: **auto_ptr, shared_ptr, weak_ptr, unique_ptr** 其中后三个是 c++11 支持，并且第一个已经被 11 弃用。

2.  为什么要使用智能指针

    ​ 智能指针的作用是管理一个指针，因为存在以下这种情况：申请的空间在函数结束时忘记释放，造成内存泄漏。使用智能指针可以很大程度上的避免这个问题，因为智能指针就是一个类，当超出了类的作用域是，类会自动调用析构函数，析构函数会自动释放资源。所以智能指针的作用原理就是在函数结束时自动释放内存空间，不需要手动释放内存空间。

3.  四种指针分别解决的问题以及各自特性如下：

    （1）auto_ptr（C++98 的方案，C++11 已经弃用）

    ```cpp
    //程序实例
    auto_ptr<string> p1(new string("I reigned loney as a cloud."));
    auto_ptr<string> p2;
    p2=p1; //auto_ptr 不会报错
    ```

    ​ **采用所有权模式。**此时不会报错，p2 剥夺了 p1 的所有权，但是当程序运行时访问 p1 将会报错。所以 auto_ptr 的**缺点**是：存在潜在的内存崩溃问题。

    （2）unique_ptr（替换 auto_ptr）

    ​ unique_ptr**实现独占式拥有或严格拥有概念**，保证同一时间内只有一个智能指针可以指向该对象。**它对于避免资源泄露，例如，以 new 创建对象后因为发生异常而忘记调用 delete 时的情形特别有用。**

    ```cpp
    //程序实例
    auto_ptr<string> p3(new string("I reigned loney as a cloud."));
    auto_ptr<string> p4;
    p4=p3; //此时不会报错
    ```

    ​ 采用所有权模式，和上面例子一样。编译器认为 P4=P3 非法，避免了 p3 不再指向有效数据的问题。因此，unique_ptr 比 auto_ptr 更安全。 另外 unique_ptr 还有更聪明的地方：当程序试图将一个 unique_ptr 赋值给另一个时，如果源 unique_ptr 是个临时右值，编译器允许这么做；如果源 unique_ptr 将存在一段时间，编译器将禁止这么做，比如以下代码：

    ```cpp
    //程序实例
    unique_ptr<string> pu1(new string ("hello world"));
    unique_ptr<string> pu2;
    pu2 = pu1;                                      // #1 not allowed
    unique_ptr<string> pu3;
    pu3 = unique_ptr<string>(new string ("You"));   // #2 allowed
    ```

    ​ 其中#1 留下悬挂的 unique_ptr(pu1)，这可能导致危害。而#2 不会留下悬挂的 unique_ptr，因为它调用 unique_ptr 的构造函数，该构造函数创建的临时对象在其所有权让给 pu3 后就会被销毁。这种随情况而已的行为表明，unique_ptr 优于允许两种赋值的 auto_ptr 。
    ​ **注意：**如果确实想执行类似与#1 的操作，要安全的重用这种指针，可给它赋新值。C++有一个标准库函数 std::move()，让你能够将一个 unique_ptr 赋给另一个。例如：

    ```cpp
    //程序实例
    unique_ptr<string> ps1, ps2;
    ps1 = demo("hello");
    ps2 = move(ps1);
    ps1 = demo("alexia");
    cout << *ps2 << *ps1 << endl;
    ```

    （3）shared_ptr（非常好使）

​ **shared_ptr 实现共享式拥有概念。**多个智能指针可以指向相同对象，该对象和其相关资源会在“最后一个引用被销毁”时候释放。从名字 share 就可以看出了**资源可以被多个指针共享，它使用计数机制来表明资源被几个指针共享。可以通过成员函数 use_count()来查看资源的所有者个数。**除了可以通过 new 来构造，还可以通过传入 auto_ptr, unique_ptr,weak_ptr 来构造。当我们调用 release()时，当前指针会释放资源所有权，计数减一。当计数等于 0 时，资源会被释放。

​ shared_ptr 是为了解决 auto_ptr 在对象所有权上的局限性(auto_ptr 是独占的), 在使用引用计数的机制上提供了可以共享所有权的智能指针。其成员函数如下：

​ **use_count** 返回引用计数的个数

​ **unique** 返回是否是独占所有权( use_count 为 1)

​ **swap** 交换两个 shared_ptr 对象(即交换所拥有的对象)

​ **reset** 放弃内部对象的所有权或拥有对象的变更, 会引起原有对象的引用计数的减少

​ **get** 返回内部对象(指针), 由于已经重载了()方法, 因此和直接使用对象是一样的.如 shared_ptr <int>sp(new int(1)); sp 与 sp.get()是等价的</int>

（4）weak_ptr

​ weak_ptr 是一种不控制对象生命周期的智能指针, 它指向一个 shared_ptr 管理的对象。进行该对象的内存管理的是那个强引用的 shared_ptr。weak_ptr 只是提供了对管理对象的一个访问手段。**weak_ptr 设计的目的是为配合 shared_ptr 而引入的一种智能指针来协助 shared_ptr 工作，它只可以从一个 shared_ptr 或另一个 weak_ptr 对象构造, 它的构造和析构不会引起引用记数的增加或减少。 ****weak_ptr 是用来解决 shared_ptr 相互引用时的死锁问题**，如果说两个 shared_ptr 相互引用，那么这两个指针的引用计数永远不可能下降为 0，资源永远不会释放。它是对对象的一种弱引用，不会增加对象的引用计数，和 shared_ptr 之间可以相互转化，shared_ptr 可以直接赋值给它，它可以通过调用 lock 函数来获得 shared_ptr。

```cpp
//程序实例
    class B;
    class A
    {
    public:
        shared_ptr<B> pb_;
        ~A()
    {
        cout<<"A delete\n";
    }
    };
    class B
    {
    public:
        shared_ptr<A> pa_;
        ~B()
    {
        cout<<"B delete\n";
    }
    };
    void fun()
    {
        shared_ptr<B> pb(new B());
        shared_ptr<A> pa(new A());
        pb->pa_ = pa;
        pa->pb_ = pb;
        cout<<pb.use_count()<<endl;
        cout<<pa.use_count()<<endl;
    }
    int main()
    {
        fun();
        return 0;
    }
```

​ 可以看到 fun 函数中 pa ，pb 之间互相引用，两个资源的引用计数为 2，当要跳出函数时，智能指针 pa，pb 析构时两个资源引用计数会减一，但是两者引用计数还是为 1，导致跳出函数时资源没有被释放（A B 的析构函数没有被调用），如果把其中一个改为 weak_ptr 就可以了，我们把类 A 里面的 shared_ptr pb_; 改为 weak_ptr pb; 运行结果如下，这样的话，资源 B 的引用开始就只有 1，当 pb 析构时，B 的计数变为 0，B 得到释放，B 释放的同时也会使 A 的计数减一，同时 pa 析构时使 A 的计数减一，那么 A 的计数为 0，A 得到释放。

​ **注意**：我们不能通过 weak_ptr 直接访问对象的方法，比如 B 对象中有一个方法 print()，我们不能这样访问，pa->pb_->print(); 英文 pb_ 是一个 weak_ptr，应该先把它转化为 shared_ptr，如：shared_ptr p = pa->pb_.lock(); p->print();

3.  **线程安全性**

    ​ 多线程环境下，调用不同 shared_ptr 实例的成员函数是不需要额外的同步手段的，即使这些 shared_ptr 拥有的是同样的对象。但是如果多线程访问（有写操作）同一个 shared_ptr，则需要同步，否则就会有 race condition 发生。也可以使用 shared_ptr overloads of atomic functions 来防止 race condition 的发生。

    ​ 多个线程同时读同一个 shared_ptr 对象是线程安全的，但是如果是多个线程对同一个 shared_ptr 对象进行读和写，则需要加锁。

    ​ 多线程读写 shared_ptr 所指向的同一个对象，不管是相同的 shared_ptr 对象，还是不同的 shared_ptr 对象，也需要加锁保护。例子如下：

    ```cpp
    //程序实例
    shared_ptr<long> global_instance = make_shared<long>(0);
    std::mutex g_i_mutex;

    void thread_fcn()
    {
        //std::lock_guard<std::mutex> lock(g_i_mutex);

        //shared_ptr<long> local = global_instance;

        for(int i = 0; i < 100000000; i++)
        {
            *global_instance = *global_instance + 1;
            //*local = *local + 1;
        }
    }

    int main(int argc, char** argv)
    {
        thread thread1(thread_fcn);
        thread thread2(thread_fcn);

        thread1.join();
        thread2.join();

        cout << "*global_instance is " << *global_instance << endl;

        return 0;
    }
    ```

    ​ 在线程函数 thread_fcn 的 for 循环中，2 个线程同时对 global_instance 进行加 1 的操作。这就是典型的非线程安全的场景，最后的结果是未定的，运行结果如下：

    ​ *global_instance is 197240539

    ​ 如果使用的是每个线程的局部 shared_ptr 对象 local，因为这些 local 指向相同的对象，因此结果也是未定的，运行结果如下： *global_instance is 160285803

    ​ 因此，这种情况下必须加锁，将 thread_fcn 中的第一行代码的注释去掉之后，不管是使用 global_instance，还是使用 local，得到的结果都是：

    ​ *global_instance is 200000000

**答案解析**

​ 无

#### 1.5.10 请你回答一下智能指针有没有内存泄露的情况

**参考回答**

​ 智能指针有内存泄露的情况发生。

1.  智能指针发生内存泄露的情况

    ​ 当两个对象同时使用一个 shared_ptr 成员变量指向对方，会造成循环引用，使引用计数失效，从而导致内存泄露。

2.  智能指针的内存泄漏如何解决？

    ```cpp
    为了解决循环引用导致的内存泄漏，引入了弱指针 weak_ptr，weak_ptr 的构造函数不会修改引用计数的值，从而不会对对象的内存进行管理，其类似一个普通指针，但是不会指向引用计数的共享内存，但是可以检测到所管理的对象是否已经被释放，从而避免非法访问。
    ```

**答案解析**

```cpp
//程序实例
#include <memory>
#include <iostream>
using namespace std;

class Child;
class Parent{
private:
    std::shared_ptr<Child> ChildPtr;
public:
    void setChild(std::shared_ptr<Child> child) {
        this->ChildPtr = child;
    }

    void doSomething() {
        if (this->ChildPtr.use_count()) {

        }
    }

    ~Parent() {

    }
};

class Child{
private:
    std::shared_ptr<Parent> ParentPtr;
public:
    void setPartent(std::shared_ptr<Parent> parent) {
        this->ParentPtr = parent;
    }
    void doSomething() {
        if (this->ParentPtr.use_count()) {

        }
    }
    ~Child() {
    }
};

int main() {
    std::weak_ptr<Parent> wpp;
    std::weak_ptr<Child> wpc;

    {
        std::shared_ptr<Parent>    p(new Parent);
        std::shared_ptr<Child> c(new Child);
        p->setChild(c);
        c->setPartent(p);
        wpp = p;
        wpc = c;
        std::cout << p.use_count() << std::endl;
        std::cout << c.use_count() << std::endl;
    }
    std::cout << wpp.use_count() << std::endl;
    std::cout << wpc.use_count() << std::endl;
    return 0;
}
/*    程序运行结果：
        2
        2
        1
        1
*/
```

​ 上述代码中，parent 有一个 shared_ptr 类型的成员指向孩子，而 child 也有一个 shared_ptr 类型的成员指向父亲。然后在创建孩子和父亲对象时也使用了智能指针 c 和 p，随后将 c 和 p 分别又赋值给 child 的智能指针成员 parent 和 parent 的智能指针成员 child。从而形成了一个循环引用。