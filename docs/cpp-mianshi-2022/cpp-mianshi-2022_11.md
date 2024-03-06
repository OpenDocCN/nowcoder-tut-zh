# 第三章 第 4 节 C++11 新特性

> 原文：[`www.nowcoder.com/tutorial/10094/bdbeda442e8d45c0b1d0a29d3d1a225a`](https://www.nowcoder.com/tutorial/10094/bdbeda442e8d45c0b1d0a29d3d1a225a)

C++11 引入了很多新特性，有些使代码更简洁、方便，有些使代码更安全、可靠，最关键的是有些特性使 C++支持更强大的功能设计。本章对一些常用和必要学习的特性进行介绍。

# 1\. 可变参数模板

在 2-3 章函数模板和类模版介绍中，模板参数的数量必须是确定的，要想在同一个模板类或函数中实现不同数量的参数，则需要编写多个模板类和函数的副本。可变模版参数（variadic templates）是 C++11 新特性中的重磅炸弹，它对模板参数进行更高度的泛化，不仅支持参数类型不确定，而且支持参数数量不确定。

### 1.1 可变参数模板函数

无论是在声明函数模板还是类模板，首先要定义占位符。可变参数模板在 typename 或 class 后面带上省略号“...”。

```cpp
#include <iostream>
using namespace std;
template <typename/class... T>
void func(T... args)
{    
    cout << sizeof...(args) << endl; // 打印可变参数的个数
}
int main()
{
    func(); // 0
    func(1); // 1
    func(1, "abs"); // 2
    return 0;
} 
```

上面可变参数模板的定义中，T 是占位符需实例化时指明类型，省略号“...”表明该模版可接受一个参数包，参数包中可以包含 0 到任意个模板参数。参数包需要以展开的方式遍历每个参数，因此如何展开和遍历参数包是最大的难点。

### 1.2 以递归方式展开参数包(可变参数模板函数)

在可变参数模板函数中，可是以递归的方式去展开可变参数包，同时提供一个递归终止函数的副本，用于结束递归。 举个例子：

```cpp
// 实现一个打印方法，对所有入参进行递归打印
#include <iostream>
using namespace std;

//递归终止函数
template <class T> // 必须声明在可变参数模板函数之前
void printParams(T param)
{
   cout << "last parameter " << param << endl;
}
template <class T, class ...Args>  // 这里需要声明两个占位符，第一个是 T，第二个是可变参数占位符 Args
void printParams(T param, Args... rest)
{
   // printParams 调用时传的第一个形参对应与 T param
   //第二个到最后一个形参对应于 Args... rest
   cout << "parameter " << param << endl;

   // 将可变参数包传递进行递归
   // 在进入下次函数调用时，可变参数包的第一个参数对应成为 T param
   // 即每次递归可变参数包的参数数量都会少一个，直到可变参数包中只剩下一个参数时
   // 会调用我们已经定义好的递归终止函数
   printParams(rest...);
}
int main(void)
{
   printParams(1, 'c', 3.14 , "hello world");
   return 0;
}
/* 调用结果：
parameter 1
parameter c
parameter 3.14
last parameter hello world
*/ 
```

### 1.3 可变参数模板类

类似的，可变参数模板类在类定义前声明模板占位符。 例如：

```cpp
template<typename ... Types>
class Myclass
{
}; 
```

此时，可以使用 0 个或任意个参数去实例化 Myclass。

```cpp
Myclass<> obj1;
Myclass<int> obj2;
Myclass<int, string> obj3; 
```

如果想避免使用 0 个参数去实例化对象，可以在模板占位符声明中在可变参数包之前定义一个普通占位符，例如：

```cpp
template<typename T, typename ... Types>
class Myclass
{
};
Myclass<> obj1;  // 不合法的实例化
Myclass<int> obj2;
Myclass<int, string> obj3; 
```

此时需要注意，可变参数模板包必须是最后一个占位符参数。

```cpp
template<typename ... Ts, typename T>
class Myclass    // 这样定义模板是不合法的，因为无法推断出占位符 T 的变量类型
{}; 
```

### 1.4 递归继承方式展开参数包——tuple 容器

tuple 又叫元祖，是一个固定大小的不同类型变量值的集合。tuple 是 C++11 引入的 STL 标准库容器，std::tuple 理论上可以有无数个任意类型的成员变量。本节剖析 tuple 中可变参数模板类的实现，关于 tuple 容器的用法这里不进行详细介绍。 tuple 的实现代码简化如下：

```cpp
#include <iostream>
// 前向声明
template<typename... Values> class tuple;
// 终止类前向声明
template<> class tuple<> {};

// 参数模板定义了 Head 和 Tail 占位符，其中 Tail 是个参数包
template<typename Head, typename... Tail>
// 继承于除 Head 之外的 Tail 参数包基 tuple 类
class tuple<Head, Tail...> : private tuple<Tail...> 
{
    typedef tuple<Tail...> inherited;  // 基类类型定义
public:
    tuple() {}
    tuple(Head v, Tail... vtail) : m_head(v), inherited(vtail...) {}
    Head& head() 
    {
        return m_head;
    }
    inherited& tail()   
    {
        // tail()函数返回基类对象，基类对象和派生类对象的内存起始地址是一样的。
        // 返回*this 再强制转化为基类 inherited 类型。
        return dynamic_cast<inherited&>(*this);
    }
protected:
    Head m_head;  // 参数包的第一个参数
} 
```

通过 tuple 类的实现可以知道，tuple 类继承于无 head 参数的基 tuple 类。同样的，基 tuple 类递归继承于无 head 参数的基 tuple 类，直到最终继承于空参数的 tuple 类。在 tuple 类中定义了 Head 类型的成员 m_head 用于访问可变参数包的第一个参数，tail()函数返回了基类的对象。因此，可以通过如下的方式打开 tuple 中的可变参数包。

```cpp
// 创建一个 tuple 实例，放入了 int, float, std::string 类型的变量，值为 0，3.14 和"hello world"
tuple<int, float, std::string> t(0, 3.14, "hello world");
// 打印前 3 个变量值
std::cout << t.head() << " " << t.tail().head() << " " << t.tail().tail().head() << std::endl; 
```

## 2.右值引用与移动语义

### 2.1 左值 & 右值

左值和右值的概念乍一看感觉很陌生，但其实它们存在于我们写过的每一份 C++代码中，C++程序中所有的值不是左值就是右值。 左值通常是指具有变量名或对象名、在表达式结束后依然存在的变量。相对的右值通常是指没有变量名、在表达式结束后就不再存在的临时值。区分左值与右值的一个关键原则是对表达式取地址，能成功取到内存地址的为左值，否则为右值。例如：

```cpp
// 以下可以对表达式取地址的是左值
int i；
char c;
string str;
// 以下不跟变量关联的字面量值：0、'!'、"hello"为右值
i = 0;
c = '!';
str = "hello";
i = 1+2; // 1+2 表达式的结果是一个临时值
// 非引用的函数返回临时变量值为右值
string func()
{
    return string("hello world");  
} 
```

### 2.2 左值引用 & 右值引用

左值引用就是常见的变量引用，也是变量的别名。由于 C++11 中引入了右值引用，因此 C++11 之前的变量引用被称为左值引用(lvalue reference)。

右值通常是在表达式结束后不再存在的值，因此想要在表达式结束后还使用右值，C++11 新特性中引入了右值引用。右值引用使用的符号是&&，例如：

```cpp
int&& x = 1;  // 1 是右值 x 是右值引用
int y = 0;  // y 是左值
int &&r = y; // 编译器错误，不能将左值赋值给右值引用

// 函数定义
string func()
{
    return string("hello world");  
}
string && rRef = func(); // func()的返回值是右值，rRef 是右值的引用 
```

*   因此，右值引用可以使右值的生命周期得以延续。如 rRef 引用了 func()的返回值，在 func 函数的 return 表达式语句结束后，返回值的生命周期终结。但通过右值引用，返回值作为右值又重获新生，其生命期将与右值引用类型变量 rRef 的生命周期一样。实际上，rRef 是一个左值，可以对其取地址。

> 注意：左值引用和右值引用只能引用相符的类型，如果绑定类型不正确会编译失败。但有一个特例，const 修饰的左值引用既可以引用左值，又可以引用右值。但缺点是，const 修饰引用为只读类型，无法修改。 例如：

```cpp
void func(const string& ref)
{
    return ;
}
string s1 = "hello";
func(s1); // 传入左值作为实参
func("hello"); // 传入右值作为实参 
```

*   右值引用的另一个优势是减少内存的拷贝。例如：

```cpp
void func_val(string value)  // 形参为 string 的左值
{
}
void func_ref(string&& ref)  // 形参为 string 的右值引用
{
}
func_val("hello");  // 调用 func_val 时会产生一次拷贝构造
func_ref("hello");  // 没有拷贝构造发生 
```

注意：验证上例需要在编译时加上-fno-elide-constructors，关闭编译器的返回值优化，否则编译器默认开启了返回值优化，编译器会使用一个对象代替临时变量的构造和函数形参的传递，因此一次拷贝构造都不会发生。

总结：

*   左值引用 T&, 只能引用左值
*   右值引用 T&&，只能引用右值
*   常量左值引用 const T&, 既可以引用左值又可以引用右值
*   已命名的右值引用，编译器会认为是个左值
*   编译器有返回值优化，但不应该过于依赖

### 2.3 移动构造函数

通过学习拷贝构造函数，我们认识到拷贝构造函数是一种特殊的构造函数，它的入参是这个类的对象或对象引用，拷贝构造函数的工作就是将实参的对象赋值为自己成员，尤其是指针成员动态申请了内存时，拷贝构造函数要实现深拷贝。再回顾下拷贝构造函数介绍时的案例：

```cpp
#include <iostream>
#include <vector>
using namespace std;
class student
{
public:
    // 构造函数
    student()
    {
        score_list = new int[10]; // 指针动态申请 10 个 int 变量所需的内存
    }
    ~student()
    {
        delete score_list;
    }
    // 拷贝构造函数
    student(const student& stu)
    {
        static_copy++;
        score_list = new int[10];    // 指针成员动态申请 10 个 int 变量所需的内存
        // 深拷贝
        for (int i = 0; i < 10; i++)
        {
            score_list[i] = stu.score_list[i]; // 循环复制 score_list 中的值
        }
    }
public:
    static int static_copy;  // 静态变量，用于统计 student 类调用拷贝构造函数的次数
    int * score_list;
};
int student::static_copy = 0; 
```

在上面这个类中，我们定义了 int*指针成员并动态申请了内存，在拷贝构造函数中实现了指针成员的深拷贝。当程序需要使用 vector 去 push_back 多个 student 对象时，vector 的 push_back 函数会将出传入的对象在 vector 自己的内存中执行一次拷贝构造，现在我们使用下面的程序统计出拷贝构造函数被调用的次数。

```cpp
int main()
{
    vector<student> vecStudent;
    vecStudent.reserve(1000);  
    // 提前分配好 1000 个对象空间，否则可能会由于 vector 的扩容导致拷贝构造函数发生次数更多，统计不准确。
    for(int i=0;i<1000;i++){
        vecStudent.push_back(student());
    }
    cout << student::static_copy << endl;  // 输出 1000
} 
```

如输出结果的注释所言，student 类的拷贝构造函数被执行了 1000 次。 在这句代码中：`vecStudent.push_back(student());`，student()构造了一个右值传入 push_back()函数，push_back 函数中以传入的右值为参数进行拷贝构造，并将生成的对象放入相应的内存地址。push_back 函数执行结束后，产生的右值对象也没什么用了，造成了无意义的资源申请和释放操作。

C++11 引入了移动构造函数，支持以浅拷贝的方式“偷”出临时对象已经申请的资源，并切断拷贝后对象与临时对象的联系，这样即使临时对象析构也不会影响到拷贝生成的对象。例如：

```cpp
#include <iostream>
#include <vector>
using namespace std;
class student
{
public:
    // 构造函数
    student()
    {
        score_list = new int[10]; // 指针动态申请 10 个 int 变量所需的内存
    }
    ~student()
    {
        delete score_list;
    }
    // 拷贝构造函数
    student(const student& stu)
    {
        static_copy++;
        score_list = new int[10];    // 指针成员动态申请 10 个 int 变量所需的内存
        // 深拷贝
        for (int i = 0; i < 10; i++)
        {
            score_list[i] = stu.score_list[i]; // 循环复制 score_list 中的值
        }
    }
    // 移动构造函数
    student(student&& stu)
    {
        static_move++;
        score_list = stu.score_list; // 指针直接赋值，浅拷贝
        stu.score_list = nullptr; // 临时对象的成员指针不再指向申请的资源
    }
public:
    static int static_copy;  // 静态变量，用于统计 student 类调用拷贝构造函数的次数
    static int static_move; // 静态变量，用于统计 student 类调用移动构造函数的次数
    int * score_list;
};
int student::static_copy = 0;
int student::static_move = 0; 
```

移动构造函数与拷贝构造函数的区别是，拷贝构造的参数是 const student& stu，是常量左值引用;而移动构造的参数是 student&& stu，是右值引用。push_back()的参数 student()是个右值，push_back 在构造对象时，优先执行 student 类的移动构造函数，移动构造函数对于成员仅进行赋值，是"偷"了过来并使传入对象与申请的资源切断了联系。因此，移动构造函数的执行效率会远高于拷贝构造函数，而且由于传入对象是右值，即使移动构造函数中切断了该对象与资源的联系，也不会影响什么。

```cpp
int main()
{
    vector<student> vecStudent;
    vecStudent.reserve(1000);  
    // 提前分配好 1000 个对象空间，否则可能会由于 vector 的扩容导致拷贝构造函数发生次数更多，统计不准确。
    for(int i=0;i<1000;i++){
        vecStudent.push_back(student());
    }
    cout << student::static_copy <<"," << student::static_move << endl;  
    // 输出 0,1000
} 
```

### 2.4 移动语义

移动构造的调用是建立在传入的构造参数是右值的情况下，如果构造参数是个左值，那么会还是会执行拷贝构造函数。但有时左值是局部变量，其生命周期也不长，当我们主观上能判断出，某个左值在作为参数进行拷贝之后没有其他作用时，能不能让左值也作为拷贝参数也执行移动构造呢？

C++11 引入移动语义`std::move()`方法，用于将左值转换为右值，移动语义帮助左值作为参数时，仍然使编译器使用移动构造函数。

例如：

```cpp
int main()
{
    vector<student> vecStudent;
    vecStudent.reserve(1000);  
    // 提前分配好 1000 个对象空间，否则可能会由于 vector 的扩容导致拷贝构造函数发生次数更多，统计不准确。
    for(int i=0;i<1000;i++){
        student stu;
        vecStudent.push_back(std::move(stu));
    }
    // 输出 0,1000
    cout << student::static_copy <<"," << student::static_move << endl;  
} 
```

使用移动语义需要注意以下几点：

*   1.类的赋值运算符重载同样可以编写拷贝赋值运算符和移动赋值运算符的版本，用于实现和拷贝构造和移动构造函数类似的功能。
*   2.当使用移动语义将一个左值转换为右值后，该左值变量并不会立刻析构，而是等到其生命周期结束时才会析构。因此，`student stu1; student stu2 = std::move(stu1);`若左值对象被转移成右值并作为移动构造或移动赋值的参数执行后，不应该继续使用 stu1 对象的成员，因为移动构造/移动赋值已经切断了 stu1 的指针变量与指针申请的内存资源的联系。
*   3.移动语义 std::move 的意义是转移对象资源的控制权，本质上将左值转换成右值，使得编译器选择移动构造函数/移动赋值运算符调用，避免对含有资源的对象发生多余的拷贝。对于像 int、char、float 等基本类型的变量使用移动语义时，它们没有申请其他内存、句柄等系统资源，而且也没有移动构造函数，但不会报错且仍会发生拷贝，只是没有必要多余使用 move 操作。

### 2.5 完美转发

声明了一个右值引用后，该右值引用实际上是个左值。例如： `string&& rRef = "hello";`, 对于编译器来说 rRef 是个左值，我们可以取出 rRef 的地址。因此，会产生如下的场景：

```cpp
class student; // 还是上节定义的 student 类，这里省略类的实现
void func1(student&& rRef)  // 定义 func1 函数，参数是 student 类型的右值引用
{
    cout<< "(student&& rRef)" << endl;
}
void func1(const student& ref)  // 重载 func1 函数，参数是 student 类型的常量左值引用
{
    cout<< "(const student& rRef)" << endl;
}
void func2(student&& rRef)  // 定义 func2 函数，参数是 student 类型的右值引用
{
    func1(rRef);  // 调用 void func1(const student& ref); 
}
func2(student());  // (const student& rRef) 
```

在上例中，定义了 func1 函数的两个重载版本，分别接受形参类型为 student 类型的右值引用和 student 类型的常量左值引用。接着定义 func2 函数，参数类型为 student 类型的右值引用，并在 func2 函数中调用 func1 函数，将形参传递到 func1 函数中。在 func2 函数调用处，我们传递了右值 student 对象作为实参，我们期望实参类型是右值时，会调用 func1 函数的参数为右值引用版本，因为前面讲到移动构造可以节省对象内存拷贝。

但实际运行的结果却是调用了 func1 函数的参数是 student 类型的常量左值引用的版本，与我们期待的调用有出入。其实原因很简单，在 func2 函数中，右值引用实际上是个左值，在调用 func1 函数时，我们将左值 rRef 传入 func1 函数，自然就调用到了参数是 student 类型的常量左值引用版本。

为了解决右值引用是左值导致的这个问题，C++11 引入了完美转发 std::forward，支持右值判断的推导。若原来是一个右值，那么 std::forward 返回一个右值，否则为一个左值。 例如：

```cpp
class student; // 还是上节定义的 student 类，这里省略类的实现
void func1(student&& rRef)  // 定义 func1 函数，参数是 student 类型的右值引用
{
    cout<< "(student&& rRef)" << endl;
}
void func1(const student& ref)  // 重载 func1 函数，参数是 student 类型的常量左值引用
{
    cout<< "(const student& rRef)" << endl;
}
void func2(student&& rRef)  // 定义 func2 函数，参数是 student 类型的右值引用
{
    func1(std::forward<student>(rRef));  // 调用 void func1(student&& ref);
}
func2(student());  // (const student& rRef) 
```

## 3.auto 关键字

auto 用于修饰变量的类型，编译器可以根据变量的初始值自动分析出 auto 修饰变量的类型，因此 auto 变量必须具有初始值。

```cpp
map<string, int> mapContainer;
// 定义 auto 变量 iter，初始值为 map<string, int>容器的 begin()函数返回值，也就是迭代器类型。
auto iter = mapContainer.begin();
// 定义 auto 变量 ptr，初始值为指向 string 类型的 shared_ptr 智能指针。 
auto ptr = make_shared<string>(); 
```

相比于 C++11 之前的标准，声明变量时需要指明该变量的类型，而 auto 可以使程序设计更便捷。

## 4.decltype 关键字

decltype 用于选择并返回变量或操作数的数据类型，常用于复制已有变量类型为 auto 或变量类型较复杂的场景。 例如：使用 auto 定义变量 str，赋初值为"hello world",使用 decltype 获得 str 的类型并创建 str2，输出 str2 的变量类型为 PKc,即 const char *;

```cpp
#include <iostream>
#include <typeinfo>
using namespace std;
int main()
{
    auto str = "hello world";
    decltype(str) str2 = "my name is Evila";
    cout<<typeid(str2).name() + " "<<str2<<endl; // PKc my name is Evila
    return 0;
} 
```

> auto 类型的变量必须初始化值，decltype 不要求。

## 5.nullptr 空指针

空指针是不指向任何对象的指针，当程序声明指针变量时没有指向的对象初值，那么应该将指针初始化微空指针。在 C++11 之前，通常使用 NULL 或 0 来表示空指针。实际上，NULL 会被宏替换为 0。

```cpp
#ifdef __cplusplus
#define NULL 0
#else
#define NULL ((void *)0)
#endif 
```

通过宏定义，C++编译器将 NULL 宏替换为 0，这使得函数在重载时会出现参数推导歧义。例如：

```cpp
// 定义函数 func，重载 2 个副本，一个形参类型为 void*，一个形参为 int。
void func(void *ptr);
void func(int x);
// 调用时，如果将 NULL 作为参数传入。
// 编译器根据重载函数参数类型推导，会调用到形参为 int 类型的重载函数副本。
// 这不是我们想要的调用，因为我们认为 NULL 是空指针，应该调用 void func(void *ptr);
func(NULL); 
```

为解决 NULL 代指空指针和整形 0 存在的二义性问题，nullptr 出现只被用于指代空指针。例如：

```cpp
func(nullptr);  // 调用到这个函数副本中 void func(void *ptr); 
```

## 6\. Lambda 表达式

lambda 表达式可指代可调用的代码单元，也可以理解为没有命名的内联函数。Lambda 的组成结构与函数很相似，它拥有一个返回类型，一个形参列表，一个函数体。

```cpp
[capture list] (parameter list) -> return type { function body} 
```

lambda 表达式通常定义在函数或代码块的内部。capture list 为捕获列表，如果要设定 lambad 表达式能够访问外部变量，可以在[]内写入&或者=加上变量名，其中&表示按引用访问，=表示按值访问，变量之间用逗号分。parameter list 为 lambda 表达式的形参列表。return type 为 lambda 表达式地返回值类型。

*   Lambda 表达式的优点在于可以到了需要函数实例的地方再写函数，而不是强制在外面提前写好。
*   lambda 表达式也无法在定义代码块之外调用，即不该暴露的代码隐藏到最小代码单元。
*   lambda 表达式的类型是独一无二的，用它可以特化出一个在该位置唯一的模板。

```cpp
#include <iostream>
using namespace std;
int main()
{
    int x = 0, y = 1;
    // func 为 lambad 类型，以值方式捕获 x，以引用方式捕获 y
    auto func = x,&y -> int { return a + b + x + y; };
    cout<<func(x,y)<<endl; // 输出 8
} 
```

关乎捕获列表的语法规则，可参考下表。

| 语法规则 | 语义 |
| :-- | :-- |
| [ ] | 无捕获，函数体内不能访问任何外部变量 |
| [=] | 以值（拷贝）的方式捕获所有外部变量，函数体内可以访问，但是不能修改 |
| [&] | 以引用的方式捕获所有外部变量，函数体内可以访问并修改（需要当心无效的引用） |
| [var] | 以值（拷贝）的方式捕获某个外部变量，函数体可以访问但不能修改 |
| [&var] | 以引用的方式获取某个外部变量，函数体可以访问并修改 |
| [this] | 捕获 this 指针，可以访问类的成员变量和函数 |
| [=&var] | 引用捕获变量 var，其他外部变量使用值捕获 |
| [&var] | 只捕获变量 var，其他外部变量使用引用捕获 |

## 7.智能指针(shared_ptr、unique_ptr、weak_ptr)

在 4-1 章 C++内存管理有关于智能指针的详细介绍。

## 8.long long

C++11 标准要求 long long/unsigned long long 整型可以在不同平台上有不同的长度，但至少有 64 位。可以在数字常量后使用 LL/ll、ULL/ull 后缀标识一个 long long/unsigned long long 类型的变量。比如：

```cpp
long long int num1=-900000000000000LL; // 有符号的 long long 变量 num1
unsigned long long int num2=900000000000ULL; // 无符号的 unsigned long long 变量 num2 
```

## 9\. std::pair

std::pair 支持将 2 个数据组合成一个整体，如 stl 中的 map 容器就是将 key 和 value 结合成整体。此外，pair 可以作为函数的返回值，以实现函数可以返回一个 pair，包含两个数据。

实际上，pair 的实现是一个类模板，主要的两个成员变量是 first 和 second。

```cpp
// pair 的源码
template <class T1, class T2> 
struct pair
{
  typedef T1 first_type;
  typedef T2 second_type;

  T1 first;
  T2 second;
  pair() : first(T1()), second(T2()) {}
  pair(const T1& x, const T2& y) : first(x), second(y) {}
  template <class U, class V>
    pair (const pair<U,V> &p) : first(p.first), second(p.second) { }
}

// pair 的使用
std::pair<std::string, int> pair1("key", 10);   // 给定初始值
std::pair<std::string, int> pair2(pair1);   // 拷贝构造

std::cout<< pair.first << " " << pair.second << std::endl;   // 访问 pair 中的成员 
```

make_pair()函数支持无需指明数据类型， 就可以生成一个 pair 对象。

```cpp
// make_pair 定义
template pair make_pair(T1 a, T2 b) { return pair(a, b); }

std::make_pair("key", 100);  // make_pair 函数无需指明类型，返回一个 pair 对象 
```

## 10 范围 for 循环

C++11 支持范围 for 循环，即 for range 循环。范围 for 循环的写法如下：

```cpp
for(declaration : expression) 
{
    // 循环体代码
} 
```

*   declaration：表示此处需要定义一个变量，该变量的类型为要遍历的容器存储的类型；这里也可以使用 auto 定义变量，编译器根据遍历容器的类型进行推导。
*   expression：表示要遍历的容器，可以为实现定义好的数组、字符串、stl 容器以及{}大括号初始化的序列。

```cpp
 std::vector<int> arr;
    arr.push_back(1);
    arr.push_back(2);

    for (auto n : arr)
    {
        std::cout << n << std::endl;
    } 
```