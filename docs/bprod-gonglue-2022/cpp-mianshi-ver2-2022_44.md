# 第五章 第 4 节 C++设计模式-4

> 原文：[`www.nowcoder.com/tutorial/10069/7a29cdbaf769490db8a7aaa10998ddd1`](https://www.nowcoder.com/tutorial/10069/7a29cdbaf769490db8a7aaa10998ddd1)

### 5.4 请说说装饰器计模式，以及它的优缺点

**参考回答**

1.  装饰器计模式的定义

    指在不改变现有对象结构的情况下，动态地给该对象增加一些职责（即增加其额外功能）的模式，它属于对象结构型模式。

2.  优点

    （1）装饰器是继承的有力补充，比继承灵活，在不改变原有对象的情况下，动态的给一个对象扩展功能，即插即用；

    （2）通过使用不用装饰类及这些装饰类的排列组合，可以实现不同效果；

    （3）装饰器模式完全遵守开闭原则。

3.  缺点

    装饰模式会增加许多子类，过度使用会增加程序得复杂性。

4.  装饰模式的结构与实现

    通常情况下，扩展一个类的功能会使用继承方式来实现。但继承具有静态特征，耦合度高，并且随着扩展功能的增多，子类会很膨胀。如果使用组合关系来创建一个包装对象（即装饰对象）来包裹真实对象，并在保持真实对象的类结构不变的前提下，为其提供额外的功能，这就是装饰模式的目标。下面来分析其基本结构和实现方法。

    装饰模式主要包含以下角色：

    （1）抽象构件（Component）角色：定义一个抽象接口以规范准备接收附加责任的对象。

    （2）具体构件（ConcreteComponent）角色：实现抽象构件，通过装饰角色为其添加一些职责。

    （3）抽象装饰（Decorator）角色：继承抽象构件，并包含具体构件的实例，可以通过其子类扩展具体构件的功能。

    （4）具体装饰（ConcreteDecorator）角色：实现抽象装饰的相关方法，并给具体构件对象添加附加的责任。

装饰模式的结构图如下图所示：

![装饰器模式](img/463e2b2c7c60db2022aee165f399a71f.png) 装饰模式结构图

装饰模式的实现代码如下：

```cpp
#include <string>
#include <iostream>

//基础组件接口定义了可以被装饰器修改的操作
class Component {
 public:
  virtual ~Component() {}
  virtual std::string Operation() const = 0;
};

//具体组件提供了操作的默认实现。这些类在程序中可能会有几个变体
class ConcreteComponent : public Component {
 public:
  std::string Operation() const override {
    return "ConcreteComponent";
  }
};

//装饰器基类和其他组件遵循相同的接口。这个类的主要目的是为所有的具体装饰器定义封装接口。
//封装的默认实现代码中可能会包含一个保存被封装组件的成员变量，并且负责对齐进行初始化
class Decorator : public Component {

 protected:
  Component* component_;

 public:
  Decorator(Component* component) : component_(component) {
  }

  //装饰器会将所有的工作分派给被封装的组件
  std::string Operation() const override {
    return this->component_->Operation();
  }
};

//具体装饰器必须在被封装对象上调用方法，不过也可以自行在结果中添加一些内容。
class ConcreteDecoratorA : public Decorator {

 //装饰器可以调用父类的是实现，来替代直接调用组件方法。
 public:
  ConcreteDecoratorA(Component* component) : Decorator(component) {
  }
  std::string Operation() const override {
    return "ConcreteDecoratorA(" + Decorator::Operation() + ")";
  }
};

//装饰器可以在调用封装的组件对象的方法前后执行自己的方法
class ConcreteDecoratorB : public Decorator {
 public:
  ConcreteDecoratorB(Component* component) : Decorator(component) {
  }

  std::string Operation() const override {
    return "ConcreteDecoratorB(" + Decorator::Operation() + ")";
  }
};

//客户端代码可以使用组件接口来操作所有的具体对象。这种方式可以使客户端和具体的实现类脱耦
void ClientCode(Component* component) {
  // ...
  std::cout << "RESULT: " << component->Operation();
  // ...
}

int main() {

  Component* simple = new ConcreteComponent;
  std::cout << "Client: I've got a simple component:\n";
  ClientCode(simple);
  std::cout << "\n\n";

  Component* decorator1 = new ConcreteDecoratorA(simple);
  Component* decorator2 = new ConcreteDecoratorB(decorator1);
  std::cout << "Client: Now I've got a decorated component:\n";
  ClientCode(decorator2);
  std::cout << "\n";

  delete simple;
  delete decorator1;
  delete decorator2;

  return 0;
}
```

**答案解析**

无