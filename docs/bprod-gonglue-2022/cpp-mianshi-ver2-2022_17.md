# 第一章 第 16 节 C++stl-3

> 原文：[`www.nowcoder.com/tutorial/10069/e56b77f1b0df4cfcb3466b418d5144be`](https://www.nowcoder.com/tutorial/10069/e56b77f1b0df4cfcb3466b418d5144be)

#### 1.4.12 说说 vector 和 list 的区别，分别适用于什么场景？

**参考回答**

​ vector 和 list 区别在于**底层实现机理不同**，因而特性和适用场景也有所不同。

​ **vector：一维数组**

​ 特点：元素在内存连续存放，动态数组，在堆中分配内存，元素连续存放，有保留内存，如果减少大小后内存也不会释放。

​ 优点：和数组类似开辟一段连续的空间，并且支持随机访问，所以它的查找效率高其时间复杂度 O(1)。

​ 缺点：由于开辟一段连续的空间，所以插入删除会需要对数据进行移动比较麻烦，时间复杂度 O（n），另外当空间不足时还需要进行扩容。

​ **list：双向链表**

​ 特点：元素在堆中存放，每个元素都是存放在一块内存中，它的内存空间可以是不连续的，通过指针来进行数据的访问。

​ 优点：底层实现是循环双链表，当对大量数据进行插入删除时，其时间复杂度 O(1)。

​ 缺点：底层没有连续的空间，只能通过指针来访问，所以查找数据需要遍历其时间复杂度 O（n），没有提供[]操作符的重载。

**应用场景**

​ vector 拥有一段连续的内存空间，因此支持随机访问，如果需要高效的随即访问，而不在乎插入和删除的效率，使用 vector。

​ list 拥有一段不连续的内存空间，如果需要高效的插入和删除，而不关心随机访问，则应使用 list。

#### 1.4.13 简述 vector 的实现原理

**参考回答**

​ vector 底层实现原理为**一维数组**（元素在空间连续存放）。

1.  新增元素

    ​ Vector 通过一个连续的数组存放元素，如果集合已满，在新增数据的时候，就要分配一块更大的内存，将原来的数据复制过来，释放之前的内存，在插入新增的元素。插入新的数据分在最后插入 push_back 和通过迭代器在任何位置插入，这里说一下通过迭代器插入，通过迭代器与第一个元素的距离知道要插入的位置，即 int index=iter-begin()。这个元素后面的所有元素都向后移动一个位置，在空出来的位置上存入新增的元素。

    ```cpp
    //新增元素 
    void insert(const_iterator iter,const T& t )
        {  
            int index=iter-begin();
            if (index<size_)
           {
               if (size_==capacity_)
               {
                    int capa=calculateCapacity();
                  newCapacity(capa);
               }
               memmove(buf+index+1,buf+index,(size_-index)*sizeof(T)); 
               buf[index]=t;
               size_++;
           } 
       }
    ```

2.  删除元素

    ​ 删除和新增差不多，也分两种，删除最后一个元素 pop_back 和通过迭代器删除任意一个元素 erase(iter)。通过迭代器删除还是先找到要删除元素的位置，即 int index=iter-begin();这个位置后面的每个元素都想前移动一个元素的位置。同时我们知道 erase 不释放内存只初始化成默认值。

    ​ 删除全部元素 clear：只是循环调用了 erase，所以删除全部元素的时候，不释放内存。内存是在析构函数中释放的。

    ```cpp
    //删除元素 
    iterator erase(const_iterator iter)
            {
                int index=iter-begin(); 
                if (index<size_ && size_>0)
                {
                    memmove(buf+index ,buf+index+1,(size_-index)*sizeof(T)); 
                    buf[--size_]=T();
                } 
                return iterator(iter); 
            }
    ```

3.  迭代器 iteraotr

    迭代器 iteraotr 是 STL 的一个重要组成部分,通过 iterator 可以很方便的存储集合中的元素.STL 为每个集合都写了一个迭代器, 迭代器其实是对一个指针的包装,实现一些常用的方法,如++,--,!=,==,*,->等, 通过这些方法可以找到当前元素或是别的元素. vector 是 STL 集合中比较特殊的一个,因为 vector 中的每个元素都是连续的,所以在自己实现 vector 的时候可以用指针代替。

    ```cpp
    //迭代器的实现
    template<class _Category,
        class _Ty,
        class _Diff = ptrdiff_t,
        class _Pointer = _Ty *,
        class _Reference = _Ty&>
        struct iterator
        {    // base type for all iterator classes
               typedef _Category iterator_category;
               typedef _Ty value_type;
               typedef _Diff difference_type;
               typedef _Diff distance_type;    // retained
               typedef _Pointer pointer;
               typedef _Reference reference;
        };
    ```

4.  vector 实现源码

    ```cpp
    #ifndef _CVECTOR_H_
    #define _CVECTOR_H_

    namespace cth
    {
        class NoCopy
        {
        public: 
            inline NoCopy(){}
            NoCopy(const NoCopy&);
            NoCopy& operator=(const NoCopy&); 
        };

        template<typename T>
        class viterator:public std::iterator<std::forward_iterator_tag,T>
        {
        public: 
            viterator()
            {
                t=NULL;
            }
            viterator(T* t_)
            {
                t=t_;
            }
            viterator(const viterator& other)
            {
                t=other.t;
            }
            viterator& operator=(const viterator& other)
            {
                t=other.t;
                return *this;
            }

            viterator& operator++()
            {
                t++;
                return *this;
            } 
            viterator operator++(int)
            {
                viterator iter=*this;
                t++;
                return iter;
            } 
            viterator operator+(int count)
            {
                viterator iter=*this;
                iter.t+=count;
                return iter;
            } 
            viterator& operator--()
            {
                t--;
                return *this;
            } 
            viterator operator--(int)
            {
                viterator iter=*this;
                t--;
                return iter;
            } 
            viterator operator-(int count)
            {
                viterator iter=*this;
                iter.t-=count;
                return iter;
            }

            int operator-(const viterator& other)
            { 
                return t-other.t;
            } 
            int operator-(const viterator& other)const
            { 
                return t-other.t;
            }

            T& operator*()
            {
                return *t;
            } 
            const T& operator*() const
            {
                return *t;
            }

            T* operator->()
            {
                return t;
            }
            const T* operator->() const
            {
                return t;
            }

            inline bool operator!=(const viterator& other)
            {
                return t!=other.t;
            }
            inline bool operator!=(const viterator& other)const
            {
                return t!=other.t;
            }

            inline bool operator==(const viterator& other)
            {
                return t==other.t;
            }
            inline bool operator==(const viterator& other)const
            {
                return t==other.t;
            }

            inline bool operator<(const viterator& other)
            {
                return t<other.t;
            }
            inline bool operator<(const viterator& other)const
            {
                return t<other.t;
            }

            inline bool operator<=(const viterator& other)
            {
                return t<=other.t;
            }
            inline bool operator<=(const viterator& other)const
            {
                return t<=other.t;
            }

            inline bool operator>(const viterator& other)
            {
                return t>other.t;
            }
            inline bool operator>(const viterator& other)const
            {
                return t>other.t;
            }

            inline bool operator>=(const viterator& other)
            {
                return t>=other.t;
            } 
            inline bool operator>=(const viterator& other)const
            {
                return t>=other.t;
            } 
        private: 
            T* t;
        };

    ```

```cpp
   template<typename T>
   class cvector:public NoCopy
   {
   public: 
       typedef viterator<T> iterator;//viterator<T>就是对一个指针的包装，所以完全可以用 T*代替 viterator <T>
       typedef const viterator<T> const_iterator;

       //typedef T* iterator;
       //typedef const T* const_iterator;
       cvector()
       {
           initData(0);
       } 
       cvector(int capa,const T& val=T())
       { 
           initData(capa);
           newCapacity(capacity_);
           for (int i=0;i<size_;i++) 
               buf[i]=val; 
       } 
       cvector(const_iterator first,const_iterator last)
       {  
           initData(last-first);
           newCapacity(capacity_);
           iterator iter=iterator(first); 
           int index=0;
           while(iter!=last)
               buf[index++]=*iter++;
       }

       ~cvector()
       {
           if (buf)
           {
               delete[] buf;
               buf=NULL;
           }
           size_=capacity_=0;
       }
       void clear()
       {
           if (buf)
               erase(begin(),end());
       }

       void push_back(const T& t)
       {
           if (size_==capacity_)
           {
               int capa=calculateCapacity();
               newCapacity(capa);
           }
           buf[size_++]=t;
       }
       void pop_back()
       { 
           if (!empty())
               erase(end() - 1); 
       }

       int insert(const_iterator iter,const T& t )
       {  
           int index=iter-begin();
           if (index<=size_)
           {
               if (size_==capacity_)
               {
                   int capa=calculateCapacity();
                   newCapacity(capa);
               }
               memmove(buf+index+1,buf+index,(size_-index)*sizeof(T)); 
               buf[index]=t;
               size_++;
           } 
           return index;
       }
       iterator erase(const_iterator iter)
       {
           int index=iter-begin(); 
           if (index<size_ && size_>0)
           {
               memmove(buf+index ,buf+index+1,(size_-index)*sizeof(T)); 
               buf[--size_]=T();
           } 
           return iterator(iter); 
       }

       iterator erase(const_iterator first,const_iterator last)
       { 
           iterator first_=iterator(first);
           iterator last_=iterator(last); 
           while(first_<=last_--) 
               erase(first_);  
           return iterator(first_);
       }

       T& front()
       { 
           assert(size_>0);
           return buf[0];
       }
       T& back()
       { 
           assert(size_>0);
           return buf[size_-1];
       }
       T& at(int index)
       { 
           assert(size_>0);
           return buf[index];
       }
       T& operator[](int index)
       { 
           assert(size_>0 && index>=0 && index<size_);
           return buf[index];
       }

       bool empty() const
       {
           return size_==0; 
       }
       int size() const
       {
           return size_;
       }
       int capacity() const
       {
           return capacity_;
       }

       iterator begin()
       {  
           return iterator(&buf[0]); 
       } 
       iterator end()
       {  
           return iterator(&buf[size_]); 
       }

   private: 
       void newCapacity(int capa)
       { 
           capacity_=capa;
           T* newBuf=new T[capacity_];
           if (buf)
           {
               memcpy(newBuf,buf,size_*sizeof(T)); 
               delete [] buf;
           } 
           buf=newBuf;
       }

       inline int calculateCapacity()
       {
           return capacity_*3/2+1 ;
       }

       inline void initData(int capa)
       {
           buf=NULL;
           size_=capacity_=capa>0?capa:0;
       }
       int size_; 
       int capacity_ ;
       T* buf; 
   };

   struct Point
   {
       Point(int x_=0,int y_=0):x(x_),y(y_){}
       int x,y;
   };

   bool operator<(const Point& p1,const Point& p2)
   {
       if(p1.x<p2.x)
       {
           return true;
       }else if(p1.x>p2.x)
       {
           return false;
       }
       return p1.y<p2.y;
   }

   void cvectorTest()
   {
       cvector<Point> vect;
       for (int i=0;i<10;i++)
       {
           Point p(i,i); 
           vect.push_back(p);
       }

       cvector<Point>::iterator iter=vect.begin(); 
       while (iter!=vect.end())
       {
           cout<< "[" << iter->x << " " << iter->y <<"], ";
           ++iter;
       }
       iter=vect.begin()+5; 
       vect.insert(iter,Point(55,55)); 
       iter=vect.end()-3; 
       vect.insert(iter,Point(77,77));
       cout<<endl<<endl<<"插入两个元素后："<<endl;
       iter=vect.begin(); 
       while (iter!=vect.end())
       {
           cout<< "[" << iter->x << " " << iter->y <<"], ";
           ++iter;
       }
       std::sort(vect.begin(),vect.end());
       cout<<endl<<endl<<"排序后："<<endl;
       iter=vect.begin(); 
       while (iter!=vect.end())
       {
           cout<< "[" << iter->x << " " << iter->y <<"], ";
           ++iter;
       }
       vect.erase(vect.begin()+10);
       vect.erase(vect.begin()+10);
       cout<<endl<<endl<<"删除之前新增的两个元素"<<endl;
       iter=vect.begin(); 
       while (iter!=vect.end())
       {
           cout<< "[" << iter->x << " " << iter->y <<"], ";
           ++iter;
       }
       vect.clear();
       cout<<endl<<endl<<"执行 clear 之后"<<endl;
       cout<<"size="<<vect.size()<<",capacity="<<vect.capacity();

       cvector<Point> vect1;
       for (int i=10;i<20;i++)
       {
           Point p(i,i); 
           vect1.push_back(p);
       }
       cout<<endl<<endl<<"从别的 cvector 复制数据:"<<endl;

       cvector<Point> vect2(vect1.begin(),vect1.end());
       vect2.pop_back();
       vect2.pop_back();
       for(int i=0;i<vect2.size();i++)
       {
           cout<<"["<<vect2[i].x<<","<<vect2[i].y<<"], ";
       }

       cout<<endl; 
   }
```

}

