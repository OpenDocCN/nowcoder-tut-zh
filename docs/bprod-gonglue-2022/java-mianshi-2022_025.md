# 第三章 第 1 节 Java-web 开发-1

> 原文：[`www.nowcoder.com/tutorial/10070/367df494ee75408490de10ef057f422d`](https://www.nowcoder.com/tutorial/10070/367df494ee75408490de10ef057f422d)

## 1\. Spring Boot

#### 1.1 说说你对 Spring Boot 的理解

**参考答案**

从本质上来说，Spring Boot 就是 Spring，它做了那些没有它你自己也会去做的 Spring Bean 配置。Spring Boot 使用“习惯优于配置”的理念让你的项目快速地运行起来，使用 Spring Boot 很容易创建一个能独立运行、准生产级别、基于 Spring 框架的项目，使用 Spring Boot 你可以不用或者只需要很少的 Spring 配置。

简而言之，Spring Boot 本身并不提供 Spring 的核心功能，而是作为 Spring 的脚手架框架，以达到快速构建项目、预置三方配置、开箱即用的目的。Spring Boot 有如下的优点：

*   可以快速构建项目；
*   可以对主流开发框架的无配置集成；
*   项目可独立运行，无需外部依赖 Servlet 容器；
*   提供运行时的应用监控；
*   可以极大地提高开发、部署效率；
*   可以与云计算天然集成。

#### 1.2 Spring Boot Starter 有什么用？

**参考答案**

Spring Boot 通过提供众多起步依赖（Starter）降低项目依赖的复杂度。起步依赖本质上是一个 Maven 项目对象模型（Project Object Model, POM），定义了对其他库的传递依赖，这些东西加在一起即支持某项功能。很多起步依赖的命名都暗示了它们提供的某种或某类功能。

举例来说，你打算把这个阅读列表应用程序做成一个 Web 应用程序。与其向项目的构建文件里添加一堆单独的库依赖，还不如声明这是一个 Web 应用程序来得简单。你只要添加 Spring Boot 的 Web 起步依赖就好了。

#### 1.3 介绍 Spring Boot 的启动流程

**参考答案**

首先，Spring Boot 项目创建完成会默认生成一个名为 `*Application` 的入口类，我们是通过该类的 main 方法启动 Spring Boot 项目的。在 main 方法中，通过 SpringApplication 的静态方法，即 run 方法进行 SpringApplication 类的实例化操作，然后再针对实例化对象调用另外一个 run 方法来完成整个项目的初始化和启动。

SpringApplication 调用的 run 方法的大致流程，如下图：

![](img/2961c8eb8f068ca29bd2a3861af1d4a3.png)

其中，SpringApplication 在 run 方法中重点做了以下操作：

*   获取监听器和参数配置；
*   打印 Banner 信息；
*   创建并初始化容器；
*   监听器发送通知。

当然，除了上述核心操作，run 方法运行过程中还涉及启动时长统计、异常报告、启动日志、异常处理等辅助操作。比较完整的流程，可以参考如下源代码：

```cpp
public ConfigurableApplicationContext run(String... args) {
    // 创建 StopWatch 对象，用于统计 run 方法启动时长。
    StopWatch stopWatch = new StopWatch();
    // 启动统计
    stopWatch.start();
    ConfigurableApplicationContext context = null;
    Collection<SpringBootExceptionReporter> exceptionReporters = new ArrayList<>();
    // 配置 Headless 属性
    configureHeadlessProperty();
    // 获得 SpringApplicationRunListener 数组，
    // 该数组封装于 SpringApplicationRunListeners 对象的 listeners 中。
    SpringApplicationRunListeners listeners = getRunListeners(args);
    // 启动监听，遍历 SpringApplicationRunListener 数组每个元素，并执行。
    listeners.starting();
    try {
        // 创建 ApplicationArguments 对象
        ApplicationArguments applicationArguments = 
            new DefaultApplicationArguments(args);
        // 加载属性配置，包括所有的配置属性。
        ConfigurableEnvironment environment = 
            prepareEnvironment(listeners, applicationArguments);
        configureIgnoreBeanInfo(environment);
        // 打印 Banner
        Banner printedBanner = printBanner(environment);
        // 创建容器
        context = createApplicationContext();
        // 异常报告器
        exceptionReporters = getSpringFactoriesInstances(
            SpringBootExceptionReporter.class,
            new Class[] { ConfigurableApplicationContext.class }, context);
        // 准备容器，组件对象之间进行关联。
        prepareContext(context, environment, 
                       listeners, applicationArguments, printedBanner);
        // 初始化容器
        refreshContext(context);
        // 初始化操作之后执行，默认实现为空。
        afterRefresh(context, applicationArguments);
        // 停止时长统计
        stopWatch.stop();
        // 打印启动日志
        if (this.logStartupInfo) {
            new StartupInfoLogger(this.mainApplicationClass)
                .logStarted(getApplicationLog(), stopWatch);
        }
        // 通知监听器：容器完成启动。
        listeners.started(context);
        // 调用 ApplicationRunner 和 CommandLineRunner 的运行方法。
        callRunners(context, applicationArguments);
    } catch (Throwable ex) {
        // 异常处理
        handleRunFailure(context, ex, exceptionReporters, listeners);
        throw new IllegalStateException(ex);
    }

    try {
        // 通知监听器：容器正在运行。
        listeners.running(context);
    } catch (Throwable ex) {
        // 异常处理
        handleRunFailure(context, ex, exceptionReporters, null);
        throw new IllegalStateException(ex);
    }
    return context;
}
```

#### 1.4 Spring Boot 项目是如何导入包的？

**参考答案**

通过 Spring Boot Starter 导入包。

Spring Boot 通过提供众多起步依赖（Starter）降低项目依赖的复杂度。起步依赖本质上是一个 Maven 项目对象模型（Project Object Model, POM），定义了对其他库的传递依赖，这些东西加在一起即支持某项功能。很多起步依赖的命名都暗示了它们提供的某种或某类功能。

举例来说，你打算把这个阅读列表应用程序做成一个 Web 应用程序。与其向项目的构建文件里添加一堆单独的库依赖，还不如声明这是一个 Web 应用程序来得简单。你只要添加 Spring Boot 的 Web 起步依赖就好了。

#### 1.5 请描述 Spring Boot 自动装配的过程

**参考答案**

使用 Spring Boot 时，我们只需引入对应的 Starters，Spring Boot 启动时便会自动加载相关依赖，配置相应的初始化参数，以最快捷、简单的形式对第三方软件进行集成，这便是 Spring Boot 的自动配置功能。Spring Boot 实现该运作机制锁涉及的核心部分如下图所示：

![](img/ee37a470bc33fab3b328ab18baa68892.png)

整个自动装配的过程是：Spring Boot 通过@EnableAutoConfiguration 注解开启自动配置，加载 spring.factories 中注册的各种 AutoConfiguration 类，当某个 AutoConfiguration 类满足其注解@Conditional 指定的生效条件（Starters 提供的依赖、配置或 Spring 容器中是否存在某个 Bean 等）时，实例化该 AutoConfiguration 类中定义的 Bean（组件等），并注入 Spring 容器，就可以完成依赖框架的自动配置。

#### 1.6 说说你对 Spring Boot 注解的了解

**参考答案**

@SpringBootApplication 注解：

在 Spring Boot 入口类中，唯一的一个注解就是@SpringBootApplication。它是 Spring Boot 项目的核心注解，用于开启自动配置，准确说是通过该注解内组合的@EnableAutoConfiguration 开启了自动配置。

@EnableAutoConfiguration 注解：

@EnableAutoConfiguration 的主要功能是启动 Spring 应用程序上下文时进行自动配置，它会尝试猜测并配置项目可能需要的 Bean。自动配置通常是基于项目 classpath 中引入的类和已定义的 Bean 来实现的。在此过程中，被自动配置的组件来自项目自身和项目依赖的 jar 包中。

@Import 注解：

@EnableAutoConfiguration 的关键功能是通过@Import 注解导入的 ImportSelector 来完成的。从源代码得知@Import(AutoConfigurationImportSelector.class)是@EnableAutoConfiguration 注解的组成部分，也是自动配置功能的核心实现者。

@Conditional 注解：

@Conditional 注解是由 Spring 4.0 版本引入的新特性，可根据是否满足指定的条件来决定是否进行 Bean 的实例化及装配，比如，设定当类路径下包含某个 jar 包的时候才会对注解的类进行实例化操作。总之，就是根据一些特定条件来控制 Bean 实例化的行为。

@Conditional 衍生注解：

在 Spring Boot 的 autoconfigure 项目中提供了各类基于@Conditional 注解的衍生注解，它们适用不同的场景并提供了不同的功能。通过阅读这些注解的源码，你会发现它们其实都组合了@Conditional 注解，不同之处是它们在注解中指定的条件（Condition）不同。

*   @ConditionalOnBean：在容器中有指定 Bean 的条件下。
*   @ConditionalOnClass：在 classpath 类路径下有指定类的条件下。
*   @ConditionalOnCloudPlatform：当指定的云平台处于 active 状态时。
*   @ConditionalOnExpression：基于 SpEL 表达式的条件判断。
*   @ConditionalOnJava：基于 JVM 版本作为判断条件。
*   @ConditionalOnJndi：在 JNDI 存在的条件下查找指定的位置。
*   @ConditionalOnMissingBean：当容器里没有指定 Bean 的条件时。
*   @ConditionalOnMissingClass：当类路径下没有指定类的条件时。
*   @ConditionalOnNotWebApplication：在项目不是一个 Web 项目的条件下。
*   @ConditionalOnProperty：在指定的属性有指定值的条件下。
*   @ConditionalOnResource：类路径是否有指定的值。
*   @ConditionalOnSingleCandidate：当指定的 Bean 在容器中只有一个或者有多个但是指定了首选的 Bean 时。
*   @ConditionalOnWebApplication：在项目是一个 Web 项目的条件下。