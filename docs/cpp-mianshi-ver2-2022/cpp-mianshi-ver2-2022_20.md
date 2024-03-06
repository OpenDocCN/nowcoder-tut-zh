# 第一章 第 19 节 C++新特性-1

> 原文：[`www.nowcoder.com/tutorial/10069/539244a24ccb45a3a9a6a7939b3f28eb`](https://www.nowcoder.com/tutorial/10069/539244a24ccb45a3a9a6a7939b3f28eb)

### 1.5 新特性

#### 1.5.1 说说 C++11 的新特性有哪些

**参考回答**

​ C++新特性主要包括包含语法改进和标准库扩充两个方面，主要包括以下 11 点：

1.  语法的改进

    （1）统一的初始化方法

    （2）成员变量默认初始化

    （3）auto 关键字 用于定义变量，编译器可以自动判断的类型（前提：定义一个变量时对其进行初始化）

    （4）decltype 求表达式的类型

    （5）智能指针 shared_ptr

    （6）空指针 nullptr（原来 NULL）

    （7）基于范围的 for 循环

    （8）右值引用和 move 语义 让程序员有意识减少进行深拷贝操作

2.  标准库扩充（往 STL 里新加进一些模板类，比较好用）

    （9）无序容器（哈希表） 用法和功能同 map 一模一样，区别在于哈希表的效率更高

    （10）正则表达式 可以认为正则表达式实质上是一个字符串，该字符串描述了一种特定模式的字符串

    （11）Lambda 表达式

**答案解析**

1.  统一的初始化方法

    ​ C++98/03 可以使用初始化列表（initializer list）进行初始化：

    ```cpp
    int i_arr[3] = { 1, 2, 3 };
    long l_arr[] = { 1, 3, 2, 4 };
    struct A
    {
        int x;
        int y;
    } a = { 1, 2 };
    ```

    ​ **但是**这种初始化方式的**适用性非常狭窄**，只有上面提到的这两种数据类型可以使用初始化列表。在 C++11 中，初始化列表的适用性被大大增加了。它现在可以用于任何类型对象的初始化，实例如下：

    ```cpp
    class Foo
    {
    public:
        Foo(int) {}
    private:
        Foo(const Foo &);
    };
    int main(void)
    {
        Foo a1(123);
        Foo a2 = 123;  //error: 'Foo::Foo(const Foo &)' is private
        Foo a3 = { 123 };
        Foo a4 { 123 };
        int a5 = { 3 };
        int a6 { 3 };
        return 0;
    }
    ```

    ​ 在上例中，a3、a4 使用了新的初始化方式来初始化对象，效果如同 a1 的直接初始化。a5、a6 则是基本数据类型的列表初始化方式。可以看到，它们的形式都是统一的。这里需要注意的是，a3 虽然使用了等于号，但它仍然是列表初始化，因此，私有的拷贝构造并不会影响到它。a4 和 a6 的写法，是 C++98/03 所不具备的。在 C++11 中，可以直接在变量名后面跟上初始化列表，来进行对象的初始化。

2.  成员变量默认初始化

    好处：构建一个类的对象不需要用构造函数初始化成员变量。

    ```cpp
    //程序实例
    #include<iostream>
    using namespace std;
    class B
    {
    public:
        int m = 1234; //成员变量有一个初始值
        int n;
    };
    int main()
    {
        B b;
        cout << b.m << endl;
        return 0;
    }
    ```

3.  auto 关键字

    ​ 用于定义变量，编译器可以自动判断的类型（前提：定义一个变量时对其进行初始化）。

    ```cpp
    //程序实例
    #include <vector>
    using namespace std;
    int main(){
        vector< vector<int> > v;
        vector< vector<int> >::iterator i = v.begin();
        return 0;
    }
    ```

    ​ 可以看出来，定义迭代器 i 的时候，类型书写比较冗长，容易出错。然而有了 auto 类型推导，我们大可不必这样，只写一个 auto 即可。

4.  decltype 求表达式的类型

    ​ decltype 是 [C++](http://c.biancheng.net/cplus/)11 新增的一个关键字，它和 auto 的功能一样，都用来在编译时期进行自动类型推导。

    (1)为什么要有 decltype

    ​ 因为 auto 并不适用于所有的自动类型推导场景，在某些特殊情况下 auto 用起来非常不方便，甚至压根无法使用，所以 decltype 关键字也被引入到 C++11 中。

    ​ auto 和 decltype 关键字都可以自动推导出变量的类型，但它们的用法是有区别的：

    ```cpp
    auto varname = value;
    decltype(exp) varname = value;
    ```

    ​ 其中，varname 表示变量名，value 表示赋给变量的值，exp 表示一个表达式。

    ​ auto 根据"="右边的初始值 value 推导出变量的类型，而 decltype 根据 exp 表达式推导出变量的类型，跟"="右边的 value 没有关系。

    ​ 另外，auto 要求变量必须初始化，而 decltype 不要求。这很容易理解，auto 是根据变量的初始值来推导出变量类型的，如果不初始化，变量的类型也就无法推导了。decltype 可以写成下面的形式：

    ```cpp
    decltype(exp) varname;
    ```

    (2)代码示例

    ```cpp
    // decltype 用法举例
    nt a = 0;
    decltype(a) b = 1;  //b 被推导成了 int
    decltype(10.8) x = 5.5;  //x 被推导成了 double
    decltype(x + 100) y;  //y 被推导成了 double
    ```

5.  智能指针 shared_ptr

    ​ 和 unique_ptr、weak_ptr 不同之处在于，多个 shared_ptr 智能指针可以共同使用同一块堆内存。并且，由于该类型智能指针在实现上采用的是引用计数机制，即便有一个 shared_ptr 指针放弃了堆内存的“使用权”（引用计数减 1），也不会影响其他指向同一堆内存的 shared_ptr 指针（只有引用计数为 0 时，堆内存才会被自动释放）。

    ```cpp
    #include <iostream>
    #include <memory>
    using namespace std;
    int main()
    {
        //构建 2 个智能指针
        std::shared_ptr<int> p1(new int(10));
        std::shared_ptr<int> p2(p1);
        //输出 p2 指向的数据
        cout << *p2 << endl;
        p1.reset();//引用计数减 1,p1 为空指针
        if (p1) {
            cout << "p1 不为空" << endl;
        }
        else {
            cout << "p1 为空" << endl;
        }
        //以上操作，并不会影响 p2
        cout << *p2 << endl;
        //判断当前和 p2 同指向的智能指针有多少个
        cout << p2.use_count() << endl;
        return 0;
    }

    /*    程序运行结果：        
                10
                p1 为空
                10
                1    
    */          
    ```

6.  空指针 nullptr（原来 NULL）

    ​ nullptr 是 nullptr_t 类型的右值常量，专用于初始化空类型指针。nullptr_t 是 C++11 新增加的数据类型，可称为“指针空值类型”。也就是说，nullpter 仅是该类型的一个实例对象（已经定义好，可以直接使用），如果需要我们完全定义出多个同 nullptr 完全一样的实例对象。值得一提的是，nullptr 可以被隐式转换成任意的指针类型。例如：

    ```cpp
    int * a1 = nullptr;
    char * a2 = nullptr;
    double * a3 = nullptr;
    ```

    ​ 显然，不同类型的指针变量都可以使用 nullptr 来初始化，编译器分别将 nullptr 隐式转换成 int*、char* 以及 double* 指针类型。另外，通过将指针初始化为 nullptr，可以很好地解决 NULL 遗留的问题，比如：

    ```cpp
    #include <iostream>
    using namespace std;
    void isnull(void *c){
        cout << "void*c" << endl;
    }
    void isnull(int n){
        cout << "int n" << endl;
    }
    int main() {
        isnull(NULL);
        isnull(nullptr);
        return 0;
    }

    /*    程序运行结果：        
            int n
            void*c
    */          
    ```

7.  基于范围的 for 循环

    ​ 如果要用 for 循环语句遍历一个数组或者容器，只能套用如下结构：

    ```cpp
    for(表达式 1; 表达式 2; 表达式 3){
        //循环体
    }
    ```

    ```cpp
    //程序实例
    #include <iostream>
    #include <vector>
    #include <string.h>
    using namespace std;
    int main() {
        char arc[] = "www.123.com";
        int i;
        //for 循环遍历普通数组
        for (i = 0; i < strlen(arc); i++) {
            cout << arc[i];
        }
        cout << endl;
        vector<char>myvector(arc,arc+3);
        vector<char>::iterator iter;
        //for 循环遍历 vector 容器
        for (iter = myvector.begin(); iter != myvector.end(); ++iter) {
            cout << *iter;
        }
        return 0;
    }
    /*    程序运行结果：        
            www.123.com
            www
    */          
    ```

8.  右值引用和 move 语义

    1.  右值引用

        ​ C++98/03 标准中就有引用，使用 "&" 表示。但此种引用方式有一个缺陷，即正常情况下只能操作 C++ 中的左值，无法对右值添加引用。举个例子：

        ```cpp
        int num = 10;
        int &b = num; //正确
        int &c = 10; //错误
        ```

        ​ 如上所示，编译器允许我们为 num 左值建立一个引用，但不可以为 10 这个右值建立引用。因此，C++98/03 标准中的引用又称为左值引用。

        注意，虽然 C++98/03 标准不支持为右值建立非常量左值引用，但允许使用常量左值引用操作右值。也就是说，常量左值引用既可以操作左值，也可以操作右值，例如：

        ```cpp
        int num = 10;
        const int &b = num;
        const int &c = 10;
        ```

        ​ 我们知道，右值往往是没有名称的，因此要使用它只能借助引用的方式。这就产生一个问题，实际开发中我们可能需要对右值进行修改（实现移动语义时就需要），显然左值引用的方式是行不通的。

        ​ 为此，C++11 标准新引入了另一种引用方式，称为右值引用，用 "&&" 表示。

        ​ 需要注意的，和声明左值引用一样，右值引用也必须立即进行初始化操作，且只能使用右值进行初始化，比如：

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

        ​ move 本意为 "移动"，但该函数并不能移动任何数据，它的功能很简单，就是将某个左值强制转化为右值。基于 move() 函数特殊的功能，其常用于实现移动语义。move() 函数的用法也很简单，其语法格式如下：

        ```cpp
        move( arg ) //其中，arg 表示指定的左值对象。该函数会返回 arg 对象的右值形式。
        ```

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

9.  无序容器（哈希表）

    用法和功能同 map 一模一样，区别在于哈希表的效率更高。

    (1) 无序容器具有以下 2 个特点：

    ​ a. 无序容器内部存储的键值对是无序的，各键值对的存储位置取决于该键值对中的键，

    ​ b. 和关联式容器相比，无序容器擅长通过指定键查找对应的值（平均时间复杂度为 O(1)）；但对于使用迭代器遍历容器中存储的元素，无序容器的执行效率则不如关联式容器。

    (2) 和关联式容器一样，无序容器只是一类容器的统称，其包含有 4 个具体容器，分别为 unordered_map、unordered_multimap、unordered_set 以及 unordered_multiset。功能如下表：

    | 无序容器 | 功能 |
    | --- | --- |
    | unordered_map | 存储键值对 <key, value> 类型的元素，其中各个键值对键的值不允许重复，且该容器中存储的键值对是无序的。 |
    | unordered_multimap | 和 unordered_map 唯一的区别在于，该容器允许存储多个键相同的键值对。 |
    | unordered_set | 不再以键值对的形式存储数据，而是直接存储数据元素本身（当然也可以理解为，该容器存储的全部都是键 key 和值 value 相等的键值对，正因为它们相等，因此只存储 value 即可）。另外，该容器存储的元素不能重复，且容器内部存储的元素也是无序的。 |
    | unordered_multiset | 和 unordered_set 唯一的区别在于，该容器允许存储值相同的元素。 |

    (3) 程序实例（以 unordered_map 容器为例）

    ```cpp
    #include <iostream>
    #include <string>
    #include <unordered_map>
    using namespace std;
    int main()
    {
        //创建并初始化一个 unordered_map 容器，其存储的 <string,string> 类型的键值对
        std::unordered_map<std::string, std::string> my_uMap{
            {"教程 1","www.123.com"},
            {"教程 2","www.234.com"},
            {"教程 3","www.345.com"} };
        //查找指定键对应的值，效率比关联式容器高
        string str = my_uMap.at("C 语言教程");
        cout << "str = " << str << endl;
        //使用迭代器遍历哈希容器，效率不如关联式容器
        for (auto iter = my_uMap.begin(); iter != my_uMap.end(); ++iter)
        {
            //pair 类型键值对分为 2 部分
            cout << iter->first << " " << iter->second << endl;
        }
        return 0;
    }

    /*    程序运行结果：
              教程 1 www.123.com
              教程 2 www.234.com
              教程 3 www.345.com
    */            
    ```

10.  正则表达式

    可以认为正则表达式实质上是一个字符串，该字符串描述了一种特定模式的字符串。常用符号的意义如下：

    | 符号 | 意义 |
    | --- | --- |
    | ^ | 匹配行的开头 |
    | $ | 匹配行的结尾 |
    | . | 匹配任意单个字符 |
    | […] | 匹配[]中的任意一个字符 |
    | (…) | 设定分组 |
    | \ | 转义字符 |
    | \d | 匹配数字[0-9] |
    | \D | \d 取反 |
    | \w | 匹配字母[a-z]，数字，下划线 |
    | \W | \w 取反 |
    | \s | 匹配空格 |
    | \S | \s 取反 |
    | + | 前面的元素重复 1 次或多次 |
    | * | 前面的元素重复任意次 |
    | ? | 前面的元素重复 0 次或 1 次 |
    | {n} | 前面的元素重复 n 次 |
    | {n,} | 前面的元素重复至少 n 次 |
    | {n,m} | 前面的元素重复至少 n 次，至多 m 次 |
    | &#124; | 逻辑或 |

11.  Lambda 匿名函数

    所谓匿名函数，简单地理解就是没有名称的函数，又常被称为 lambda 函数或者 lambda 表达式。

    （1）定义

    ​ lambda 匿名函数很简单，可以套用如下的语法格式：

    ​ [外部变量访问方式说明符] (参数) mutable noexcept/throw() -> 返回值类型
    ​ {
    ​ 函数体;
    ​ };

    其中各部分的含义分别为：

    a. [外部变量方位方式说明符]

    ```cpp
    [ ] 方括号用于向编译器表明当前是一个 lambda 表达式，其不能被省略。在方括号内部，可以注明当前 lambda 函数的函数体中可以使用哪些“外部变量”。
    ```

    ​ 所谓外部变量，指的是和当前 lambda 表达式位于同一作用域内的所有局部变量。

    b. (参数)

    ```cpp
    和普通函数的定义一样，lambda 匿名函数也可以接收外部传递的多个参数。和普通函数不同的是，如果不需要传递参数，可以连同 () 小括号一起省略；
    ```

    c. mutable

    ```cpp
    此关键字可以省略，如果使用则之前的 () 小括号将不能省略（参数个数可以为 0）。默认情况下，对于以值传递方式引入的外部变量，不允许在 lambda 表达式内部修改它们的值（可以理解为这部分变量都是 const 常量）。而如果想修改它们，就必须使用 mutable 关键字。
    ```

    ​ **注意:**对于以值传递方式引入的外部变量，lambda 表达式修改的是拷贝的那一份，并不会修改真正的外部变量；

    d. noexcept/throw()

    ```cpp
    可以省略，如果使用，在之前的 () 小括号将不能省略（参数个数可以为 0）。默认情况下，lambda 函数的函数体中可以抛出任何类型的异常。而标注 noexcept 关键字，则表示函数体内不会抛出任何异常；使用 throw() 可以指定 lambda 函数内部可以抛出的异常类型。
    ```

    e. -> 返回值类型

    ```cpp
    指明 lambda 匿名函数的返回值类型。值得一提的是，如果 lambda 函数体内只有一个 return 语句，或者该函数返回 void，则编译器可以自行推断出返回值类型，此情况下可以直接省略"-> 返回值类型"。
    ```

    f. 函数体

    ```cpp
    和普通函数一样，lambda 匿名函数包含的内部代码都放置在函数体中。该函数体内除了可以使用指定传递进来的参数之外，还可以使用指定的外部变量以及全局范围内的所有全局变量。
    ```

    （2）程序实例

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

#### 1.5.2 说说 C++ 中智能指针和指针的区别是什么？

**参考回答**

1.  智能指针

    ​ 如果在程序中使用 new 从堆（自由存储区）分配内存，等到不需要时，应使用 delete 将其释放。C++引用了智能指针 auto_ptr，以帮助自动完成这个过程。随后的编程体验（尤其是使用 STL）表明，需要有更精致的机制。基于程序员的编程体验和 BOOST 库提供的解决方案，C++11 摒弃了 auto_ptr，并新增了三种智能指针：unique_ptr、shared_ptr 和 weak_ptr。所有新增的智能指针都能与 STL 容器和移动语义协同工作。

2.  指针

    ​ C 语言规定所有变量在使用前必须先定义，指定其类型，并按此分配内存单元。指针变量不同于整型变量和其他类型的变量，它是专门用来存放地址的，所以必须将它定义为“指针类型”。

3.  智能指针和普通指针的区别

​ **智能指针和普通指针的区别**在于智能指针实际上是对普通指针加了一层封装机制，区别是它负责自动释放所指的对象，这样的一层封装机制的目的是为了使得智能指针可以方便的管理一个对象的生命期。

**答案解析**

​ 无

#### 1.5.3 说说 C++中的智能指针有哪些？分别解决的问题以及区别？

**参考回答**

1.  C++中的智能指针有 4 种，分别为：**shared_ptr、unique_ptr、weak_ptr、auto_ptr**，其中 auto_ptr 被 C++11 弃用。

2.  使用智能指针的原因

    ​ 申请的空间（即 new 出来的空间），在使用结束时，需要 delete 掉，否则会形成内存碎片。在程序运行期间，new 出来的对象，在析构函数中 delete 掉，但是这种方法不能解决所有问题，因为有时候 new 发生在某个全局函数里面，该方法会给程序员造成精神负担。**此时，智能指针就派上了用场。**使用智能指针可以很大程度上避免这个问题，因为智能指针就是一个类，当超出了类的作用域时，类会自动调用析构函数，析构函数会自动释放资源。所以，智能指针的作用原理就是在函数结束时自动释放内存空间，避免了手动释放内存空间。

3.  四种指针分别解决的问题以及各自特性如下：

    （1）auto_ptr（C++98 的方案，C++11 已经弃用）

    ​ 采用所有权模式。

    ```cpp
    auto_ptr<string> p1(new string("I reigned loney as a cloud."));
    auto_ptr<string> p2;
    p2=p1; //auto_ptr 不会报错
    ```

    ​ 此时不会报错，p2 剥夺了 p1 的所有权，但是当程序运行时访问 p1 将会报错。所以 auto_ptr 的缺点是：存在潜在的内存崩溃问题。

    （2）unique_ptr（替换 auto_ptr）

    ​ unique_ptr 实现独占式拥有或严格拥有概念，保证同一时间内只有一个智能指针可以指向该对象。它对于避免资源泄露，例如，以 new 创建对象后因为发生异常而忘记调用 delete 时的情形特别有用。

    ​ 采用所有权模式，和上面例子一样。

    ```cpp
    auto_ptr<string> p3(new string("I reigned loney as a cloud."));
    auto_ptr<string> p4;
    p4=p3; //此时不会报错
    ```

    ​ 编译器认为 P4=P3 非法，避免了 p3 不再指向有效数据的问题。因此，unique_ptr 比 auto_ptr 更安全。 另外 unique_ptr 还有更聪明的地方：当程序试图将一个 unique_ptr 赋值给另一个时，如果源 unique_ptr 是个临时右值，编译器允许这么做；如果源 unique_ptr 将存在一段时间，编译器将禁止这么做，比如：

    ```cpp
    unique_ptr<string> pu1(new string ("hello world"));
    unique_ptr<string> pu2;
    pu2 = pu1;                                      // #1 not allowed
    unique_ptr<string> pu3;
    pu3 = unique_ptr<string>(new string ("You"));   // #2 allowed
    ```

    ​ 其中#1 留下悬挂的 unique_ptr(pu1)，这可能导致危害。而#2 不会留下悬挂的 unique_ptr，因为它调用 unique_ptr 的构造函数，该构造函数创建的临时对象在其所有权让给 pu3 后就会被销毁。这种随情况而已的行为表明，unique_ptr 优于允许两种赋值的 auto_ptr 。

    ​ **注意：**如果确实想执行类似与#1 的操作，要安全的重用这种指针，可给它赋新值。C++有一个标准库函数 std::move()，让你能够将一个 unique_ptr 赋给另一个。例如：

    ```cpp
    unique_ptr<string> ps1, ps2;
    ps1 = demo("hello");
    ps2 = move(ps1);
    ps1 = demo("alexia");
    cout << *ps2 << *ps1 << endl;
    ```

    （3）shared_ptr（非常好使）

    ​ shared_ptr 实现共享式拥有概念。多个智能指针可以指向相同对象，该对象和其相关资源会在“最后一个引用被销毁”时候释放。从名字 share 就可以看出了资源可以被多个指针共享，它使用计数机制来表明资源被几个指针共享。可以通过成员函数 use_count()来查看资源的所有者个数。除了可以通过 new 来构造，还可以通过传入 auto_ptr, unique_ptr,weak_ptr 来构造。当我们调用 release()时，当前指针会释放资源所有权，计数减一。当计数等于 0 时，资源会被释放。

    ​ shared_ptr 是为了解决 auto_ptr 在对象所有权上的局限性(auto_ptr 是独占的), 在使用引用计数的机制上提供了可以共享所有权的智能指针。

    **成员函数：**

    **use_count** 返回引用计数的个数

    **unique** 返回是否是独占所有权( use_count 为 1)

    **swap** 交换两个 shared_ptr 对象(即交换所拥有的对象)

    **reset** 放弃内部对象的所有权或拥有对象的变更, 会引起原有对象的引用计数的减少

    **get** 返回内部对象(指针), 由于已经重载了()方法, 因此和直接使用对象是一样的.如 shared_ptr <int>sp(new int(1)); sp 与 sp.get()是等价的</int>

    （4）weak_ptr

    ​ weak_ptr 是一种不控制对象生命周期的智能指针, 它指向一个 shared_ptr 管理的对象。进行该对象的内存管理的是那个强引用的 shared_ptr。weak_ptr 只是提供了对管理对象的一个访问手段。weak_ptr 设计的目的是为配合 shared_ptr 而引入的一种智能指针来协助 shared_ptr 工作，它只可以从一个 shared_ptr 或另一个 weak_ptr 对象构造, 它的构造和析构不会引起引用记数的增加或减少。weak_ptr 是用来解决 shared_ptr 相互引用时的死锁问题，如果说两个 shared_ptr 相互引用，那么这两个指针的引用计数永远不可能下降为 0,资源永远不会释放。它是对对象的一种弱引用，不会增加对象的引用计数，和 shared_ptr 之间可以相互转化，shared_ptr 可以直接赋值给它，它可以通过调用 lock 函数来获得 shared_ptr。

    ```cpp
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

    ​ **注意**：我们不能通过 weak_ptr 直接访问对象的方法，比如 B 对象中有一个方法 print(),我们不能这样访问，pa->pb_->print(); 英文 pb_ 是一个 weak_ptr，应该先把它转化为 shared_ptr，如：shared_ptr p = pa->pb_.lock(); p->print();

**答案解析**

​ 无