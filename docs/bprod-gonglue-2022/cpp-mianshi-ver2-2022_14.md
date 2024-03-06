# 第一章 第 13 节 C++面向对象-6

> 原文：[`www.nowcoder.com/tutorial/10069/79d8fafcd1fa43cd99bb4c520d7bf62f`](https://www.nowcoder.com/tutorial/10069/79d8fafcd1fa43cd99bb4c520d7bf62f)

#### 1.3.30 简述一下虚析构函数，什么作用

**参考回答**

1.  虚析构函数，是将基类的析构函数声明为 virtual，举例如下：

    ```cpp
    class TimeKeeper
    {
    public:    
        TimeKeeper() {}        
        virtual ~TimeKeeper() {}    
    };
    ```

2.  虚析构函数的主要作用是防止内存泄露。

    定义一个基类的指针 p，在 delete p 时，如果基类的析构函数是虚函数，这时只会看 p 所赋值的对象，如果 p 赋值的对象是派生类的对象，就会调用派生类的析构函数（毫无疑问，在这之前也会先调用基类的构造函数，在调用派生类的构造函数，然后调用派生类的析构函数，基类的析构函数，所谓先构造的后释放）；如果 p 赋值的对象是基类的对象，就会调用基类的析构函数，这样就不会造成内存泄露。

    如果基类的析构函数不是虚函数，在 delete p 时，调用析构函数时，只会看指针的数据类型，而不会去看赋值的对象，这样就会造成内存泄露。

**答案解析**

*   我们创建一个 TimeKeeper 基类和一些及其它的派生类作为不同的计时方法

    ```cpp
    class TimeKeeper
    {
    public:
        TimeKeeper() {}
        ~TimeKeeper() {}  //非 virtual 的
    };

    //都继承与 TimeKeeper
    class AtomicClock :public TimeKeeper{};
    class WaterClock :public TimeKeeper {};
    class WristWatch :public TimeKeeper {};
    ```

*   如果客户想要在程序中使用时间，不想操作时间如何计算等细节，这时候我们可以设计 factory（工厂）函数，让函数返回指针指向一个计时对象。该函数返回一个基类指针，这个基类指针是指向于派生类对象的

    ```cpp
    TimeKeeper* getTimeKeeper()
    {
        //返回一个指针，指向一个 TimeKeeper 派生类的动态分配对象
    }
    ```

*   因为函数返回的对象存在于堆中，因此为了**在不使用时我们需要使用释放该对象（delete）**

    ```cpp
    TimeKeeper* ptk = getTimeKeeper();

    delete ptk;
    ```

*   此处基类的析构函数是非 virtual 的，因此**通过一个基类指针删除派生类对象是错误的**

*   **解决办法：** 将基类的析构函数改为 virtual 就正确了

    ```cpp
    class TimeKeeper
    {
    public:
        TimeKeeper() {}
        virtual ~TimeKeeper() {}
    };
    ```

*   声明为 virtual 之后，通过**基类指针删除派生类对象就会释放整个对象（基类+派生类）**

#### 1.3.31 说说什么是虚基类，可否被实例化？

**参考回答**

1.  在被继承的类前面加上 virtual 关键字，这时被继承的类称为虚基类，代码如下：

    ```cpp
    class A
    class B1:public virtual A;
    class B2:public virtual A;
    class D:public B1,public B2;
    ```

2.  虚继承的类可以被实例化，举例如下：

    ```cpp
    class Animal {/* ... */ };

    class Tiger : virtual public Animal { /* ... */ };

    class Lion : virtual public Animal { /* ... */ }
    ```

    ```cpp
    int main( )
    {
    Liger lg ;

    /*既然我们已经在 Tiger 和 Lion 类的定义中声明了"virtual"关键字，于是下面的代码编译 OK */

    int weight = lg.getWeight();
    }

    ```

#### 1.3.32 简述一下拷贝赋值和移动赋值？

**参考回答**

1.  拷贝赋值是通过拷贝构造函数来赋值，在创建对象时，使用同一类中之前创建的对象来初始化新创建的对象。

2.  移动赋值是通过移动构造函数来赋值，二者的主要区别在于

    1）拷贝构造函数的形参是一个左值引用，而移动构造函数的形参是一个右值引用；

    2）拷贝构造函数完成的是整个对象或变量的拷贝，而移动构造函数是生成一个指针指向源对象或变量的地址，接管源对象的内存，相对于大量数据的拷贝节省时间和内存空间。

#### 1.3.33 仿函数了解吗？有什么作用

**参考回答**

1.  仿函数（functor）又称为函数对象（function object）是一个能行使函数功能的类。仿函数的语法几乎和我们普通的函数调用一样，不过作为仿函数的类，都必须重载 operator()运算符，举个例子：

```cpp
 class Func{
     public:
         void operator() (const string& str) const {
             cout<<str<<endl;
         }
 };
 Func myFunc;
 myFunc("helloworld!");
>>>helloworld!
```

2.  仿函数既能想普通函数一样传入给定数量的参数，还能存储或者处理更多我们需要的有用信息。我们可以举个例子：

    假设有一个`vector<string>`，你的任务是统计长度小于 5 的 string 的个数，如果使用`count_if`函数的话，你的代码可能长成这样：

    ```cpp
      bool LengthIsLessThanFive(const string& str) {
           return str.length()<5;    
      }
      int res=count_if(vec.begin(), vec.end(), LengthIsLessThanFive);
    ```

    其中`count_if`函数的第三个参数是一个函数指针，返回一个 bool 类型的值。一般的，如果需要将特定的阈值长度也传入的话，我们可能将函数写成这样：

    ```cpp
      bool LenthIsLessThan(const string& str, int len) {
          return str.length()<len;
      }
    ```

    这个函数看起来比前面一个版本更具有一般性，但是他不能满足`count_if`函数的参数要求：`count_if`要求的是 unary function（仅带有一个参数）作为它的最后一个参数。如果我们使用仿函数，是不是就豁然开朗了呢：

    ```cpp
      class ShorterThan {
          public:
              explicit ShorterThan(int maxLength) : length(maxLength) {}
              bool operator() (const string& str) const {
                  return str.length() < length;
              }
          private:
              const int length;
      };
    ```

#### 1.3.34 C++ 中哪些函数不能被声明为虚函数？

**参考回答**

常见的不不能声明为虚函数的有：普通函数（非成员函数），静态成员函数，内联成员函数，构造函数，友元函数。

1.  为什么 C++不支持普通函数为虚函数？

    普通函数（非成员函数）只能被 overload，不能被 override，声明为虚函数也没有什么意思，因此编译器会在编译时绑定函数。

2.  为什么 C++不支持构造函数为虚函数？

    这个原因很简单，主要是从语义上考虑，所以不支持。因为构造函数本来就是为了明确初始化对象成员才产生的，然而 virtual function 主要是为了再不完全了解细节的情况下也能正确处理对象。另外，virtual 函数是在不同类型的对象产生不同的动作，现在对象还没有产生，如何使用 virtual 函数来完成你想完成的动作。（这不就是典型的悖论）

    构造函数用来创建一个新的对象,而虚函数的运行是建立在对象的基础上,在构造函数执行时,对象尚未形成,所以不能将构造函数定义为虚函数

3.  为什么 C++不支持内联成员函数为虚函数？

    其实很简单，那内联函数就是为了在代码中直接展开，减少函数调用花费的代价，虚函数是为了在继承后对象能够准确的执行自己的动作，这是不可能统一的。（再说了，*inline 函数在编译时被展开，虚函数在运行时才能动态的绑定函数*）

    内联函数是在编译时期展开,而虚函数的特性是运行时才动态联编,所以两者矛盾,不能定义内联函数为虚函数

4.  为什么 C++不支持静态成员函数为虚函数？

    这也很简单，静态成员函数对于每个类来说只有一份代码，所有的对象都共享这一份代码，他也没有要动态绑定的必要性。

    静态成员函数属于一个类而非某一对象,没有 this 指针,它无法进行对象的判别

5.  为什么 C++不支持友元函数为虚函数？

    因为 C++不支持友元函数的继承，对于没有继承特性的函数没有虚函数的说法。

#### 1.3.35 解释下 C++ 中类模板和模板类的区别

**参考回答**

1.  类模板是模板的定义，不是一个实实在在的类，定义中用到通用类型参数

2.  模板类是实实在在的类定义，是类模板的实例化。类定义中参数被实际类型所代替。

**答案解析**

1.  类模板的类型参数可以有一个或多个，每个类型前面都必须加 class，如 template <class T1,class T2>class someclass{…};在定义对象时分别代入实际的类型名，如 someclass<int,double> obj;

2.  和使用类一样，使用类模板时要注意其作用域，只能在其有效作用域内用它定义对象。

3.  模板可以有层次，一个类模板可以作为基类，派生出派生模板类。

#### 1.3.36 虚函数表里存放的内容是什么时候写进去的？

**参考回答**

1.  虚函数表是一个存储虚函数地址的数组,以 NULL 结尾。虚表（vftable）在编译阶段生成，对象内存空间开辟以后，写入对象中的 vfptr，然后调用构造函数。即：虚表在构造函数之前写入

2.  除了在构造函数之前写入之外，我们还需要考虑到虚表的二次写入机制，通过此机制让每个对象的虚表指针都能准确的指向到自己类的虚表，为实现动多态提供支持。