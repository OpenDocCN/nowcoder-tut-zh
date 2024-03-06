# 第一章 第 18 节 C++stl-5

> 原文：[`www.nowcoder.com/tutorial/10069/66ad259ae1e947169fedf9c5f3e1ac97`](https://www.nowcoder.com/tutorial/10069/66ad259ae1e947169fedf9c5f3e1ac97)

#### 1.4.17 hashtable 扩容和如何解决冲突

**参考回答**

1.  哈希表的扩容

    （1）为什么要扩容

    ​ 使用链地址法封装哈希表时, 填装因子(loaderFactor)会大于 1，理论上这种封装的哈希表时可以无限插入数据的但是但是随着数据量的增多，哈希表中的每个元素会变得越来越长， 这是效率会大大降低。 因此，需要通过扩容来提高效率。

    （2）如何扩容

    ​ Hashtable 每次扩容，容量都为原来的 2 倍加 1，而 HashMap 为原来的 2 倍。此时，需要将所有数据项都进行修改(需要重新调用哈希函数，来获取新的位置)。 哈希表扩容是一个比较耗时的过程，但是一劳永逸。

    （3）什么情况下扩容

    ​ 常见的情况是在填装因子(loaderFactor) > 0.75 是进行扩容。

    （4）扩容的代码实现（详见解析答案解析代码）

2.  如何解决哈希冲突

    解决哈希冲突通常有开放地址法和链地址法两种方法，分别如下：

    ##### 开放定址法

    ​ 即当一个关键字和另一个关键字发生冲突时，使用某种探测技术在 Hash 表中形成一个探测序列，然后沿着这个探测序列依次查找下去，当碰到一个空的单元时，则插入其中。比较常用的探测方法有线性探测法，比如有一组关键字 `{12，13，25，23，38，34，6，84，91}`，Hash 表长为 14，Hash 函数为`address(key)=key%11` ，当插入 12，13，25 时可以直接插入，而当插入 23 时，地址 1 被占用了，因此沿着地址 1 依次往下探测(探测步长可以根据情况而定)，直到探测到地址 4，发现为空，则将 23 插入其中。

    ##### 链地址法

    ​ 采用数组和链表相结合的办法，将 Hash 地址相同的记录存储在一张线性表中，而每张表的表头的序号即为计算得到的 Hash 地址。

**答案解析**

1.  哈希表的使用

    ​ Hash 表采用一个映射函数`f :key -> address` 将关键字映射到该记录在表中的存储位置，从而在想要查找该记录时，可以直接根据关键字和映射关系计算出该记录在表中的存储位置，通常情况下，这种映射关系称作为 Hash 函数，而通过 Hash 函数和关键字计算出来的存储位置(注意这里的存储位置只是表中的存储位置，并不是实际的物理地址)称作为 Hash 地址。

2.  哈希函数的设计

    ​ Hash 函数设计的好坏直接影响到对 Hash 表的操作效率。通常有以下几种构造 Hash 函数的方法：

    ##### 直接定址法

    ​ 取关键字或者关键字的某个线性函数作为 Hash 地址，即`address(key) = a*key + b`。

    ##### 平方取中法

    ​ 对关键字进行平方计算，然后取结果的中间几位作为 Hash 地址，假如有以下关键字序列`{421，423，436}`，平方之后的结果为`{177241，178929，190096}`，那么可以取中间的两位数`{72，89，00}`作为 Hash 地址。

    ##### 折叠法

    ​ 将关键字拆分成几个部分，然后将这几个部分组合在一起，以特定的方式进行转化形成 Hash 地址。例如假如知道某图书的 SBN 号为：`8903-241-23`，可以将`address(key)=89+03+24+12+3`作为 Hash 地址。

    ##### 除留取余法

    ​ 如果知道 Hash 表的最大长度为 m，可以取不大于 m 的最大质数 p，然后对关键字进行取余运算，`address(key)=key % p`。

    在这里 p 的选取非常关键，p 选择的好的话，能够最大程度地减少冲突，p 一般取不大于 m 的最大质数。

3.  哈希表大小的确定

    ​ Hash 表大小的确定非常关键，如果 Hash 表的空间远远大于最后实际存储的记录个数，就会造成较大的空间浪费。如果选取小了的话，则容易造成冲突。在实际情况中，一般需要根据最终记录存储个数和关键字的分布特点来确定 Hash 表的大小。还有一种情况时可能事先不知道最终需要存储的记录个数，则需要动态维护 Hash 表的容量，此时可能需要重新计算 Hash 地址。

4.  冲突的解决

    ​ 如果产生了 Hash 冲突，就需要办法来解决，通常有如下两种方法：

    ##### 开放定址法

    ​ 即当一个关键字和另一个关键字发生冲突时，使用某种探测技术在 Hash 表中形成一个探测序列，然后沿着这个探测序列依次查找下去，当碰到一个空的单元时，则插入其中。比较常用的探测方法有线性探测法，比如有一组关键字 `{12，13，25，23，38，34，6，84，91}`，Hash 表长为 14，Hash 函数为`address(key)=key%11` ，当插入 12，13，25 时可以直接插入，而当插入 23 时，地址 1 被占用了，因此沿着地址 1 依次往下探测(探测步长可以根据情况而定)，直到探测到地址 4，发现为空，则将 23 插入其中。

    ##### 链地址法

    ​ 采用数组和链表相结合的办法，将 Hash 地址相同的记录存储在一张线性表中，而每张表的表头的序号即为计算得到的 Hash 地址。

5.  需注意的点

    （1）Hashtable 的默认容量为 11，默认负载因子为 0.75(HashMap 默认容量为 16，默认负载因子也是 0.75)；

    （2）Hashtable 的容量可以为任意整数，最小值为 1，而 HashMap 的容量始终为 2 的 n 次方；

    （3）为避免扩容带来的性能问题，建议指定合理容量；

    （4）跟 HashMap 一样，Hashtable 内部也有一个静态类叫 Entry，其实是个键值对对象，保存了键和值的引用；

    （5）HashMap 和 Hashtable 存储的是键值对对象，而不是单独的键或值。

6.  哈希表的构造函数

    ```cpp
    public Hashtable(int initialCapacity, float loadFactor) {//可指定初始容量和加载因子  
            if (initialCapacity < 0)  
                throw new IllegalArgumentException("Illegal Capacity: "+  
                                                   initialCapacity);  
            if (loadFactor <= 0 || Float.isNaN(loadFactor))  
                throw new IllegalArgumentException("Illegal Load: "+loadFactor);  
            if (initialCapacity==0)  
                initialCapacity = 1;//初始容量最小值为 1  
            this.loadFactor = loadFactor;  
            table = new Entry[initialCapacity];//创建桶数组  
            threshold = (int)Math.min(initialCapacity * loadFactor, MAX_ARRAY_SIZE + 1);//初始化容量阈值  
            useAltHashing = sun.misc.VM.isBooted() &&  
                    (initialCapacity >= Holder.ALTERNATIVE_HASHING_THRESHOLD);  
        }  
        /** 
         * Constructs a new, empty hashtable with the specified initial capacity 
         * and default load factor (0.75). 
         */  
        public Hashtable(int initialCapacity) {  
            this(initialCapacity, 0.75f);//默认负载因子为 0.75  
        }  
        public Hashtable() {  
            this(11, 0.75f);//默认容量为 11，负载因子为 0.75  
        }  
        /** 
         * Constructs a new hashtable with the same mappings as the given 
         * Map.  The hashtable is created with an initial capacity sufficient to 
         * hold the mappings in the given Map and a default load factor (0.75). 
         */  
        public Hashtable(Map<? extends K, ? extends V> t) {  
            this(Math.max(2*t.size(), 11), 0.75f);  
            putAll(t);  
        }
    ```

#### 1.4.18 说说 push_back 和 emplace_back 的区别

**参考回答**

​ 如果要将一个临时变量 push 到容器的末尾，push_back()需要先构造临时对象，再将这个对象拷贝到容器的末尾，而 emplace_back()则直接在容器的末尾构造对象，这样就省去了拷贝的过程。

**答案解析**

​ 参考代码：

```cpp
#include <iostream>
#include <cstring>
#include <vector>
using namespace std;

class A {
public:
    A(int i){
        str = to_string(i);
        cout << "构造函数" << endl; 
    }
    ~A(){}
    A(const A& other): str(other.str){
        cout << "拷贝构造" << endl;
    }

public:
    string str;
};

int main()
{
    vector<A> vec;
    vec.reserve(10);
    for(int i=0;i<10;i++){
        vec.push_back(A(i)); //调用了 10 次构造函数和 10 次拷贝构造函数,
//        vec.emplace_back(i);  //调用了 10 次构造函数一次拷贝构造函数都没有调用过
    }

```

#### 1.4.19 STL 中 vector 与 list 具体是怎么实现的？常见操作的时间复杂度是多少？

**参考回答**

1.  vector 一维数组（元素在内存连续存放）

    ​ 是动态数组，在堆中分配内存，元素连续存放，有保留内存，如果减少大小后，内存也不会释放；如果新增大小当前大小时才会重新分配内存。

    ​ 扩容方式： a. 倍放开辟三倍的内存

    ​ b. 旧的数据开辟到新的内存

    ​ c. 释放旧的内存

    ​ d. 指向新内存

2.  list 双向链表（元素存放在堆中）

    ​ 元素存放在堆中，每个元素都是放在一块内存中，它的内存空间可以是不连续的，通过指针来进行数据的访问，这个特点，使得它的随机存取变得非常没有效率，因此它没有提供[ ]操作符的重载。但是由于链表的特点，它可以很有效的支持任意地方的删除和插入操作。

    ​ 特点：a. 随机访问不方便

    ​ b. 删除插入操作方便

3.  常见时间复杂度

    （1）vector 插入、查找、删除时间复杂度分别为：O(n)、O(1)、O(n)；

    （2）list 插入、查找、删除时间复杂度分别为：O(1)、O(n)、O(1)。