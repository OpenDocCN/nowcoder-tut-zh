# 第一章 第 12 节 C++面向对象-5

> 原文：[`www.nowcoder.com/tutorial/10069/6ab4de2914d849ceae757ab905095136`](https://www.nowcoder.com/tutorial/10069/6ab4de2914d849ceae757ab905095136)

#### 1.3.24 说说 C++ 中什么是菱形继承问题，如何解决

**参考回答**

1.  下面的图表可以用来解释菱形继承问题。

![](img/55ff4c7a71a84b2fb20b497e43861d35.png)

*   假设我们有类 B 和类 C，它们都继承了相同的类 A。另外我们还有类 D，类 D 通过多重继承机制继承了类 B 和类 C。因为上述图表的形状类似于菱形，因此这个问题被形象地称为菱形继承问题。现在，我们将上面的图表翻译成具体的代码：

    ```cpp
     /*
     *Animal 类对应于图表的类 A*
     */

     class Animal { /* ... */  }; // 基类
     {
     int weight;

     public:

     int getWeight() { return weight;};

     };

     class Tiger : public Animal { /* ... */ };

     class Lion : public Animal { /* ... */  }

     class Liger : public Tiger, public Lion { /* ... */ }

    ```

    在上面的代码中，我们给出了一个具体的菱形继承问题例子。Animal 类对应于最顶层类（图表中的 A），Tiger 和 Lion 分别对应于图表的 B 和 C，Liger 类（狮虎兽，即老虎和狮子的杂交种)对应于 D。

    现在，问题是如果我们有这种继承结构会出现什么样的问题。

    看看下面的代码后再来回答问题吧。

    ```cpp
      int main( )
      {
      Liger lg ;
      /*编译错误，下面的代码不会被任何 C++编译器通过 */
      int weight = lg.getWeight();  
      ｝
    ```

*   在我们的继承结构中，我们可以看出 Tiger 和 Lion 类都继承自 Animal 基类。所以问题是：因为 Liger 多重继承了 Tiger 和 Lion 类，因此 Liger 类会有两份 Animal 类的成员（数据和方法），Liger 对象"lg"会包含 Animal 基类的两个子对象。

    所以，你会问 Liger 对象有两个 Animal 基类的子对象会出现什么问题？再看看上面的代码-调用"lg.getWeight()"将会导致一个编译错误。这是因为编译器并不知道是调用 Tiger 类的 getWeight()还是调用 Lion 类的 getWeight()。所以，调用 getWeight 方法是不明确的，因此不能通过编译。

2.  我们给出了菱形继承问题的解释，但是现在我们要给出一个菱形继承问题的解决方案。如果 Lion 类和 Tiger 类在分别继承 Animal 类时都用 virtual 来标注，对于每一个 Liger 对象，C++会保证只有一个 Animal 类的子对象会被创建。看看下面的代码:

    ```cpp
    class Tiger : virtual public Animal { /* ... */ };

    class Lion : virtual public Animal { /* ... */ }
    ```

*   你可以看出唯一的变化就是我们在类 Tiger 和类 Lion 的声明中增加了"virtual"关键字。现在类 Liger 对象将会只有一个 Animal 子对象，下面的代码编译正常:

    ```cpp
    int main( )
    {
    Liger lg ;

    /*既然我们已经在 Tiger 和 Lion 类的定义中声明了"virtual"关键字，于是下面的代码编译 OK */

    int weight = lg.getWeight();
    }

    ```

#### 1.3.25 请问构造函数中的能不能调用虚方法

**参考回答**

1.  不要在构造函数中调用虚方法，从语法上讲，调用完全没有问题，但是从效果上看，往往不能达到需要的目的。

    派生类对象构造期间进入基类的构造函数时，对象类型变成了基类类型，而不是派生类类型。

    同样，进入基类析构函数时，对象也是基类类型。

    所以，虚函数始终仅仅调用基类的虚函数（如果是基类调用虚函数），不能达到多态的效果，所以放在构造函数中是没有意义的，而且往往不能达到本来想要的效果。

