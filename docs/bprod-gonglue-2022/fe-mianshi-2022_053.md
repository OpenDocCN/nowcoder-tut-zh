# 第六章 第 5 节 前端进阶-移动端

> 原文：[`www.nowcoder.com/tutorial/10072/4e9329ed6fbc44efae31245bb99540d2`](https://www.nowcoder.com/tutorial/10072/4e9329ed6fbc44efae31245bb99540d2)

#### 1.2 移动端适配方案

**参考答案：**

适配思路

设计稿（750*1334） ---> 开发 ---> 适配不同的手机屏幕，使其显得合理

原则

1.  开发时方便，写代码时设置的值要和标注的 160px 相关
2.  方案要适配大多数手机屏幕，并且无 BUG
3.  用户体验要好，页面看着没有不适感

思路

1.  写页面时，按照设计稿写固定宽度，最后再统一缩放处理，在不同手机上都能用
2.  按照设计稿的标准开发页面，在手机上部分内容根据屏幕宽度等比缩放，部分内容按需要变化，需要缩放的元素使用 rem, vw 相对单位，不需要缩放的使用 px
3.  固定尺寸+弹性布局，不需要缩放

**viewport 适配**

根据设计稿标准（750px 宽度）开发页面，写完后页面及元素自动缩小，适配 375 宽度的屏幕

在 head 里设置如下代码

```cpp
<meta name="viewport" content="width=750,initial-scale=0.5">
```

initial-scale = 屏幕的宽度 / 设计稿的宽度

为了适配其他屏幕，需要动态的设置 initial-scale 的值

```cpp
<head>
  <script>
    const WIDTH = 750
    const mobileAdapter = () => {
      let scale = screen.width / WIDTH
      let content = `width=${WIDTH}, initial-scale=${scale}, maximum-scale=${scale}, minimum-scale=${scale}`
      let meta = document.querySelector('meta[name=viewport]')
      if (!meta) {
        meta = document.createElement('meta')
        meta.setAttribute('name', 'viewport')
        document.head.appendChild(meta)
      }
      meta.setAttribute('content',content)
    }
    mobileAdapter()
    window.onorientationchange = mobileAdapter //屏幕翻转时再次执行
  </script>
</head>
```

缺点就是边线问题，不同尺寸下，边线的粗细是不一样的（等比缩放后），全部元素都是等比缩放，实际显示效果可能不太好

**vw 适配（部分等比缩放）**

1.  开发者拿到设计稿（假设设计稿尺寸为 750px，设计稿的元素标注是基于此宽度标注）
2.  开始开发，对设计稿的标注进行转换，把 px 换成 vw。比如页面元素字体标注的大小是 32px，换成 vw 为 (100/750)*32 vw
3.  对于需要等比缩放的元素，CSS 使用转换后的单位
4.  对于不需要缩放的元素，比如边框阴影，使用固定单位 px

关于换算，为了开发方便，利用自定义属性，CSS 变量

```cpp
<head>
  <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, minimum-scale=1">
  <script>
    const WIDTH = 750
    //:root { --width: 0.133333 } 1 像素等于多少 vw
    document.documentElement.style.setProperty('--width', (100 / WIDTH)) 
  </script>
</head>
```

注意此时，meta 里就不要去设置缩放了

业务代码里就可以写

```cpp
header {
  font-size: calc(28vw * var(--width))
}
```

实现了按需缩放

**rem 适配**

1.  开发者拿到设计稿（假设设计稿尺寸为 750px，设计稿的元素标是基于此宽度标注）
2.  开始开发，对设计稿的标注进行转换
3.  对于需要等比缩放的元素，CSS 使用转换后的单位
4.  对于不需要缩放的元素，比如边框阴影，使用固定单位 px

假设设计稿的某个字体大小是 40px, 手机屏幕上的字体大小应为 420/750*40 = 22.4px (体验好)，换算成 rem（相对于 html 根节点，假设 html 的 font-size = 100px,）则这个字体大小为 0.224 rem

写样式时，对应的字体设置为 0.224 rem 即可，其他元素尺寸也做换算...

但是有问题

举个 ，设计稿的标注 是 40px，写页面时还得去做计算，很麻烦（全部都要计算）

能不能规定一下，看到 40px ,就应该写 40/100 = 0.4 rem,这样看到就知道写多少了（不用计算），此时的 html 的 font-size 就不能是 100px 了，应该为 (420*100)/750 = 56px，100 为我们要规定的那个参数

根据不同屏幕宽度，设置 html 的 font-size 值

```cpp
<head>
  <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, minimum-scale=1">
  <script>
    const WIDTH = 750 //设计稿尺寸
    const setView = () => {
      document.documentElement.style.fontSize = (100 * screen.width / WIDTH) + 'px'
    }
    window.onorientationchange = setView
    setView()
  </script>
</head>
```

对于需要等比缩放的元素，CSS 使用转换后的单位

```cpp
header {
  font-size: .28rem;
}
```

对于不需要缩放的元素，比如边框阴影，使用固定单位 px

```cpp
header > span.active {
  color: #fff;
  border-bottom: 2px solid rgba(255, 255, 255, 0.3);
}
```

假设 html 的 font size = 1px 的话，就可以写 28 rem 了，更方便了，但是浏览器对字体大小有限制，设为 1px 的话，在浏览器中是失效的，会以 12px（或者其他值） 做一个计算 , 就会得到一个很夸张的结果，所以可以把 html 写的大一些

使用 sass 库时

JS 处理还是一样的，但看着好看些

```cpp
@function px2rem($px) {
  @return $px * 1rem / 100;
}

header {
  font-size: px2rem(28);
}
```

以上的三种适配方案，都是等比缩放，放到 ipad 上时（设计稿以手机屏幕设计的），页面元素会很大很丑，有些场景下，并不需要页面整体缩放（viewport 自动处理的也很好了），所以有时只需要合理的布局即可。

**弹性盒适配（合理布局）**

```cpp
<meta name="viewport" content="width=device-width">
```

使用 flex 布局

```cpp
section {
  display: flex;
}
```

总结一下，什么样的页面需要做适配（等比缩放）呢

*   页面中的布局是栅格化的

换了屏幕后，到底有多宽多高很难去做设置，整体的都需要改变，所以需要整体的缩放

*   头屏大图，宽度自适应，高度固定的话，对于不同的屏幕，形状就会发生改变（放到 ipad 上就变成长条了），宽度变化后，高度也要保持等比例变化

以上所有的适配都是宽度的适配，但是在某些场景下，也会出现高度的适配

比如大屏，需要适配很多的电视尺寸，要求撑满屏幕，不能有滚动条，此时若换个屏幕

此时需要考虑小元素用 vh, 宽和高都用 vh 去表示，中间的大块去自适应，这就做到了大屏的适配，屏幕变小了，整体变小了（体验更好），中间这块撑满了屏幕

对于更复杂的场景，需要更灵活考虑，没有一种适配方式可以囊括所有场景。

#### 2.2 开发 APP 的技术栈是怎么样的

**参考答案**：

手机 App 的技术栈可以分成三类：**原生 App 技术栈** （native technology stack）、**混合 App 技术栈** （hybrid technology stack）、**跨平台 App 技术栈** （cross-platform technology stack），H5 开发主要用在混合技术栈。但是，跨平台技术栈的某些容器也会用到（比如 React Native），因为它们的 UI 层借鉴了 Web 模型。混合技术栈和跨平台技术栈的基础，都是原生技术栈，因为最终都要编译成原生 App。所以，不管使用哪一种技术栈，多多少少要了解一些各平台的原生技术。

**解析**：

（1）**原生 App 技术栈** （native technology stack）

原生技术栈指的是，只能用于特定手机平台的开发技术。比如，安卓平台的 Java 技术栈，iOS 平台的 Object-C 技术栈或 Swift 技术栈。

这种技术栈只能用在一个平台，不能跨平台。

（2）**混合 App 技术栈** （hybrid technology stack）

混合技术栈指的是开发混合 App 的技术，也就是把 Web 网页放到特定的容器中，然后再打包成各个平台的原生 App。所以，混合技术栈其实是 Web 技术栈 + 容器技术栈，典型代表是 PhoneGap、Cordova、Ionic 等框架。

如果已经掌握了 Web 技术，这个技术栈就主要学习容器提供的 API Bridge，网页通过它们去调用底层硬件的 API。

（3）**跨平台 App 技术栈** （cross-platform technology stack）

跨平台技术栈指的是使用一种技术，同时支持多个手机平台。它与混合技术栈的区别是，不使用 Web 技术，即它的页面不是 HTML5 页面，而是使用自己的语法写的 UI 层，然后编译成各平台的原生 App。

这个技术栈就是纯粹的容器技术栈，React Native、Xamarin、Flutter 都属于这一类。学习时，除了学习容器的 API Bridge，还要学习容器提供的 UI 层，即怎么写页面。

**WebView 控件**

讲解具体的技术栈之前，大家需要知道，不管什么技术，最终在 App 里面显示网页，一定需要一个网页引擎，这样才能解析网页。

通常情况下，App 内部会使用 WebView 控件作为网页引擎。这是系统自带的控件，专门用来显示网页。应用程序的界面，只要放上 WebView，就好像内嵌了浏览器窗口，可以显示网页。

不同的 App 技术栈要显示网页，区别仅仅在于怎么处理 WebView 这个原生控件。

> *   原生技术栈：需要开发者自己把 WebView 控件放到页面上。
> *   混合技术栈：页面本身就是网页，默认在 WebView 中显示。
> *   跨平台技术栈：提供一个 WebView 的语法，编译的时候将其换成原生的 WebView。

注意，不同系统的 WebView 控件名称不一样，安卓系统就叫 WebView，iOS 系统有较老的 UIWebView，也有较新的 WKWebView，作用都是一样的，差异在于功能的强弱。

**原生技术栈**

原生技术栈分成 iOS 和安卓两个平台。

简单说，iOS 的原生技术栈就是使用 Object-C 语言或 Swift 语言，在 Xcode 开发环境中编程。安卓的原生技术栈，则是使用 Java 语言或 Kotlin 语言，开发环境是 Android Studio。

**混合技术栈**

上面的原生技术栈需要自己新建 WebView 实例，相比之下，混合技术栈就简单多了。因为页面就是网页，所以容器已经设置好了 WebView，开发者直接写页面即可。

框架种类

混合技术栈的各种容器框架之中，历史最悠久是 [PhoneGap](https://phonegap.com/)，诞生于 2009 年。后来在 2011 年被 Adobe 公司收购，改名为 Adobe PhoneGap。

Adobe 公司将 PhoneGap 的核心代码，后来都捐给了 Apache 基金会，作为一个全新的开源项目，名为 [Apache Cordova](https://cordova.apache.org/)。

PhoneGap 和 Cordova 现在是两个独立发展的开源项目，但是彼此有密切的关系，可以简单理解成 Cordova 是 PhoneGap 的内核，PhoneGap 是 Cordova 的发行版。

后来，其他人也开始基于 Cordova 封装自己的框架，所以市场上有许多基于 Cordova 的开源框架，比较著名的有 [Ionic](https://ionicframework.com/)、[Monaca](https://monaca.io/)、[Framework7](https://framework7.io/) 等。

所有这些框架的共同点，都是使用 Web 技术（HTML5 + CSS + JavaScript）开发页面，再由框架分别打包成 iOS 和安卓的 App 安装包。它们的优点是开发简单、周期短、成本低，缺点是功能和性能都很有限。

**跨平台技术栈**

上面的混合技术栈使用 HTML 语言编写页面，再用 WebView 控件加载页面，所以只写一次页面，就能支持多个平台。跨平台技术栈也能做到多平台支持，但是原理完全不同。

跨平台技术栈的框架，都是使用自己的语法编写页面，不使用 Web 技术，编译的时候再将其转为原生控件，或者使用自己的底层控件，生成原生 App。这样就完全解决了 Web 页面性能不佳的问题。下面介绍三个这样的框架。

> *   React Native: 使用 JavaScipt 语言编写页面
> *   Xamarin：使用 C# 语言编写页面
> *   Flutter：使用 Dart 语言编写页面

1.  React Native

（1）原理

​ 2013 年， Facebook 公司发布了 React 框架。这个框架是为网页开发设计的，核心思想是在网页之上，建立一 个 UI 的抽象层，所有数据操作都在这个抽象层完成（即在内存里面完成），然后再渲染成网页的 DOM 结构， 这样就提升了性能。

​ 很快，工程师们就意识到了，UI 抽象层本质上是一种数据结构，与底层设备无关，不仅可以渲染成网页，也可 以渲染成手机的原生页面。这样的话，只要写一次 React 页面，就能分别编译成 iOS 和安卓的原生 App。这就 是 React Native 项目的由来。

​ 注意，React Native 虽然也使用 JavaScript 语言，并且写法看上去像 Web 页面，但其实所有控件都是自己定义 的，编译时再一一翻译为对应的原生控件。举例来说，React Native 的文本渲染控件是，翻译成 iOS 控件为 UIView，翻译成安卓控件为`TextView`。这种做即保证了性能，又做到了跨平台支持，所以一诞生就引起开发者 的关注，成了热门技术。

​ 还有一个 [NativeScript](https://www.nativescript.org/) 框架，跟 React Native 很像，也是使用 JavaScript 语言，然后编译成原生控件。不过， 它的开发模型是基于 Angular.js，而不是 React。

（2）React Native 的问题

​ React Native 的想法虽然很美好，但是实际开发中出现了各种各样的问题。

​ 最主要的一个问题是， UI 抽象层翻译出来的 iOS 和安卓原生页面，做不到完全一致，尤其是复杂页面，样式或 功能存在差异。编译出来两个平台的原生 App 往往是一个正常，另一个会出现各种奇怪的小毛病。ReactNative 的底层还是没有做到无缝适配，它至今没有发布 1.0 版（2019 年底是 0.61 版），这多多少少也说明了一些题。

​ 如果你想用 React Native 做到 iOS 和安卓体验一致，并且充分发挥原生控件的功能，就需要同时熟悉 React Native、iOS、安卓三个平台，这对开发者的要求实在太高了。Airbnb 公司在使用 React Native 两年后，宣 布放弃，改用原生技术栈。

2.  Xamarin

    Xamarin 是微软公司的跨平台 App 开发框架，原理跟 React Native 很相似，只不过它的语言是 C#。

    它的使用需要 Visual Studio，这里就不举例了。

3.  Flutter

    [Flutter](https://flutter.dev/) 是谷歌公司最新的跨平台开发框架。它为了解决 React Native 的平台差异问题，采用了一个完全不同的方案。

    它自己实现了一套控件。打包的时候，会把这套控件打包进每一个 App，因此不存在调用原生控件的问题。不管什么平台，都调用内嵌的自己那套控件，就能做到 iOS 和安卓体验完全一致。

    Flutter 历史还不长，应用还不广泛，API 也没稳定下来。但是很值得关注。

    它是 Flutter 的官方语言，接近 JavaScript 语法，但是多了静态类型支持。

#### 2.3 描述一下移动端跨平台

**参考答案**：

跨平台开发的目的

*   线上动态性，不需要频繁更新版本即可实现新业务的上线；
*   增加代码复用，减少开发者对多个平台差异适配的工作量，解决多端不一致的问题；
*   提高业务专注的同时，提供比 web 更好的体验；
*   降低开发成本.

跨平台开发流派

*   Web 流：也被称为 Hybrid 技术，它基于 Web 相关技术来实现界面及功能
    `Cordova，AppCan，小程序，快应用`
*   代码转换流：将某个语言转成 Objective-C、Java 或 C#，然后使用不同平台下的官方工具来开发
    `java2OC，OC2Java，java2C#`
*   编译流：将某个语言编译为二进制文件，生成动态库或打包成 apk/ipa/xap 文件
    `Xamarin`
*   虚拟机流：通过将某个语言的虚拟机移植到不同平台上来运行
    `Flutter，Titanium，React Native，Weex`

跨平台开发主流技术

*   Flutter（Google）
*   ReactNative（FaceBook）
*   Weex（Alibaba）
*   Hybrid App
*   Cordova（原 PhoneGap，Adobe）（）
*   小程序，快应用

比较

| 解决方案 | ReactNative | Weex | Flutter | Hybrid App |
| :-: | :-: | :-: | :-: | :-: |
| 平台实现 | JavaScript | JavaScript | 无桥接，原生编码 | 无桥接，原生编码 |
| 引擎 | JSCore | JS V8 | Flutter engine | 原生渲染 |
| 核心语言 | React | Vue | Dart | Java/Obeject-C |
| Apk 大小（Release） | 7.6M | 10.6M | 8.1M |  |
| bundle 文件大小 | 默认单一，较大 | 较小，多页面可多文件 | 不需要 | 不需要 |
| 上手难度（原生角度） | 较高 | 一般 | 一般 | 容易 |
| 框架程度 | 较重 | 较轻 | 重 | - |
| 特点 | 适合开发整体 App | 适合单页面 | 适合开发整体 App | 适合开发整体 App |
| 社区 | 丰富，FaceBook 重点维护 | 有点残念，托管 apache | 刚出道小鲜肉，拥护者众多 | 丰富 |
| 线上动态性 | ✅ | ✅ | ❎ | ❎ |
| 跨平台支持 | Android、iOS | Android、iOS、Web | Android、iOS | Android、iOS |

**解析**：

**Flutter**

闲鱼、美团，饿了么、NOW 直播（腾讯）、京东金融

> Flutter 是谷歌的最新移动 UI 框架。

优点

*   热重载（Hot Reload），利用 Android Studio 直接一个 ctrl+s 就可以保存并重载，模拟器立马就可以看见效果，相比原生冗长的编译过程强很多；
*   一切皆为 Widget 的理念，对于 Flutter 来说，手机应用里的所有东西都是 Widget，通过可组合的空间集合、丰富的动画库以及分层课扩展的架构实现了富有感染力的灵活界面设计；
*   借助可移植的 GPU 加速的渲染引擎以及高性能本地代码运行时以达到跨平台设备的高质量用户体验。 简单来说就是：最终结果就是利用 Flutter 构建的应用在运行效率上会和原生应用差不多。

缺点

*   不支持热更新；
*   三方库很少，需要自己造轮子；
*   dart 语言编写，掌握该语言的开发者很少。

**React Native**

墨刀，京东，手机百度 ,腾讯 QQ，QQ 空间，Facebook 及旗下应用

> React Native (简称 RN)是 Facebook 于 2015 年 4 月开源的跨平台移动应用开发框架

优点

*   效率体验接近原生应用质量，发布和开发成本低于原生 App；
*   支持热更新，快速迭代；
*   社区活跃，基本坑点都能解决；
*   代码跨越双平台

缺点

*   RN 的开源库质量不可靠；
*   RN 运行时的初始化太慢，首次渲染时间慢（需要从 主线程 -> JS -> Yoga -> 主线程）；
*   调试困难，JSCore 在 iOS / Android 上不一致 （Android 上是 RN 自己 bundle 的），很难 debug 这种坑

**WEEX**

淘宝，天猫，支付宝，网易考拉，网易严选

> 2016 年 4 月 21 日，阿里巴巴在 Qcon 大会上宣布跨平台移动开发工具 Weex。

优点

*   单页开发模式效率极高，热更新发包体积小，并且跨平台性更强

缺点

*   社区没有 RN 活跃，功能尚不健全，暂不适合完全使用 Weex 开发 App

**Hybrid App（Android/iOS+Html5）**

微信，爱奇艺，我爱我家

> Hybrid App 主要以 JS+Native 两者相互调用为主，从开发层面实现“一次开发，多处运行”的机制，成为真正适合跨平台的开发

优点

*   兼具了 Native App 良好用户体验的优势；
*   提升了开发效率，h5 可以多平台复用，由服务器快速迭代；
*   实现简单，且原生与 h5 开发人员充沛。

缺点

*   体验比不上原生,webView 性能差；
*   适用部分展示页面，复杂交互仍需要原生开发；
*   需要对应平台人员配合，jsApi 因需求需要调整，版本迭代并不自由.

#### 2.4 谈谈移动端点击

**参考答案**：

**移动端 300 ms 点击（click 事件）延迟**

由于移动端会有双击缩放的这个操作，因此浏览器在 click 之后要等待 300ms，判断这次操作是不是双击。

解决方案：

1.  禁用缩放：user-scalable=no
2.  更改默认的视口宽度
3.  CSS touch-action

**点击穿透问题**

因为 click 事件的 300ms 延迟问题，所以有可能会在某些情况触发多次事件。

解决方案：

1.  只用 touch
2.  只用 click

#### 2.5 什么是响应式开发？

**参考答案**：

响应式开发是前端开发工作比较常见的工作内容，随着移动互联网的发展，移动端设计越来越重要，很多项目都是移动端项目先开发，而后是 PC 端的开发，为了降低运营成本和开发成本，同一个网站要能兼容 PC 端和移动端显示呼之欲出，进而响应式开发成了前端开发人员必备的技能，所以响应式开发的技术必须掌握。

**什么是响应式**

顾名思义，同一个网站兼容不同的大小的设备。如 PC 端、移动端（平板、横屏、竖排）的显示风格。

**需要用到的技术**

1.  Media Query（媒体查询）

用于查询设备是否符合某一特定条件，这些特定条件包括屏幕尺寸，是否可触摸，屏幕精度，横屏竖屏等信息。

2.  使用 em 或 rem 做尺寸单位

用于文字大小的响应和弹性布局。

3.  禁止页面缩放

```cpp
<meta name="viewport" content="initial-scale=1, width=device-width, maximum-scale=1, user-scalable=no" />
复制代码
```

4.  屏幕尺寸响应

a) 固定布局：页面居中，两边留白，他能适应大于某个值一定范围的宽度，但是如果太宽就会有很多留白，太窄会出现滚动条，在 PC 页面上很常见。

b) 流动布局：屏幕尺寸在一定范围内变化时，不改变模块布局，只改变模块尺寸比例。比固定布局更具响应能力，两边不留白，但是也只能适应有限的宽度变化范围，否则模块会被挤（拉）得不成样子。

c) 自定义布局：上面几种布局方式都无法跨域大尺寸变化，所以适当时候我们需要改变模块的位置排列或者隐藏一些次要元素。

d) 栅格布局：这种布局方式使得模块之间非常容易对齐，易于模块位置的改变用于辅助自定义布局。

**响应式设计注意事项**

1.宽度不固定，可以使用百分比

```cpp
#head{width:100%;}
#content{width:50%;}
```

2.  图片处理

图片的宽度和高度设置等比缩放，可以设置图片的 width 为百分比，height:auto;

背景图片可以使用 background-size 指定背景图片的大小。