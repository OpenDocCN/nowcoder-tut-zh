# 第三章 第 3 节 模版与泛型编程

> 原文：[`www.nowcoder.com/tutorial/10094/cb2422d04e4148198d565ec7d30e92ac`](https://www.nowcoder.com/tutorial/10094/cb2422d04e4148198d565ec7d30e92ac)

# 1\. 前言

我们知道声明变量时需要指明变量的类型，类型决定了变量的属性和表现。变量的类型可以是内置的类型，例如 int、float、double 等；也可以是自定义的类型，例如结构体和类。在 2-2 章中函数重载一节我们了解了同名函数、不同数量或类型的参数构成了函数重载的条件，编译器在调用重载函数时根据参数类型推导出最适配的函数调用。

但如果我们想实现一个函数，它的参数类型有很多种可能，那么为了适配所有的参数类型，我们需要编写很多很多个重载的函数。

那么能不能不指定函数参数的类型，即参数类型以泛型的型式定义呢？

答案是：能，C++支持编写函数模板和类模板，以支持函数和类的多样性。STL 标准库中容器和方法基本都以模板的方式实现。

> 函数重载和模板均是静态多态的实现方式，此外在 2-4 章中详细介绍了可变参数模板，读者可在阅读到相应内容时进行呼应。

## 2\. 函数模板

在不使用模板的情况下，我们定义一个函数需要按如下的方式声明：ret_type function_name (parameter list),即 返回值类型 函数名(参数列表)。当定义模板时，需要先声明变量类型的占位符，语法如下：

```cpp
template <typename T>
或者
template <class T>
ret_type function_name (parameter list) 
```

T 是变量类型的占位符，在函数声明前增加模板定义，即可以在这个函数实现的任何位置使用 T 类型去定义变量，但需要注意的是 T 作为泛型参数时无法制定默认值(形参不能为空)。 举例，声明泛型 T 和模板函数，返回值为 T 类型，参数为两个 T 类型的 const 引用，函数实现为两个参数相加：

```cpp
#include<iostream>
#include<string>
using namespace std;

template <typename T>
T add(const T& a, const T& b)
{
    return a + b;
}

int main()
{
    int i = 1, j = 2;
    cout << "i + j = " << add(i, j) << endl;  // i + j = 3
    double x = 3.14, y = 5.27;
    cout << "x + y = "<< add(x, y) << endl;  // x + y = 8.41
    string s1 = "hello";
    string s2 = "world";
    cout << "s1 + s2 = "<<add(s1, s2)<<endl; // s1 + s2 = helloworld
    return 0;
} 
```

通过函数模板中占位符的类型在函数实际调用时被指定，从而实现了一个接口根据不同类型的入参实现不同的功能——接口重用。

## 3\. 类模版

与函数模板类似的，在类定义前声明泛型占位符，在类的定义中使用占位符去定义变量。当类在实例化对象时，需要指定模版中占位符的实际类型，例如： 定义一个模板类，它有两个泛型占位符，在类中定义 STL 中的 map 容器作为成员变量，并实现向 map 中插入数据和将 map 打印的方法。

```cpp
#include <map>
#include <string>
#include <iostream>
using namespace std;
template <typename T1, typename T2>
class KVMap
{
private:
    map<T1, T2> kvMap;
public:
    void setValue(const T1 key, const T2 value)  // 向 map 中插入数据
    {
        kvMap.insert(make_pair(key, value));
    }
    void printMap()     // 打印 map 的 key 和 value
    {
        for(auto iter = kvMap.begin(); iter != kvMap.end(); iter++)
        {
            cout<<iter->first<<"="<<iter->second<<"&";
        }
        cout<<endl;
    }
};

int main()
{
    KVMap<string, string> kvParams;  // 实例化对象时指明模板的类型
    kvParams.setValue("age", "25");
    kvParams.setValue("name", "Evila");
    kvParams.printMap();  // age=25&name=Evila&
    return 0;
} 
```

### 3.1 类模板继承于类模板

类模板可以作为基类被继承，若派生类也为类模板，那么可以指定基类特定的类型，也可以使用派生类的泛型来指定基类。

```cpp
// 以上节定义的类模板为基类
template <typename T1, typename T2>
class KVMap;

// 若派生类也为类模板，则可以用派生类的泛型来指定基类
template <typename T1, typename T2>
class KVMapOne : public KVMap<T1, T2> 
{
} 
```

### 3.2 普通类继承于类模板

类模板可以作为基类被继承，若派生类为普通类，那么必须指明当前基类的类型。

```cpp
// 以上节定义的类模板为基类
template <typename T1, typename T2>
class KVMap;

// 若派生类也为类模板，则可以用派生类的泛型来指定基类
class KVMapTwo : public KVMap<std::string, int> 
{
} 
```

### 3.3 类模板继承于普通类

类模板继承于普通类时，与普通的继承并无区别。类模板作为派生类，根据继承权限获得基类中的成员访问。

## 4\. 模板特化、偏特化与萃取机

### 4.1 模板全特化

通过上节可以认识到，模板在非特化情况下，占位符的类型是在实例化时指明的。但有时在实现模板类或模板函数时，需要对某个确定的类型进行特殊处理，即实现特定类型下的非通用行为。此时，我们需要对模板进行特化处理，若将所有的占位符特化为绝对类型，则视为模板的全特化。

举个例子： 1、首先定义一个模板类，这里直接引用第二节定义的 KVMap 模板类。 2、对模板中的两个占位符进行全特化实现，如特化为 string 和 double。实现非通用行为：在打印 double 的值时，保留 3 位数字。

```cpp
template <> // 不能省略，为了说明正在实现该类的特化版本
class KVMap <string, double>
{
private:
    map<string, double> kvMap;
public:
    void setValue(const string key, const double value)  // 向 map 中插入数据
    {
        kvMap.insert(make_pair(key, value));
    }
    void printMap()     // 打印 map 的 key 和 value
    {
        for(auto iter = kvMap.begin(); iter != kvMap.end(); iter++)
        {
            cout<<iter->first<<"="<<setprecision(3)<<iter->second<<"&";  // value 为 double 类型时，只输出 3 位长度
        }
        cout<<endl;
    }
};
int main()
{
    KVMap<string, double> kvParams;
    kvParams.setValue("age", 25.12345); 
    kvParams.printMap();  // age=25.1&
    return 0;
} 
```

### 4.2 模板偏特化

若只对模板声明中的部分占位符进行特化处理，或对全部模板占位符进行修饰处理，则可称为模板的偏特化。

*   偏特化：特化部分占位符

```cpp
#include <map>
#include <iostream>
#include <iomanip>
using namespace std;
template <typename T>
class KVMap <string, T>  // 该类具有两个模板占位符，一个被特化为 string，第二个未特化
{
private:
    map<string, double> kvMap;
public:
    void setValue(const string key, const T value)  // 向 map 中插入数据
    {
        kvMap.insert(make_pair(key, value));
    }
    void printMap()     // 打印 map 的 key 和 value
    {
        for(auto iter = kvMap.begin(); iter != kvMap.end(); iter++)
        {
            cout<<iter->first<<"="<<setprecision(3)<<iter->second<<"&";  // 只输出 3 位数字
        }
        cout<<endl;
    }
};
int main()
{
    KVMap<string, double> kvParams; // 实例化时确定模板类型
    kvParams.setValue("age", 25.12345);  
    kvParams.printMap();  // age=25.1&
    return 0;
} 
```

*   偏特化：特化为指针、引用、其他模板类型，这类偏特化对模板做了某些限定，但仍保留了泛型的通用性。

```cpp
template <typename T1, typename  T2>
class KVMap <T1 *,T2 *>
{
private:
    map<T1*, T2*> kvMap;
public:
    void setValue(T1* key, T2* value)  // 向 map 中插入数据
    {
        kvMap.insert(make_pair(key, value));
    }
    void printMap()     // 打印 map 的 key 和 value
    {
        for(auto iter = kvMap.begin(); iter != kvMap.end(); iter++)
        {
            // key 和 value 被偏特化为指针类型，输出变值时需要解引用
            cout<<(*iter->first)<<"="<<setprecision(3)<<(*iter->second)<<"&";
        }
        cout<<endl;
    }
};
int main()
{
    KVMap<string*, string*> kvParams; // 实例化时确定模板类型
    string key1 = "age";
    string value1 = "100";
    kvParams.setValue(&key1, &value1);
    kvParams.printMap(); // age=100&
    return 0;
}
// C++不支持模板函数的偏特化，事实上对模板函数进行重载可以视为"偏特化"。例如：
template <typename T1, typename  T2>
void func(T1 t1, T2 t2);    // 未特化的模板函数
template <typename T1>
void func(T1 t1, int t2);   // 同名函数，第二个参数为 int 类型，这其实是函数的重载 
```

### 4.3 类型萃取机制

泛型类和方法在模板的支持下，可以支持多种多样的变量类型。若我们在编写模板类或模板函数时，对某个特殊类型需要实现特殊处理，可以选择性的使用全特化和偏特化实现。但如果模板类和模板函数需要实现很多不同类型的特殊性质，那么就需要编写多个特化副本。试想一下若有一个萃取机可以帮助判断传入的类型，那么就可以在一份实现中编写多个分支，去实现不同的特性。

总结概念：萃取是在模板类中萃取类型的某些特性，帮助判断该类型是否需要实现某些特性，从而在泛型方法中来对该类型进行特殊的处理的机制。

萃取机制的实现依靠于模板特化机制和 typedef 类型定义关键字。 举个例子：在讲述类模板案例中实现了模板类 KVMap，类中依靠成员变量 std::map 容器实现了成员函数 printMap()。如果我们希望在打印 map 中存储的数据时，对于 value 为 float 和 double 类型实现只保留 3 位小数的特性，就需要在打印函数中利用类型萃取机制来获得 value 的变量类型，从而做出特性动作。

```cpp
#include <iostream>
#include <map>
#include <iomanip>
#include <string>
#include <vector>
using namespace std;

// 1\. 首先实现两个自定义类型，分别代表 '是' float/double 类型 和 '不是'float/double 类型
struct __TrueFloatOrDoubleType  // '是'float/double 类型
{
    bool get()
    {
        return true;   // 判断是 float 或 double 类型
    }
};
struct __FalseFloatOrDoubleType  // '不是'float/double 类型
{
    bool get()
    {
        return false;  // 判断不是 float 或 double 类型
    }
};

// 2\. 定义萃取机类型
template <class _Tp>
struct TypeTraits
{
    typedef __FalseFloatOrDoubleType   __IsFloatOrDoubleType;  // 对类型重命名，默认定义不是 float 或 double 类型
    __IsFloatOrDoubleType traits;
};
// 3\. 对萃取机类实现特化是类型萃取的关键
template <>
struct TypeTraits<float>  // 萃取机模板特化类型为 float
{
    typedef __TrueFloatOrDoubleType   __IsFloatOrDoubleType;  // 对类型重命名，定义为是 float/double 类型
    __IsFloatOrDoubleType traits;
};
template <>
struct TypeTraits<double>  // 萃取机模板特化类型为 double
{
    typedef __TrueFloatOrDoubleType   __IsFloatOrDoubleType;  // 对类型重命名，定义为是 float/double 类型
    __IsFloatOrDoubleType traits;
};
// 4\. KVMap 中利用萃取机制在泛型方法中实现特性
template <typename T1, typename  T2>
class KVMap
{
private:
    map<T1, T2> kvMap;
public:
    void setValue(T1 key, T2 value)  // 向 map 中插入数据
    {
        kvMap.insert(make_pair(key, value));
    }
    void printMap()     // 打印 map 的 key 和 value
    {
        for(auto iter = kvMap.begin(); iter != kvMap.end(); iter++)
        {
            // 这里用萃取机实现判断 T2 是否为 float 或 double 类型
            if (!TypeTraits<T2>().traits.get())
            {
                // 5\. 关键点：TypeTraits<T2> 若 T2 是 float 或 double，则编译器会选择 TypeTraits 的特化实现
                // 在 TypeTraits 的特化实现中，我们将 __IsFloatOrDoubleType 由 typedef 定义
                //__FalseFloatOrDoubleType 得到，因此 get()方法会返回 false
                // 在这个 if 分支中，T2 的类型一定是 float 或 double 因此打印时只输出 3 位小数
                cout<<iter->first<<"="<<setprecision(3)<<iter->second<<"&";
            }
            else
            {
                 // T2 不是 float 或 doube 类型，打印时全部输出
                cout<<iter->first<<"="<<iter->second<<"&";
            }
        }
        cout<<endl;
    }
};
int main()
{
    KVMap<string, double> kvParams; // 实例化时确定模板类型
    string key1 = "age";
    double value1 = 100.12345;  
    kvParams.setValue(key1, value1);
    kvParams.printMap();  // age=100.123&
    return 0;
} 
```

以上用一个小的案例介绍了萃取机制在模板类编写过程中的作用，事实上萃取机制在 STL 标准库中被广泛应用。STL 标准库中提供了一系列容器模板和泛型方法，而容器迭代器是容器与方法之间沟通的桥梁，泛型方法希望了解到传入容器的数据类型、数据指针和引用类型、迭代器类型、元素间隔计算类型等。因此，对容器迭代器进行类型萃取是实现 STL 中泛型方法的重要一步。

```cpp
// 在 GNU2.9 中关于迭代器萃取机 iterator_traits 的声明，iterator_traits 能够根据传入的迭代器类型萃取出以下 5 种有关容器的类型。
template <class _Iterator>
struct iterator_traits {
  typedef typename _Iterator::iterator_category iterator_category;  // 迭代器的类型
  typedef typename _Iterator::value_type        value_type;     // 数据类型
  typedef typename _Iterator::difference_type   difference_type; // 距离类型   
  typedef typename _Iterator::pointer           pointer;     // 指针类型
  typedef typename _Iterator::reference         reference;    // 引用类型
}; 
```