#### 1.3.26 请问拷贝构造函数的参数是什么传递方式，为什么

**参考回答**

1.  拷贝构造函数的参数必须使用引用传递

2.  如果拷贝构造函数中的参数不是一个引用，即形如 CClass(const CClass c_class)，那么就相当于采用了传值的方式(pass-by-value)，而传值的方式会调用该类的拷贝构造函数，从而造成无穷递归地调用拷贝构造函数。因此拷贝构造函数的参数必须是一个引用。

    需要澄清的是，传指针其实也是传值，如果上面的拷贝构造函数写成 CClass(const CClass* c_class)，也是不行的。事实上，只有传引用不是传值外，其他所有的传递方式都是传值。

#### 1.3.27 说说类方法和数据的权限有哪几种

**参考回答**

1.  C++通过 public、protected、private 三个关键字来控制成员变量和成员函数的访问权限，它们分别表示公有的、受保护的、私有的，被称为成员访问限定符。

    | 关键字 | 权限 |
    | :-- | :-- |
    | public | 可以被任意实体访问 |
    | protected | 只允许子类及本类的成员函数访问 |
    | private | 只允许本类的成员函数访问 |

2.  下面介绍一个例子。

    父类：

    ```cpp
     class Person
     {
     public:
         Person(const string& name, int age) : m_name(name), m_age(age)
         {
         }

         void ShowInfo()
         {
             cout << "姓名：" << m_name << endl;
             cout << "年龄：" << m_age << endl;
         }

     protected:
         string  m_name;     //姓名

     private:
         int     m_age;      //年龄
     };
    ```

    子类：

    ```cpp
     class Teacher : public Person
     {
     public:
         Teacher(const string& name, int age, const string& title)
             : Person(name, age), m_title(title)
         {
         }

         void ShowTeacherInfo()
         {
             ShowInfo();                             //正确，public 属性子类可见
             cout << "姓名：" << m_name << endl;        //正确，protected 属性子类可见
             cout << "年龄：" << m_age << endl;     //错误，private 属性子类不可见

             cout << "职称：" << m_title << endl;   //正确，本类中可见自己的所有成员
         }

     private:
         string  m_title;        //职称
     };
    ```

    调用方：

    ```cpp
     void test()
     {
         Person person("张三", 22);
         person.ShowInfo();                  //public 属性,对外部可见
         cout << person.m_name << endl;      //protected 属性,对外部不可见
         cout << person.m_age << endl;       //private 属性,对外部不可见
     }
    ```

#### 1.3.28 如何理解抽象类？

**参考回答**

1.  抽象类的定义如下：

    纯虚函数是在基类中声明的虚函数，它在基类中没有定义，但要求任何派生类都要定义自己的实现方法。在基类中实现纯虚函数的方法是在函数原型后加“=0”，有虚函数的类就叫做抽象类。

2.  抽象类有如下几个特点：

    1）抽象类只能用作其他类的基类，不能建立抽象类对象。

    2）抽象类不能用作参数类型、函数返回类型或显式转换的类型。

    3）可以定义指向抽象类的指针和引用，此指针可以指向它的派生类，进而实现多态性。

#### 1.3.29 什么是多态？除了虚函数，还有什么方式能实现多态？

**参考回答**

1.  多态是面向对象的重要特性之一，它是一种行为的封装，就是不同对象对同一行为会有不同的状态。(举例 : 学生和成人都去买票时,学生会打折,成人不会)

2.  多态是以封装和继承为基础的。在 C++中多态分为静态多态（早绑定）和动态多态（晚绑定）两种，其中动态多态是通过虚函数实现，静态多态通过函数重载实现，代码如下：

    ```cpp
    class A
    {
    public:    
        void do(int a);    
        void do(int a, int b);
    };
    ```