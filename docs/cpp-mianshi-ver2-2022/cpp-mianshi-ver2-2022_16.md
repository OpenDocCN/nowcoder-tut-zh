# 第一章 第 15 节 C++stl-2

> 原文：[`www.nowcoder.com/tutorial/10069/015ac5fdfbfe40c0b849dcf75fc7d4c0`](https://www.nowcoder.com/tutorial/10069/015ac5fdfbfe40c0b849dcf75fc7d4c0)

#### 1.4.6 迭代器用过吗？什么时候会失效？

**参考回答**

​ 用过，常用容器迭代器失效情形如下。

1.  对于序列容器 vector，deque 来说，使用 erase 后，后边的每个元素的迭代器都会失效，后边每个元素都往前移动一位，erase 返回下一个有效的迭代器。

2.  对于关联容器 map，set 来说，使用了 erase 后，当前元素的迭代器失效，但是其结构是红黑树，删除当前元素，不会影响下一个元素的迭代器，所以在调用 erase 之前，记录下一个元素的迭代器即可。

3.  对于 list 来说，它使用了不连续分配的内存，并且它的 erase 方法也会返回下一个有效的迭代器，因此上面两种方法都可以使用。

#### 1.4.7 说一下 STL 中迭代器的作用，有指针为何还要迭代器？

**参考回答**

1.  迭代器的作用

    （1）用于指向顺序容器和关联容器中的元素

    （2）通过迭代器可以读取它指向的元素

    （3）通过非 const 迭代器还可以修改其指向的元素

2.  迭代器和指针的区别

    ​ **迭代器不是指针，是类模板，表现的像指针。**他只是模拟了指针的一些功能，重载了指针的一些操作符，-->、++、--等。迭代器封装了指针，是一个”可遍历 STL（ Standard Template Library）容器内全部或部分元素”的对象，**本质**是封装了原生指针，是指针概念的一种提升，提供了比指针更高级的行为，相当于一种智能指针，他可以根据不同类型的数据结构来实现不同的++，--等操作。

    ​ **迭代器返回的是对象引用而不是对象的值**，所以 cout 只能输出迭代器使用取值后的值而不能直接输出其自身。

3.  迭代器产生的原因

    ​ Iterator 类的访问方式就是把不同集合类的访问逻辑抽象出来，使得不用暴露集合内部的结构而达到循环遍历集合的效果。

**答案解析**

1.  迭代器

    ​ Iterator（迭代器）模式又称游标（Cursor）模式，用于提供一种方法**顺序访问一个聚合对象中各个元素, 而又不需暴露该对象的内部表示**。 或者这样说可能更容易理解：Iterator 模式是运用于聚合对象的一种模式，通过运用该模式，使得我们**可以在不知道对象内部表示的情况下，按照一定顺序（由 iterator 提供的方法）访问聚合对象中的各个元素**。 由于 Iterator 模式的以上特性：与聚合对象耦合，在一定程度上限制了它的广泛运用，一般仅用于底层聚合支持类，如 STL 的 list、vector、stack 等容器类及 ostream_iterator 等扩展 Iterator。

​ 2\. 迭代器示例：

```cpp
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<int> v; //一个存放 int 元素的数组，一开始里面没有元素
    v.push_back(1);
    v.push_back(2);
    v.push_back(3);
    v.push_back(4);
    vector<int>::const_iterator i; //常量迭代器
    for (i = v.begin(); i != v.end(); ++i) //v.begin()表示 v 第一个元素迭代器指针，++i 指向下一个元素
        cout << *i << ","; //*i 表示迭代器指向的元素
    cout << endl;

    vector<int>::reverse_iterator r; //反向迭代器
    for (r = v.rbegin(); r != v.rend(); r++)
        cout << *r << ",";
    cout << endl;
    vector<int>::iterator j; //非常量迭代器
    for (j = v.begin();j != v.end();j++)
        *j = 100;
    for (i = v.begin();i != v.end();i++)
        cout << *i << ",";
    return 0;
}

/*    运行结果：
          1,2,3,4,
          4,3,2,1,
          100,100,100,100,
*/                
```

#### 1.4.8 说说 STL 迭代器是怎么删除元素的

**参考回答**

​ 这是主要考察迭代器失效的问题。

1.  对于序列容器 vector，deque 来说，使用 erase 后，后边的每个元素的迭代器都会失效，后边每个元素都往前移动一位，erase 返回下一个有效的迭代器；

2.  对于关联容器 map，set 来说，使用了 erase 后，当前元素的迭代器失效，但是其结构是红黑树，删除当前元素，不会影响下一个元素的迭代器，所以在调用 erase 之前，记录下一个元素的迭代器即可；

3.  对于 list 来说，它使用了不连续分配的内存，并且它的 erase 方法也会返回下一个有效的迭代器，因此上面两种方法都可以使用。

**答案解析**

​ 容器上迭代器分类如下表（详细实现过程请翻阅相关资料详细了解）：

| 容器 | 容器上的迭代器类别 |
| :-: | :-: |
| vector | 随机访问 |
| deque | 随机访问 |
| list | 双向 |
| set/multiset | 双向 |
| map/multimap | 双向 |
| stack | 不支持迭代器 |
| queue | 不支持迭代器 |
| priority_queue | 不支持迭代器 |

#### 1.4.9 说说 STL 中 resize 和 reserve 的区别

**参考回答**

1.  首先必须弄清楚两个概念：

    （1）capacity：该值在容器初始化时赋值，指的是容器能够容纳的最大的元素的个数。还不能通过下标等访问，因为此时容器中还没有创建任何对象。

    （2）size：指的是此时容器中实际的元素个数。可以通过下标访问 0-(size-1)范围内的对象。

2.  resize 和 reserve 区别主要有以下几点：

    （1）resize 既分配了空间，也创建了对象；reserve 表示容器预留空间，但并不是真正的创建对象，需要通过 insert（）或 push_back（）等创建对象。

    （2）resize 既修改 capacity 大小，也修改 size 大小；reserve 只修改 capacity 大小，不修改 size 大小。

    （3）两者的形参个数不一样。 resize 带两个参数，一个表示容器大小，一个表示初始值（默认为 0）；reserve 只带一个参数，表示容器预留的大小。

**答案解析**

​ **问题延伸：**

​ resize 和 reserve 既有差别，也有共同点。两个接口的**共同点**是**它们都保证了 vector 的空间大小(capacity)最少达到它的参数所指定的大小。**下面就他们的细节进行分析。

​ 为实现 resize 的语义，resize 接口做了两个保证：

​ （1）保证区间[0, new_size)范围内数据有效，如果下标 index 在此区间内，vector[indext]是合法的；

​ （2）保证区间[0, new_size)范围以外数据无效，如果下标 index 在区间外，vector[indext]是非法的。

​ reserve 只是保证 vector 的空间大小(capacity)最少达到它的参数所指定的大小 n。在区间[0, n)范围内，如果下标是 index，vector[index]这种访问有可能是合法的，也有可能是非法的，视具体情况而定。

​ 以下是两个接口的源代码：

```cpp
void resize(size_type new_size)

   { 
           resize(new_size, T());
   }
  void resize(size_type new_size, const T& x)
   {
        if (new_size < size()) 
              erase(begin() + new_size, end()); // erase 区间范围以外的数据，确保区间以外的数据无效
           else
              insert(end(), new_size - size(), x); // 填补区间范围内空缺的数据，确保区间内的数据有效
   }

#include<iostream>
#include<vector>
using namespace std;
int main()
{
    vector<int> a;
    cout<<"initial capacity:"<<a.capacity()<<endl;
    cout<<"initial size:"<<a.size()<<endl;

    /*resize 改变 capacity 和 size*/
    a.resize(20);
    cout<<"resize capacity:"<<a.capacity()<<endl;
    cout<<"resize size:"<<a.size()<<endl;

    vector<int> b;
     /*reserve 改变 capacity,不改变 resize*/
    b.reserve(100);
    cout<<"reserve capacity:"<<b.capacity()<<endl;
    cout<<"reserve size:"<<b.size()<<endl;
return 0;
}

/*    运行结果：
          initial capacity:0
        initial size:0
        resize capacity:20
        resize size:20
        reserve capacity:100
        reserve size:0
*/    
```

​ **注意：**如果 n 大于当前的 vector 的容量(是容量，并非 vector 的 size)，将会引起自动内存分配。所以现有的 pointer,references,iterators 将会失效。而内存的重新配置会很耗时间。

#### 1.4.10 说说 STL 容器动态链接可能产生的问题？

**参考回答**

1.  可能产生 的问题

    ​ 容器是一种动态分配内存空间的一个变量集合类型变量。在一般的程序函数里，局部容器，参数传递容器，参数传递容器的引用，参数传递容器指针都是可以正常运行的，而在动态链接库函数内部使用容器也是没有问题的，但是给动态库函数传递容器的对象本身，则会出现内存堆栈破坏的问题。

2.  产生问题的原因
    容器和动态链接库相互支持不够好，动态链接库函数中使用容器时，参数中只能传递容器的引用，并且要保证容器的大小不能超出初始大小，否则导致容器自动重新分配，就会出现内存堆栈破坏问题。

#### 1.4.11 说说 map 和 unordered_map 的区别？底层实现

**参考回答**

​ map 和 unordered_map 的区别在于他们的**实现基理不同**。

1.  map 实现机理

    ​ map 内部实现了一个**红黑树**（红黑树是非严格平衡的二叉搜索树，而 AVL 是严格平衡二叉搜索树），红黑树有自动排序的功能，因此 map 内部所有元素都是有序的，红黑树的每一个节点都代表着 map 的一个元素。因此，对于 map 进行的查找、删除、添加等一系列的操作都相当于是对红黑树进行的操作。map 中的元素是按照二叉树（又名二叉查找树、二叉排序树）存储的，特点就是左子树上所有节点的键值都小于根节点的键值，右子树所有节点的键值都大于根节点的键值。使用中序遍历可将键值按照从小到大遍历出来。

2.  unordered_map 实现机理

    unordered_map 内部实现了一个**哈希表**（也叫散列表），通过把关键码值映射到 Hash 表中一个位置来访问记录，查找时间复杂度可达 O（1），其中在海量数据处理中有着广泛应用。因此，元素的排列顺序是无序的。