```cpp
   ```c++
   //实例代码级运行结果
   struct Point
       {
           Point(int x_=0,int y_=0):x(x_),y(y_){}
           int x,y;
       };

       bool operator<(const Point& p1,const Point& p2)
       {
           if(p1.x<p2.x)
           {
               return true;
           }else if(p1.x>p2.x)
           {
               return false;
           }
           return p1.y<p2.y;
       }

       void cvectorTest()
       {
           cvector<Point> vect;
           for (int i=0;i<10;i++)
           {
               Point p(i,i); 
               vect.push_back(p);
           }

           cvector<Point>::iterator iter=vect.begin(); 
           while (iter!=vect.end())
           {
               cout<< "[" << iter->x << " " << iter->y <<"], ";
               ++iter;
           }
           iter=vect.begin()+5; 
           vect.insert(iter,Point(55,55)); 
           iter=vect.end()-3; 
           vect.insert(iter,Point(77,77));
           cout<<endl<<endl<<"插入两个元素后："<<endl;
           iter=vect.begin(); 
           while (iter!=vect.end())
           {
               cout<< "[" << iter->x << " " << iter->y <<"], ";
               ++iter;
           }
           std::sort(vect.begin(),vect.end());
           cout<<endl<<endl<<"排序后："<<endl;
           iter=vect.begin(); 
           while (iter!=vect.end())
           {
               cout<< "[" << iter->x << " " << iter->y <<"], ";
               ++iter;
           }
           vect.erase(vect.begin()+10);
           vect.erase(vect.begin()+10);
           cout<<endl<<endl<<"删除之前新增的两个元素"<<endl;
           iter=vect.begin(); 
           while (iter!=vect.end())
           {
               cout<< "[" << iter->x << " " << iter->y <<"], ";
               ++iter;
           }
           vect.clear();
           cout<<endl<<endl<<"执行 clear 之后"<<endl;
           cout<<"size="<<vect.size()<<",capacity="<<vect.capacity();

           cvector<Point> vect1;
           for (int i=10;i<20;i++)
           {
               Point p(i,i); 
               vect1.push_back(p);
           }
           cout<<endl<<endl<<"从别的 cvector 复制数据:"<<endl;

           cvector<Point> vect2(vect1.begin(),vect1.end());
           vect2.pop_back();
           vect2.pop_back();
           for(int i=0;i<vect2.size();i++)
           {
               cout<<"["<<vect2[i].x<<","<<vect2[i].y<<"], ";
           }

           cout<<endl; 
       }
```

代码运行结果如下：

![](img/31465048a1acb23507a63134efcb1fb7.png)