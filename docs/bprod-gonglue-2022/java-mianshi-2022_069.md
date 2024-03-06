# 第八章 第 4 节 Java-设计模式-4

> 原文：[`www.nowcoder.com/tutorial/10070/da3609991e654b8fba4e7f3496e8a995`](https://www.nowcoder.com/tutorial/10070/da3609991e654b8fba4e7f3496e8a995)

#### 1.12 Spring 框架中用到了哪些设计模式？

Spring 框架在实现时运用了大量的设计模式，常见的有如下几种：

1.  简单工厂

    Spring 中的 BeanFactory 就是简单工厂模式的体现，根据传入一个唯一的标识来获得 Bean 对象，但是否是在传入参数后创建还是传入参数前创建这个要根据具体情况来定。

2.  工厂方法

    实现了 FactoryBean 接口的 bean 是一类叫做 factory 的 bean。其特点是，spring 会在使用 getBean()调用获得该 bean 时，会自动调用该 bean 的 getObject()方法，所以返回的不是 factory 这个 bean，而是这个 bean 的 getOjbect()方法的返回值。

3.  单例模式

    Spring 依赖注入 Bean 实例默认是单例的。Spring 的依赖注入（包括 lazy-init 方式）都是发生在 AbstractBeanFactory 的 getBean 里。getBean 的 doGetBean 方法调用 getSingleton 进行 bean 的创建。

4.  适配器模式

    SpringMVC 中的适配器 HandlerAdatper，它会根据 Handler 规则执行不同的 Handler。即 DispatcherServlet 根据 HandlerMapping 返回的 handler，向 HandlerAdatper 发起请求处理 Handler。HandlerAdapter 根据规则找到对应的 Handler 并让其执行，执行完毕后 Handler 会向 HandlerAdapter 返回一个 ModelAndView，最后由 HandlerAdapter 向 DispatchServelet 返回一个 ModelAndView。

5.  装饰器模式

    Spring 中用到的装饰器模式在类名上有两种表现：一种是类名中含有 Wrapper，另一种是类名中含有 Decorator。

6.  代理模式

    AOP 底层就是动态代理模式的实现。即：切面在应用运行的时刻被织入。一般情况下，在织入切面时，AOP 容器会为目标对象创建动态的创建一个代理对象。SpringAOP 就是以这种方式织入切面的。

7.  观察者模式

    Spring 的事件驱动模型使用的是观察者模式，Spring 中 Observer 模式常用的地方是 listener 的实现。

8.  策略模式

    Spring 框架的资源访问 Resource 接口。该接口提供了更强的资源访问能力，Spring 框架本身大量使用了 Resource 接口来访问底层资源。Resource 接口是具体资源访问策略的抽象，也是所有资源访问类所实现的接口。

9.  模板方法模式

    Spring 模板方法模式的实质，是模板方法模式和回调模式的结合，是 Template Method 不需要继承的另一种实现方式。Spring 几乎所有的外接扩展都采用这种模式。