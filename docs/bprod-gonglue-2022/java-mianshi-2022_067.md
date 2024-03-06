# 第八章 第 2 节 Java-设计模式-2

> 原文：[`www.nowcoder.com/tutorial/10070/335e69ad5b0e4d9fa7836279834e1619`](https://www.nowcoder.com/tutorial/10070/335e69ad5b0e4d9fa7836279834e1619)

#### 1.6 简单工厂模式和抽象工厂模式有什么区别？

**参考答案**

简单工厂模式其实并不算是一种设计模式，更多的时候是一种编程习惯。简单工厂的实现思路是，定义一个工厂类，根据传入的参数不同返回不同的实例，被创建的实例具有共同的父类或接口。

工厂方法模式是简单工厂的仅一步深化， 在工厂方法模式中，我们不再提供一个统一的工厂类来创建所有的对象，而是针对不同的对象提供不同的工厂。也就是说每个对象都有一个与之对应的工厂。工厂方法的实现思路是，定义一个用于创建对象的接口，让子类决定将哪一个类实例化。工厂方法模式让一个类的实例化延迟到其子类。

抽象工厂模式是工厂方法的仅一步深化，在这个模式中的工厂类不单单可以创建一个对象，而是可以创建一组对象。这是和工厂方法最大的不同点。抽象工厂的实现思路是，提供一个创建一系列相关或相互依赖对象的接口，而无须指定它们具体的类。

#### 1.7 如何实现工厂模式？

**参考答案**

简单工厂

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

抽象工厂

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