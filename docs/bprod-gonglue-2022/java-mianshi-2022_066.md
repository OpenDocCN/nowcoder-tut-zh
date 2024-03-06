# 第八章 第 1 节 Java-设计模式-1

> 原文：[`www.nowcoder.com/tutorial/10070/70a943256c8840c9a37437ac657d4909`](https://www.nowcoder.com/tutorial/10070/70a943256c8840c9a37437ac657d4909)

## 1\. 设计模式

#### 1.1 说一说设计模式的六大原则

**参考答案**

单一职责原则

一个类，应当只有一个引起它变化的原因；即一个类应该只有一个职责。

就一个类而言，应该只专注于做一件事和仅有一个引起变化的原因，这就是所谓的单一职责原则。该原则提出了对对象职责的一种理想期望，对象不应该承担太多职责，正如人不应该一心分为二用。唯有专注，才能保证对象的高内聚；唯有单一，才能保证对象的细粒度。对象的高内聚与细粒度有利于对象的重用。一个庞大的对象承担了太多的职责，当客户端需要该对象的某一个职责时，就不得不将所有的职责都包含进来，从而造成冗余代码。

里氏替换原则

在面向对象的语言中，继承是必不可少的、优秀的语言机制，它主要有以下几个优点：

*   代码共享，减少创建类的工作量，每个子类都拥有父类的方法和属性；
*   提高代码的可重用性；
*   提高代码的可扩展性；
*   提高产品或项目的开放性。

相应的，继承也存在缺点，主要体现在以下几个方面：

*   继承是入侵式的。只要继承，就必须拥有父类的所有属性和方法；
*   降低代码的灵活性。子类必须拥有父类的属性和方法，使子类受到限制；
*   增强了耦合性。当父类的常量、变量和方法修改时，必须考虑子类的修改，这种修改可能造成大片的代码需要重构。

从整体上看，继承的“利”大于“弊”，然而如何让继承中“利”的因素发挥最大作用，同时减少“弊”所带来的麻烦，这就需要引入“里氏替换原则”。里氏替换原则的定义有以下两种：

*   如果对一个类型为 S 的对象 o1，都有类型为 T 的对象 o2，使得以 S 定义的所有程序 P 在所有的对象 o1 都代换成 o2 时，程序 P 的行为没有发生变化，那么类型 T 是类型 S 的子类型。
*   所有引用基类的地方必须能透明地使用其子类对象。清晰明确地说明只要父类能出现的地方子类就可以出现，而且替换为子类也不会产生任何错误或异常，使用者可能根本就不需要知道父类还是子类；但是反过来则不可以，有子类的地方，父类未必就能适应。

依赖倒置原则

依赖倒置原则包括三种含义：

*   高层模块不应该依赖低层模块，两者都依赖其抽象；
*   抽象不依赖细节；
*   细节应该依赖于抽象。

传统的过程性系统的设计办法倾向于高层次的模块依赖于低层次的模块；抽象层次依赖于具体层次。“倒置”原则将这个错误的依赖关系倒置了过来，如下图所示，由此命名为“依赖倒置原则”。

![](img/48c85da06747433c14397b9d36ecd4c5.png)

在 Java 语言中，抽象就是指接口或抽象类，两者都是不能直接被实例化的；细节就是具体的实现类，实现类实现了接口或继承了抽象类，其特点是可以直接被实例化。依赖倒置原则在 Java 语言中的表现是：

*   模块间的依赖通过抽象发生，实现类之间不发生直接的依赖关系，其依赖关系是通过接口或抽象类产生；
*   接口或抽象类不依赖于实现类；
*   实现类依赖于接口或抽象类。

依赖倒置原则更加精确的定义就是“面向接口编程”——OOD（Object-Oriented Design）的精髓之一。依赖倒置原则可以减少类间的耦合性，提高系统的稳定性，降低并行开发引起的风险，提高代码的可读性和可维护性。依赖倒置原则是 JavaBean、EJB 和 COM 等组件设计模型背后的基本原则。

接口隔离原则

接口隔离原则有如下两种定义：

1.  客户端不应该依赖它不需要的接口。
2.  类间的依赖关系应该建立在最小的接口上。

接口隔离原则的具体含义如下：

*   一个类对另外一个类的依赖性应当是建立在最小的接口上的。
*   一个接口代表一个角色，不应当将不同的角色都交给一个接口。没有关系的接口合并在一起，形成一个臃肿的大接口，这是对角色和接口的污染。因此使用多个专门的接口比使用单一的总接口要好。
*   不应该强迫客户依赖于它们不用的方法。接口属于客户，不属于它所在的类层次结构，即不要强迫客户使用它们不用的方法，否则这些客户就会面临由于这些不使用的方法的改变所带来的改变。

迪米特法则

迪米特法则又叫最少知识原则，意思是一个对象应当对其他对象尽可能少的了解。迪米特法则不同于其他的 OO 设计原则，它具有很多种表述方式，其中具有代表性的是以下几种表述：

*   只与你直接的朋友们通信；
*   不要跟“陌生人”说话；
*   每一个软件单位对其他的单位都只有最少的了解，这些了解仅局限于那些与本单位密切相关的软件单位。

按照迪米特法则，如果两个类不必彼此直接通信，那么这两个类就不应当发生直接的相互作用；如果一个类需要调用另一个类的某一个方法，可以通过第三者转发这个调用。

开闭原则

开闭原则的定义是：一个软件实体应当对扩展开放，对修改关闭。这个原则说的是，在设计一个模块的时候，应当使这个模块可以在不被修改的前提下被扩展，即应当可以在不必修改源代码的情况下改变这个模块的行为。

在面向对象的编程中，开闭原则是最基础的原则，起到总的指导作用，其他原则（单一职责、里氏替换、依赖倒置、接口隔离、迪米特法则）都是开闭原则的具体形态，即其他原则都是开闭原则的手段和工具。开闭原则的重要性可以通过以下几个方面来体现。

*   开闭原则提高复用性。在面向对象的设计中，所有的逻辑都是从原子逻辑组合而来的，而不是在一个类中独立实现一个业务逻辑，代码粒度越小，被复用的可能性就越大，避免相同的逻辑重复增加。开闭原则的设计保证系统是一个在高层次上实现了复用的系统。
*   开闭原则提高可维护性。一个软件投产后，维护人员的工作不仅仅是对数据进行维护，还可能对程序进行扩展，就是扩展一个类，而不是修改一个类。开闭原则对已有软件模块，特别是最重要的抽象层模块要求不能再修改，这就使变化中的软件系统有一定的稳定性和延续性，便于系统的维护。
*   开闭原则提高灵活性。所有的软件系统都有一个共同的性质，即对系统的需求都会随时间的推移而发生变化。在软件系统面临新的需求时，系统的设计必须是稳定的。开闭原则可以通过扩展已有的软件系统，提供新的行为，能快速应对变化，以满足对软件新的需求，使变化中的软件系统有一定的适应性和灵活性。
*   开闭原则易于测试。测试是软件开发过程中必不可少的一个环节。测试代码不仅要保证逻辑的正确性，还要保证苛刻条件（高压力、异常、错误）下不产生“有毒代码”（Poisonous Code），因此当有变化提出时，原有健壮的代码要尽量不修改，而是通过扩展来实现。否则，就需要把原有的测试过程回笼一遍，需要进行单元测试、功能测试、集成测试，甚至是验收测试。开闭原则的使用，保证软件是通过扩展来实现业务逻辑的变化，而不是修改。因此，对于新增加的类，只需新增相应的测试类，编写对应的测试方法，只要保证新增的类是正确的就可以了。

#### 1.2 说一下六大原则中的开闭原则

**参考答案**

开闭原则的定义是：一个软件实体应当对扩展开放，对修改关闭。这个原则说的是，在设计一个模块的时候，应当使这个模块可以在不被修改的前提下被扩展，即应当可以在不必修改源代码的情况下改变这个模块的行为。

在面向对象的编程中，开闭原则是最基础的原则，起到总的指导作用，其他原则（单一职责、里氏替换、依赖倒置、接口隔离、迪米特法则）都是开闭原则的具体形态，即其他原则都是开闭原则的手段和工具。开闭原则的重要性可以通过以下几个方面来体现。

*   开闭原则提高复用性。在面向对象的设计中，所有的逻辑都是从原子逻辑组合而来的，而不是在一个类中独立实现一个业务逻辑，代码粒度越小，被复用的可能性就越大，避免相同的逻辑重复增加。开闭原则的设计保证系统是一个在高层次上实现了复用的系统。
*   开闭原则提高可维护性。一个软件投产后，维护人员的工作不仅仅是对数据进行维护，还可能对程序进行扩展，就是扩展一个类，而不是修改一个类。开闭原则对已有软件模块，特别是最重要的抽象层模块要求不能再修改，这就使变化中的软件系统有一定的稳定性和延续性，便于系统的维护。
*   开闭原则提高灵活性。所有的软件系统都有一个共同的性质，即对系统的需求都会随时间的推移而发生变化。在软件系统面临新的需求时，系统的设计必须是稳定的。开闭原则可以通过扩展已有的软件系统，提供新的行为，能快速应对变化，以满足对软件新的需求，使变化中的软件系统有一定的适应性和灵活性。
*   开闭原则易于测试。测试是软件开发过程中必不可少的一个环节。测试代码不仅要保证逻辑的正确性，还要保证苛刻条件（高压力、异常、错误）下不产生“有毒代码”（Poisonous Code），因此当有变化提出时，原有健壮的代码要尽量不修改，而是通过扩展来实现。否则，就需要把原有的测试过程回笼一遍，需要进行单元测试、功能测试、集成测试，甚至是验收测试。开闭原则的使用，保证软件是通过扩展来实现业务逻辑的变化，而不是修改。因此，对于新增加的类，只需新增相应的测试类，编写对应的测试方法，只要保证新增的类是正确的就可以了。

#### 1.3 手写一个单例模式

**参考答案**

饿汉式单例模式：

```cpp
public class Singleton {
    private static Singleton instance = new Singleton();
    // 私有构造方法，保证外界无法直接实例化。
    private Singleton() {}
    // 通过公有的静态方法获取对象实例
    public static Singleton getInstace() {
        return instance;
    }
}
```

懒汉式单例模式：

```cpp
public class Singleton {
    private static Singleton instance = null;
    // 私有构造方法，保证外界无法直接实例化。
    private Singleton() {}
    // 通过公有的静态方法获取对象实例
    public static Singleton getInstace() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

#### 1.4 手写一个线程安全的单例模式

**参考答案**

在懒汉式单例模式基础上实现线程同步：

```cpp
public class Singleton {
    private static Singleton instance = null;
    // 私有构造方法，保证外界无法直接实例化。
    private Singleton() {}
    // 通过公有的静态方法获取对象实例
    synchronized public static Singleton getInstace() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

上述代码对静态方法 getInstance()进行同步，以确保多线程环境下只创建一个实例。如果 getInstance()方法未被同步，并且线程 A 和线程 B 同时调用此方法，则执行`if (instance == null)`语句时都为真，那么线程 A 和线程 B 都会创建一个对象，在内存中就会出现两个对象，这样就违反了单例模式。而使用 synchronized 关键字进行同步后，则不会出现此种情况。

#### 1.5 说一说你对工厂模式的理解

**参考答案**

工厂模式的用意是定义一个创建产品对象的工厂接口，将实际创建性工作推迟到子类中。工厂模式可分为简单工厂、工厂方法和抽象工厂模式。注意，我们常说的 23 种经典设计模式，包含了工程方法模式和抽象工厂模式，而并未包含简单工厂模式。另外，我们平时说的工厂模式，一般默认是指工厂方法模式。

简单工厂

简单工厂模式其实并不算是一种设计模式，更多的时候是一种编程习惯。简单工厂的实现思路是，定义一个工厂类，根据传入的参数不同返回不同的实例，被创建的实例具有共同的父类或接口。简单工厂的适用场景是：

*   需要创建的对象较少。
*   客户端不关心对象的创建过程。

示例：

创建一个可以绘制不同形状的绘图工具，可以绘制圆形，正方形，三角形，每个图形都会有一个 draw()方法用于绘图，不看代码先考虑一下如何通过该模式设计完成此功能。

由题可知圆形，正方形，三角形都属于一种图形，并且都具有 draw 方法，所以首先可以定义一个接口或者抽象类，作为这三个图像的公共父类，并在其中声明一个公共的 draw 方法：

```cpp
public interface Shape {
    void draw();
}
```

下面就是编写具体的图形，每种图形都实现 Shape 接口：

```cpp
// 圆形
class CircleShape implements Shape {
    public CircleShape() {
        System.out.println("CircleShape: created");
    }
    @Override
    public void draw() {
        System.out.println("draw: CircleShape");
    }
}
// 正方形
class RectShape implements Shape {
    public RectShape() {
       System.out.println("RectShape: created");
    }
    @Override
    public void draw() {
       System.out.println("draw: RectShape");
    }

}
// 三角形
public class TriangleShape implements Shape {
    public TriangleShape() {
        System.out.println("TriangleShape: created");
    }
    @Override
    public void draw() {
        System.out.println("draw: TriangleShape");
    }
}
```

下面是工厂类的具体实现：

```cpp
 class ShapeFactory {
          public static Shape getShape(String type) {
              Shape shape = null;
              if (type.equalsIgnoreCase("circle")) {
                  shape = new CircleShape();
              } else if (type.equalsIgnoreCase("rect")) {
                  shape = new RectShape();
              } else if (type.equalsIgnoreCase("triangle")) {
                  shape = new TriangleShape();
              }
              return shape;
          }
   }
```

为工厂类传入不同的 type 可以 new 不同的形状，返回结果为 Shape 类型，这个就是简单工厂核心的地方了。

工厂方法

工厂方法模式是简单工厂的仅一步深化， 在工厂方法模式中，我们不再提供一个统一的工厂类来创建所有的对象，而是针对不同的对象提供不同的工厂。也就是说每个对象都有一个与之对应的工厂。

工厂方法的实现思路是，定义一个用于创建对象的接口，让子类决定将哪一个类实例化。工厂方法模式让一个类的实例化延迟到其子类。

示例：

现在需要设计一个这样的图片加载类，它具有多个图片加载器，用来加载 jpg，png，gif 格式的图片，每个加载器都有一个 read()方法，用于读取图片。下面我们完成这个图片加载类。

首先完成图片加载器的设计，编写一个加载器的公共接口：

```cpp
public interface Reader {
    void read();
}
```

然后完成各个图片加载器的代码：

```cpp
// jpg 图片加载器
class JpgReader implements Reader {
    @Override
    public void read() {
        System.out.print("read jpg");
    }
}
// png 图片加载器
class PngReader implements Reader {
    @Override
    public void read() {
        System.out.print("read png");
    }
}
// gif 图片加载器
class GifReader implements Reader {
    @Override
    public void read() {
        System.out.print("read gif");
    }
}
```

现在我们按照定义所说定义一个抽象的工厂接口 ReaderFactory：

```cpp
interface ReaderFactory {
    Reader getReader();
}
```

里面有一个 getReader()方法返回我们的 Reader 类，接下来我们把上面定义好的每个图片加载器都提供一个工厂类，这些工厂类实现了 ReaderFactory 。

```cpp
// jpg 加载器工厂
class JpgReaderFactory implements ReaderFactory {
    @Override
    public Reader getReader() {
        return new JpgReader();
    }
}
// png 加载器工厂
class PngReaderFactory implements ReaderFactory {
    @Override
    public Reader getReader() {
        return new PngReader();
    }
}
// gif 加载器工厂
class GifReaderFactory implements ReaderFactory {
    @Override
    public Reader getReader() {
        return new GifReader();
    }
}
```

在每个工厂类中我们都通过重写的 getReader()方法返回各自的图片加载器对象。

和简单工厂对比一下，最根本的区别在于，简单工厂只有一个统一的工厂类，而工厂方法是针对每个要创建的对象都会提供一个工厂类，这些工厂类都实现了一个工厂基类。

下面总结一下工厂方法的适用场景：

*   客户端不需要知道它所创建的对象的类。
*   客户端可以通过子类来指定创建对应的对象。

抽象工厂

这个模式最不好理解，而且在实际应用中局限性也蛮大的，因为这个模式并不符合开闭原则。实际开发还需要做好权衡。抽象工厂模式是工厂方法的仅一步深化，在这个模式中的工厂类不单单可以创建一个对象，而是可以创建一组对象。这是和工厂方法最大的不同点。

抽象工厂的实现思路是，提供一个创建一系列相关或相互依赖对象的接口，而无须指定它们具体的类。抽象工厂和工厂方法一样可以划分为 4 大部分：

*   AbstractFactory（抽象工厂）：声明了一组用于创建对象的方法，注意是一组。
*   ConcreteFactory（具体工厂）：它实现了在抽象工厂中声明的创建对象的方法，生成一组具体对象。
*   AbstractProduct（抽象产品）：它为每种对象声明接口，在其中声明了对象所具有的业务方法。
*   ConcreteProduct（具体产品）：它定义具体工厂生产的具体对象。

示例：

现在需要做一款跨平台的游戏，需要兼容 Android，Ios，Wp 三个移动操作系统，该游戏针对每个系统都设计了一套操作控制器（OperationController）和界面控制器（UIController），下面通过抽闲工厂方式完成这款游戏的架构设计。

由题可知，游戏里边的各个平台的 UIController 和 OperationController 应该是我们最终生产的具体产品。所以新建两个抽象产品接口。

抽象操作控制器：

```cpp
interface OperationController {
    void control();
}
```

抽象界面控制器：

```cpp
interface UIController {
    void display();
}
```

然后完成各个系统平台的具体操作控制器和界面控制器。

Android：

```cpp
class AndroidOperationController implements OperationController {
    @Override
    public void control() {
        System.out.println("AndroidOperationController");
    }
}
class AndroidUIController implements UIController {
    @Override
    public void display() {
        System.out.println("AndroidInterfaceController");
    }
}
```

IOS：

```cpp
class IosOperationController implements OperationController {
    @Override
    public void control() {
        System.out.println("IosOperationController");
    }
}
class IosUIController implements UIController {
    @Override
    public void display() {
        System.out.println("IosInterfaceController");
    }
}
```

WP：

```cpp
class WpOperationController implements OperationController {
    @Override
    public void control() {
        System.out.println("WpOperationController");
    }
}
class WpUIController implements UIController {
    @Override
    public void display() {
        System.out.println("WpInterfaceController");
    }
}
```

下面定义一个抽闲工厂，该工厂需要可以创建 OperationController 和 UIController。

```cpp
public interface SystemFactory {
    public OperationController createOperationController();
    public UIController createInterfaceController();
}
```

在各平台具体的工厂类中完成操作控制器和界面控制器的创建过程。

Android：

```cpp
public class AndroidFactory implements SystemFactory {
    @Override
    public OperationController createOperationController() {
        return new AndroidOperationController();
    }
    @Override
    public UIController createInterfaceController() {
        return new AndroidUIController();
    }
}
```

IOS：

```cpp
public class IosFactory implements SystemFactory {
    @Override
    public OperationController createOperationController() {
        return new IosOperationController();
    }
    @Override
    public UIController createInterfaceController() {
        return new IosUIController();
    }
}
```

WP：

```cpp
public class WpFactory implements SystemFactory {
    @Override
    public OperationController createOperationController() {
        return new WpOperationController();
    }
    @Override
    public UIController createInterfaceController() {
        return new WpUIController();
    }
}
```

下面总结一下抽象工厂的适用场景：

*   和工厂方法一样客户端不需要知道它所创建的对象的类。
*   需要一组对象共同完成某种功能时。并且可能存在多组对象完成不同功能的情况。
*   系统结构稳定，不会频繁的增加对象。