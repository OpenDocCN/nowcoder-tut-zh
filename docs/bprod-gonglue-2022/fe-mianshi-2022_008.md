# 第三章 第 1 节 前端设计模式

> 原文：[`www.nowcoder.com/tutorial/10072/b184244429094bbcb695fab1eabcd44c`](https://www.nowcoder.com/tutorial/10072/b184244429094bbcb695fab1eabcd44c)

## 3\. 设计模式

#### 3.1 观察者模式

**参考答案**：

**观察者模式（Observer）**

通常又被称为 **发布-订阅者模式** 或 **消息机制**，它定义了对象间的一种一对多的依赖关系，只要当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被自动更新，解决了主体对象与观察者之间功能的耦合，即一个对象状态改变给其他对象通知的问题

**观察者模式 JS 实现**

```cpp
let observer_ids=0;
let observed_ids=0;
//观察者类
class Observer {
   constructor() {
      this.id = observer_ids++;
   }
   //观测到变化后的处理
   update(ob){
      console.log("观察者" + this.id + `-检测到被观察者${ob.id}变化`);
   }
}
//被观察者列
class Observed {
   constructor() {
      this.observers = [];
      this.id=observed_ids++;
   }
   //添加观察者
   addObserver(observer) {
      this.observers.push(observer);
   }
   //删除观察者
   removeObserver(observer) {
      this.observers = this.observers.filter(o => {
         return o.id != observer.id;
      });
   }
   //通知所有的观察者
   notify(ob) {
      this.observers.forEach(observer => {
         observer.update(ob);
      });
   }
}

let mObserved=new Observed();
let mObserver1=new Observer();
let mObserver2=new Observer();

mObserved.addObserver(mObserver1);
mObserved.addObserver(mObserver2);

mObserved.notify();
```

#### 3.2 设计模式有哪些

**参考答案**：

**设计模式（Design pattern）**是一套被反复使用、多数人知晓的、经过分类编目的、代码设计经验的总结。

**常用的设计模式**

*   单体模式：单体是一个用来划分命名空间并将一批相关的属性和方法组织在一起的对象，如果他可以被实例化，那么他只能被实例化一次。
*   工厂模式:提供创建对象的接口，意思就是根据领导（调用者）的指示（参数），生产相应的产品（对象）
*   单例模式：单例模式定义了一个对象的创建过程，此对象只有一个单独的实例，并提供一个访问它的全局访问点。也可以说单例就是保证一个类只有一个实例，实现的方法一般是先判断实例存在与否，如果存在直接返回，如果不存在就创建了再返回，这就确保了一个类只有一个实例对象。
*   观察者模式（发布订阅模式）： 定义对象间的一种一对多的依赖关系，以便当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并自动刷新，也被称为是发布订阅模式。
*   策略模式：策略模式指的是定义一些列的算法，把他们一个个封装起来，目的就是将算法的使用与算法的实现分离开来。说白了就是以前要很多判断的写法，现在把判断里面的内容抽离开来，变成一个个小的个体
*   模板模式：定义了一个操作中的算法的骨架，而将一些步骤延迟到子类中。模板方法使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。 通俗的讲，就是将一些公共方法封装到父类，子类可以继承这个父类，并且可以在子类中重写父类的方法，从而实现自己的业务逻辑
*   代理模式：代理模式的中文含义就是帮别人做事，javascript 的解释为：把对一个对象的访问, 交给另一个代理对象来操作.
*   外观模式： 外观模式是很常见。其实它就是通过编写一个单独的函数，来简化对一个或多个更大型的，可能更为复杂的函数的访问。也就是说可以视外观模式为一种简化某些内容的手段