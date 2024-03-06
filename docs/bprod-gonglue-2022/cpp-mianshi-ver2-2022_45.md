# 第五章 第 5 节 C++设计模式-5

> 原文：[`www.nowcoder.com/tutorial/10069/75026689f435419f82ec85266211086e`](https://www.nowcoder.com/tutorial/10069/75026689f435419f82ec85266211086e)

### 5.5 请说说观察者设计模式，如何实现

**参考回答**

1.  观察者设计模式的定义

    指多个对象间存在一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被自动更新。这种模式有时又称作发布-订阅模式、模型-视图模式，它是对象行为型模式。

2.  优点

    （1）降低了目标与观察者之间的耦合关系，两者之间是抽象耦合关系。符合依赖倒置原则。

    （2）目标与观察者之间建立了一套触发机制。

3.  缺点

    （1）目标与观察者之间的依赖关系并没有完全解除，而且有可能出现循环引用。

    （2）当观察者对象很多时，通知的发布会花费很多时间，影响程序的效率。

4.  观察者设计模式的结构与实现

    观察者模式的主要角色如下：

    （1）抽象主题（Subject）角色：也叫抽象目标类，它提供了一个用于保存观察者对象的聚集类和增加、删除观察者对象的方法，以及通知所有观察者的抽象方法。

    （2）具体主题（Concrete Subject）角色：也叫具体目标类，它实现抽象目标中的通知方法，当具体主题的内部状态发生改变时，通知所有注册过的观察者对象。

    （3）抽象观察者（Observer）角色：它是一个抽象类或接口，它包含了一个更新自己的抽象方法，当接到具体主题的更改通知时被调用。

    （4）具体观察者（Concrete Observer）角色：实现抽象观察者中定义的抽象方法，以便在得到目标的更改通知时更新自身的状态。

可以举个博客订阅的例子，当博主发表新文章的时候，即博主状态发生了改变，那些订阅的读者就会收到通知，然后进行相应的动作，比如去看文章，或者收藏起来。博主与读者之间存在种一对多的依赖关系。下面给出相应的 UML 图设计:

![观察者模式](img/4d35ecc1ea45936141eee1b0a400ad64.png)
观察者模式的结构图

可以看到博客类中有一个观察者链表（即订阅者），当博客的状态发生变化时，通过 Notify 成员函数通知所有的观察者，告诉他们博客的状态更新了。而观察者通过 Update 成员函数获取博客的状态信息。代码实现不难，下面给出 C++的一种实现。

```cpp
//观察者
class Observer  
{
public:
    Observer() {}
    virtual ~Observer() {}
    virtual void Update() {} 
};
//博客
class Blog  
{
public:
    Blog() {}
    virtual ~Blog() {}
    void Attach(Observer *observer) { m_observers.push_back(observer); }     //添加观察者
    void Remove(Observer *observer) { m_observers.remove(observer); }        //移除观察者
    void Notify() //通知观察者
    {
        list<Observer*>::iterator iter = m_observers.begin();
        for(; iter != m_observers.end(); iter++)
            (*iter)->Update();
    }
    virtual void SetStatus(string s) { m_status = s; } //设置状态
    virtual string GetStatus() { return m_status; }    //获得状态
private:
    list<Observer* > m_observers; //观察者链表
protected:
    string m_status; //状态
};
```

以上是观察者和博客的基类，定义了通用接口。博客类主要完成观察者的添加、移除、通知操作，设置和获得状态仅仅是一个默认实现。下面给出它们相应的子类实现。

```cpp
//具体博客类
class BlogCSDN : public Blog
{
private:
    string m_name; //博主名称
public:
    BlogCSDN(string name): m_name(name) {}
    ~BlogCSDN() {}
    void SetStatus(string s) { m_status = "CSDN 通知 : " + m_name + s; } //具体设置状态信息
    string GetStatus() { return m_status; }
};
//具体观察者
class ObserverBlog : public Observer   
{
private:
    string m_name;  //观察者名称
    Blog *m_blog;   //观察的博客，当然以链表形式更好，就可以观察多个博客
public: 
    ObserverBlog(string name,Blog *blog): m_name(name), m_blog(blog) {}
    ~ObserverBlog() {}
    void Update()  //获得更新状态
    { 
        string status = m_blog->GetStatus();
        cout<<m_name<<"-------"<<status<<endl;
    }
};
```

```cpp
//测试案例
int main()
{
    Blog *blog = new BlogCSDN("wuzhekai1985");
    Observer *observer1 = new ObserverBlog("tutupig", blog);
    blog->Attach(observer1);
    blog->SetStatus("发表设计模式 C++实现（15）——观察者模式");
    blog->Notify();
    delete blog; delete observer1;
    return 0;
}
```