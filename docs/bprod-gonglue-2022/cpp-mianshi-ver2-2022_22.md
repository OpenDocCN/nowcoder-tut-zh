# 第一章 第 21 节 C++新特性-3

> 原文：[`www.nowcoder.com/tutorial/10069/44ddc1727d594dac8b57098f23f4fe61`](https://www.nowcoder.com/tutorial/10069/44ddc1727d594dac8b57098f23f4fe61)

#### 1.5.11 简述一下 C++11 中四种类型转换

**参考回答**

​ C++中四种类型转换分别为**const_cast、static_cast、dynamic_cast、reinterpret_cast**，四种转换功能分别如下：

1.  const_cast

​ 将 const 变量转为非 const

2.  static_cast

    ​ 最常用，可以用于各种隐式转换，比如非 const 转 const，static_cast 可以用于类向上转换，但向下转换能成功但是不安全。

3.  dynamic_cast

    ​ 只能用于含有虚函数的类转换，用于类向上和向下转换

    ​ **向上转换：**指子类向基类转换。

    ​ **向下转换：**指基类向子类转换。

    ​ 这两种转换，子类包含父类，当父类转换成子类时可能出现非法内存访问的问题。

    ​ dynamic_cast 通过判断变量运行时类型和要转换的类型是否相同来判断是否能够进行向下转换。dynamic_cast 可以做类之间上下转换，转换的时候会进行类型检查，类型相等成功转换，类型不等转换失败。运用 RTTI 技术，RTTI 是”Runtime Type Information”的缩写，意思是运行时类型信息，它提供了运行时确定对象类型的方法。在 c++层面主要体现在 dynamic_cast 和 typeid，vs 中虚函数表的-1 位置存放了指向 type_info 的指针，对于存在虚函数的类型，dynamic_cast 和 typeid 都会去查询 type_info。

​ 4\. reinterpret_cast

​ reinterpret_cast 可以做任何类型的转换，不过不对转换结果保证，容易出问题。

**注意：**为什么不用 C 的强制转换：C 的强制转换表面上看起来功能强大什么都能转，但是转换不够明确，不能进行错误检查，容易出错。

**答案解析**

​ 无

#### 1.5.12 简述一下 C++ 11 中 auto 的具体用法

**参考回答**

​ auto 用于定义变量，编译器可以自动判断变量的类型。auto 主要有以下几种用法：

1.  auto 的基本使用方法

    （1）基本使用语法如下

```cpp
auto name = value; //name 是变量的名字，value 是变量的初始值
```

​ **注意：**auto 仅仅是一个占位符，在编译器期间它会被真正的类型所替代。或者说，C++ 中的变量必须是有明确类型的，只是这个类型是由编译器自己推导出来的。

​ （2）程序实例如下

```cpp
auto n = 10; 
auto f = 12.8; 
auto p = &n; 
auto url = "www.123.com";
```

​ a. 第 1 行中，10 是一个整数，默认是 int 类型，所以推导出变量 n 的类型是 int。

​ b. 第 2 行中，12.8 是一个小数，默认是 double 类型，所以推导出变量 f 的类型是 double。

​ c. 第 3 行中，&n 的结果是一个 int* 类型的指针，所以推导出变量 f 的类型是 int*。

​ d. 第 4 行中，由双引号`""`包围起来的字符串是 const char* 类型，所以推导出变量 url 的类型是 const char*，也即一个常量指针。

2.  auto 和 const 的结合使用

    （1） auto 与 const 结合的用法

    ​ a. 当类型不为引用时，auto 的推导结果将不保留表达式的 const 属性；

    ​ b. 当类型为引用时，auto 的推导结果将保留表达式的 const 属性。

    （2）程序实例如下

```cpp
int  x = 0;
const  auto n = x;  //n 为 const int ，auto 被推导为 int
auto f = n;      //f 为 const int，auto 被推导为 int（const 属性被抛弃）
const auto &r1 = x;  //r1 为 const int& 类型，auto 被推导为 int
auto &r2 = r1;  //r1 为 const int& 类型，auto 被推导为 const int 类型
```

​ a. 第 2 行代码中，n 为 const int，auto 被推导为 int。

​ b. 第 3 行代码中，n 为 const int 类型，但是 auto 却被推导为 int 类型，这说明当`=`右边的表达式带有 const 属性时，auto 不会 使用 const 属性，而是直接推导出 non-const 类型。

​ c. 第 4 行代码中，auto 被推导为 int 类型，这个很容易理解，不再赘述。

​ d. 第 5 行代码中，r1 是 const int & 类型，auto 也被推导为 const int 类型，这说明当 const 和引用结合时，auto 的推导将保留 表达式的 const 类型。

3.  使用 auto 定义迭代器

    ​ 在使用 stl 容器的时候，需要使用迭代器来遍历容器里面的元素；不同容器的迭代器有不同的类型，在定义迭代器时必须指明。而迭代器的类型有时候比较复杂，请看下面的例子：

```cpp
#include <vector>
using namespace std;
int main(){
    vector< vector<int> > v;
    //vector< vector<int> >::iterator i = v.begin();
    auto i = v.begin();  //使用 auto 代替具体的类型,该句比上一句简洁,根据表达式 v.begin() 的类型（begin() 函数的返回值类型）来推导出变量 i 的类型
    return 0;
}
```

4.  用于泛型编程

    ​ auto 的另一个应用就是当我们不知道变量是什么类型，或者不希望指明具体类型的时候，比如泛型编程中。请看下面例子：

```cpp
#include <iostream>
using namespace std;
class A{
public:
    static int get(void){
        return 100;
    }
};
class B{
public:
    static const char* get(void){
        return "www.123.com";
    }
};
template <typename T>
void func(void){
    auto val = T::get();
    cout << val << endl;
}
int main(void){
    func<A>();
    func<B>();
    return 0;
}

/*        运行结果：
            100
            www.123.com
*/
```

​ 本例中的模板函数 func() 会调用所有类的静态函数 get()，并对它的返回值做统一处理，但是 get() 的返回值类型并不一样，而且不能自动转换。这种要求在以前的 C++ 版本中实现起来非常的麻烦，需要额外增加一个模板参数，并在调用时手动给该模板参数赋值，用以指明变量 val 的类型。但是有了 auto 类型自动推导，编译器就根据 get() 的返回值自己推导出 val 变量的类型，就不用再增加一个模板参数了。

**答案解析**

​ 无

#### 1.5.13 简述一下 C++11 中的可变参数模板新特性

**参考回答**

​ 可变参数模板(variadic template)使得编程者能够创建这样的模板函数和模板类，即可接受可变数量的参数。例如要编写一个函数，它可接受任意数量的参数，参数的类型只需是 cout 能显示的即可，并将参数显示为用逗号分隔的列表。

```cpp
int n = 14;
double x = 2.71828;
std::string mr = "Mr.String objects!";
show_list(n, x);
show_list(x*x, '!', 7, mr); //这里的目标是定义 show_list()

/*    运行结果：
          14, 2.71828
          7.38905， !, 7, Mr.String objects!
*/    
```

​ 要创建可变参数模板，需要理解几个要点：

​ （1）模板参数包（parameter pack）；

​ （2）函数参数包；

​ （3）展开（unpack）参数包；

​ （4）递归。

**答案解析**

​ 无

#### 1.5.14 简述一下 C++11 中 Lambda 新特性

**参考回答**

1.  定义

    lambda 匿名函数很简单，可以套用如下的**语法格式**：

    ​ [外部变量访问方式说明符] (参数) mutable noexcept/throw() -> 返回值类型
    ​ {
    ​ 函数体;
    ​ };

    其中各部分的含义分别为：

    a. **[外部变量方位方式说明符]**

    ```cpp
    [ ] 方括号用于向编译器表明当前是一个 lambda 表达式，其不能被省略。在方括号内部，可以注明当前 lambda 函数的函数体中可以使用哪些“外部变量”。
    ```

    ​ 所谓外部变量，指的是和当前 lambda 表达式位于同一作用域内的所有局部变量。

    b. **(参数)**

    ```cpp
    和普通函数的定义一样，lambda 匿名函数也可以接收外部传递的多个参数。和普通函数不同的是，如果不需要传递参数，可以连同 () 小括号一起省略；
    ```

    c. **mutable**

    ```cpp
    此关键字可以省略，如果使用则之前的 () 小括号将不能省略（参数个数可以为 0）。默认情况下，对于以值传递方式引入的外部变量，不允许在 lambda 表达式内部修改它们的值（可以理解为这部分变量都是 const 常量）。而如果想修改它们，就必须使用 mutable 关键字。
    ```

    ​ **注意:**对于以值传递方式引入的外部变量，lambda 表达式修改的是拷贝的那一份，并不会修改真正的外部变量；

    d. **noexcept/throw()**

    ```cpp
    可以省略，如果使用，在之前的 () 小括号将不能省略（参数个数可以为 0）。默认情况下，lambda 函数的函数体中可以抛出任何类型的异常。而标注 noexcept 关键字，则表示函数体内不会抛出任何异常；使用 throw() 可以指定 lambda 函数内部可以抛出的异常类型。
    ```

    e. **-> 返回值类型**

    ```cpp
    指明 lambda 匿名函数的返回值类型。值得一提的是，如果 lambda 函数体内只有一个 return 语句，或者该函数返回 void，则编译器可以自行推断出返回值类型，此情况下可以直接省略"-> 返回值类型"。
    ```

    f. **函数体**

    ```cpp
    和普通函数一样，lambda 匿名函数包含的内部代码都放置在函数体中。该函数体内除了可以使用指定传递进来的参数之外，还可以使用指定的外部变量以及全局范围内的所有全局变量。
    ```

2.  程序实例

```cpp
#include <iostream>
#include <algorithm>
using namespace std;
int main()
{
    int num[4] = {4, 2, 3, 1};
    //对 a 数组中的元素进行排序
    sort(num, num+4, = -> bool{ return x < y; } );
    for(int n : num){
        cout << n << " ";
    }
    return 0;
}

/*    程序运行结果：
          1 2 3 4
*/            
```