# 第六章 第 6 节 前端进阶-性能

> 原文：[`www.nowcoder.com/tutorial/10072/f3ea278c4c30401b914b00faf80c245f`](https://www.nowcoder.com/tutorial/10072/f3ea278c4c30401b914b00faf80c245f)

#### 3.1 前端性能优化手段

**参考答案**：

前端性能优化手段从以下几个方面入手：**加载优化**、**执行优化**、**渲染优化**、**样式优化**、**脚本优化**

**加载优化**:减少 HTTP 请求、缓存资源、压缩代码、无阻塞、首屏加载、按需加载、预加载、压缩图像、减少 Cookie、避免重定向、异步加载第三方资源

**执行优化**：CSS 写在头部，JS 写在尾部并异步、避免 img、iframe 等的 src 为空、尽量避免重置图像大小、图像尽量避免使用 DataURL

**渲染优化**：设置 viewport、减少 DOM 节点、优化动画、优化高频事件、GPU 加速

**样式优化**：避免在 HTML 中书写 style、避免 CSS 表达式、移除 CSS 空规则、正确使用`display：display`、不滥用 float 等

**脚本优化**：减少重绘和回流、缓存 DOM 选择与计算、缓存.length 的值、尽量使用事件代理、尽量使用 id 选择器、touch 事件优化

**解析**：

**加载优化**

*   减少 HTTP 请求：尽量减少页面的请求数(首次加载同时请求数不能超过 4 个)，移动设备浏览器同时响应请求为 4 个请求(`Android`支持 4 个，`iOS5+`支持 6 个)

    *   合并 CSS 和 JS
    *   使用 CSS 精灵图
*   缓存资源：使用缓存可减少向服务器的请求数，节省加载时间，所有静态资源都要在服务器端设置缓存，并且尽量使用长缓存(使用时间戳更新缓存)

    *   缓存一切可缓存的资源
    *   使用长缓存
    *   使用外联的样式和脚本
*   压缩代码：减少资源大小可加快网页显示速度，对代码进行压缩，并在服务器端设置`GZip`

    *   压缩代码(多余的缩进、空格和换行符)
    *   启用 Gzip
*   无阻塞：头部内联的样式和脚本会阻塞页面的渲染，样式放在头部并使用`link`方式引入，脚本放在尾部并使用异步方式加载

*   首屏加载：首屏快速显示可大大提升用户对页面速度的感知，应尽量针对首屏的快速显示做优化

*   按需加载：将不影响首屏的资源和当前屏幕不用的资源放到用户需要时才加载，可大大提升显示速度和降低总体流量(按需加载会导致大量重绘，影响渲染性能)

    *   懒加载
    *   滚屏加载
    *   Media Query 加载
*   预加载：大型资源页面可使用`Loading`，资源加载完成后再显示页面，但加载时间过长，会造成用户流失

    *   可感知 Loading：进入页面时`Loading`
    *   不可感知 Loading：提前加载下一页
*   压缩图像：使用图像时选择最合适的格式和大小，然后使用工具压缩，同时在代码中用`srcset`来按需显示(

    过度压缩图像大小影响图像显示效果)

    *   使用[TinyJpg](https://tinyjpg.com/)和[TinyPng](https://tinypng.com/)压缩图像
    *   使用 CSS3、SVG、IconFont 代替图像
    *   使用 img 的 srcset 按需加载图像
    *   选择合适的图像：`webp`优于`jpg`，`png8`优于`gif`
    *   选择合适的大小：首次加载不大于`1014kb`、不宽于`640px`
    *   PS 切图时 D 端图像保存质量为 80，M 端图像保存质量为 60
*   减少 Cookie：`Cookie`会影响加载速度，静态资源域名不使用`Cookie`

*   避免重定向：重定向会影响加载速度，在服务器正确设置避免重定向

*   异步加载第三方资源：第三方资源不可控会影响页面的加载和显示，要异步加载第三方资源

**执行优化**

*   **CSS 写在头部，JS 写在尾部并异步**
*   **避免 img、iframe 等的 src 为空**：空`src`会重新加载当前页面，影响速度和效率
*   **尽量避免重置图像大小**：多次重置图像大小会引发图像的多次重绘，影响性能
*   **图像尽量避免使用 DataURL**：`DataURL`图像没有使用图像的压缩算法，文件会变大，并且要解码后再渲染，加载慢耗时长

**渲染优化**

*   **设置 viewport**：HTML 的`viewport`可加速页面的渲染

    ```cpp
    <meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1, minimum-scale=1, maximum-scale=1">
    复制代码
    ```

*   **减少 DOM 节点**：DOM 节点太多影响页面的渲染，尽量减少 DOM 节点

*   **优化动画**

    *   尽量使用 CSS3 动画
    *   合理使用 requestAnimationFrame 动画代替 setTimeout
    *   适当使用 Canvas 动画：5 个元素以内使用`CSS 动画`，5 个元素以上使用`Canvas 动画`，`iOS8+`可使用`WebGL 动画`
*   **优化高频事件**：`scroll`、`touchmove`等事件可导致多次渲染

    *   函数节流
    *   函数防抖
    *   使用 requestAnimationFrame 监听帧变化：使得在正确的时间进行渲染
    *   增加响应变化的时间间隔：减少重绘次数
*   **GPU 加速**：使用某些 HTML5 标签和 CSS3 属性会触发`GPU 渲染`，请合理使用(**过渡使用会引发手机耗电量增加**)

    *   HTML 标签：`video`、`canvas`、`webgl`
    *   CSS 属性：`opacity`、`transform`、`transition`

**样式优化**

*   **避免在 HTML 中书写 style**
*   **避免 CSS 表达式**：CSS 表达式的执行需跳出 CSS 树的渲染
*   **移除 CSS 空规则**：CSS 空规则增加了 css 文件的大小，影响 CSS 树的执行
*   **正确使用`display：display`会影响页面的渲染**
    *   `display:inline`后不应该再使用`float`、`margin`、`padding`、`width`和`height`
    *   `display:inline-block`后不应该再使用`float`
    *   `display:block`后不应该再使用`vertical-align`
    *   `display:table-*`后不应该再使用`float`和`margin`
*   **不滥用 float**：`float`在渲染时计算量比较大，尽量减少使用
*   **不滥用 Web 字体**：Web 字体需要下载、解析、重绘当前页面，尽量减少使用
*   **不声明过多的 font-size**：过多的`font-size`影响 CSS 树的效率
*   **值为 0 时不需要任何单位**：为了浏览器的兼容性和性能，值为`0`时不要带单位
*   标准化各种浏览器前缀
    *   无前缀属性应放在最后
    *   CSS 动画属性只用-webkit-、无前缀两种
    *   其它前缀为-webkit-、-moz-、-ms-、无前缀四种：`Opera`改用`blink`内核，`-o-`已淘汰
*   **避免让选择符看起来像正则表达式**：高级选择符执行耗时长且不易读懂，避免使用

**脚本优化**

*   **减少重绘和回流**
    *   避免不必要的 DOM 操作
    *   避免使用 document.write
    *   减少 drawImage
    *   尽量改变 class 而不是 style，使用 classList 代替 className
*   **缓存 DOM 选择与计算**：每次 DOM 选择都要计算和缓存
*   **缓存.length 的值**：每次`.length`计算用一个变量保存值
*   **尽量使用事件代理**：避免批量绑定事件
*   **尽量使用 id 选择器**：`id`选择器选择元素是最快的
*   **touch 事件优化**：使用`tap`(`touchstart`和`touchend`)代替`click`(**注意`touch`响应过快，易引发误操作**)

**常用规则**

> **雅虎军规**

*   内容
    *   **Make Fewer HTTP Requests**：减少`HTTP`请求数
    *   **Reduce DNS Lookups**：减少`DNS`查询
    *   **Avoid Redirects**：避免重定向
    *   **Make Ajax Cacheable**：缓存`AJAX 请求`
    *   **Postload Components**：延迟加载资源
    *   **Preload Components**：预加载资源
    *   **Reduce The Number Of DOM Elements**：减少`DOM`元素数量
    *   **Split Components Across Domains**：跨域拆分资源
    *   **Minimize The Number Of Iframes**：减少`iframe`数量
    *   **No 404s**：消除`404`错误
*   样式
    *   **Put Stylesheets At The Top**：置顶样式
    *   **Avoid CSS Expressions**：避免`CSS`表达式
    *   **Choose Over @import**：选择``代替`@import`
    *   **Avoid Filters**：避免滤镜
*   脚本
    *   **Put Scripts At The Bottom**：置底脚本
    *   **Make JavaScript And CSS External**：使用外部`JS`和`CSS`
    *   **Minify JavaScript And CSS**：压缩`JS`和`CSS`
    *   **Remove Duplicate Scripts**：删除重复脚本
    *   **Minimize DOM Access**：减少`DOM`操作
    *   **Develop Smart Event Handlers**：开发高效的事件处理
*   图像
    *   **Optimize images**：优化图片
    *   **Optimize CSS Sprites**：优化`CSS 精灵图`
    *   **Don't Scale images In HTML**：不在`HTML`中缩放图片
    *   **Make Favicon.ico Small And Cacheable**：使用小体积可缓存的`favicon`
*   缓存
    *   **Reduce Cookie Size**：减少`Cookie`大小
    *   **Use Cookie-Free Domains For Components**：使用无`Cookie`域名的资源
*   移动端
    *   **Keep Components Under 25kb**：保持资源小于`25kb`
    *   **Pack Components Into A Multipart Document**：打包资源到多部分文档中
*   服务器
    *   **Use A Content Delivery Network**：使用`CDN`
    *   **Add An Expires Or A Cache-Control Header**：响应头添加`Expires`或`Cache-Control`
    *   **Gzip Components**：`Gzip`资源
    *   **Configure ETags**：配置`ETags`
    *   **Flush The Buffer Early**：尽早输出缓冲
    *   **Use Get For AJAX Requests**：`AJAX 请求`时使用`get`
    *   **Avoid Empty Image Src**：避免图片空链接

> **2-5-8 原则**

在前端开发中，此规则作为一种开发指导思路，针对浏览器页面的性能优化。

*   用户在`2 秒内`得到响应，会感觉页面的响应速度很快 Fast
*   用户在`2~5 秒间`得到响应，会感觉页面的响应速度还行 Medium
*   用户在`5~8 秒间`得到响应，会感觉页面的响应速度很慢，但还可以接受 Slow
*   用户在`8 秒后`仍然无法得到响应，会感觉页面的响应速度垃圾死了

#### 3.2 一个官网怎么优化，有哪些性能指标，如何量化

**参考答案**：

对首屏加载速度影响最大的还是资源大小，请求数量，请求速度等。代码方面，前端一般很难写出严重影响速度的代码。减小资源大小，可以用各种压缩，懒加载，预加载，异步加载等方法。减少请求数量可以使用雪碧图，搭建 node 中台将多个请求合并成一个等。对于官网这种项目，最好使用服务端渲染，首屏快之外，也有利于 SEO。

**检测方案**：

使用 lighthouse 进行性能检测，并对 lighthouse 提出的建议进行优化

**具体优化方案**：

通过静态化、图片懒加载、图片压缩、异步加载（js 和 css）、优化代码等方式，以下是具体方法

**静态化**

服务端渲染，“直出”页面，具有较好的 SEO 和首屏加载速度。主要还有以下的优点：

*   使用 jsp 模板语法（百度后发现是用 Velocity 模板语法）渲染页面，减少了 js 文件体积
*   减少了请求数量
*   因为不用等待大量接口返回，加快了首屏时间

可以尝试 Vue 的服务端渲染。首页目前有部分是用接口读取数据，然后用 jq 进行渲染，性能上应该不如 Virtual DOM，不过内容不多。

**图片懒加载**

这是一个很重要的优化项。因为官网上有很多图片，而且编辑们上传文章图片的时候一般没有压缩，但是很多图片的体积都很大。还有一个轮播图，20 张图标，最小的几十 K，最大的两百多 K。对于图片来源不可控的页面，懒加载是个很实用的操作，直接将首屏加载的资源大小加少了十几 M。

**图片压缩**

对于来源可控，小图标等图片可以用雪碧图，base64 等方法进行优化。目前只是用工具压缩了图片大小，后续可以考虑在 webpack 打包的时候生成雪碧图。

**异步加载 js**

通过标签引入的 js 文件，可以设置`defer`，`async`属性让其异步加载，而不会阻塞渲染。`defer`和`async`的区别在于`async`加载完就立即执行，没有考虑依赖，标签顺序等。而`defer`加载完后会等它前面引入的文件执行完再执行。一般`defer`用的比较多，`async`只能用在那些跟别的文件没有联系的孤儿脚本上。

**异步加载 css**

没想到 css 也能异步加载，但这是 lighthouse 给出的建议。找了一下发现有以下两种方法：
一是通过 js 脚本在文档中插入标签
二是通过``的`media`属性
`media 属性是媒体查询用的，用于在不同情况下加载不同的 css。这里是将其设置为一个不适配当前浏览器环境的值，甚至是不能识别的值，浏览器会认为这个样式文件优先级低，会在不阻塞的情况加载。加载完成后再将`media`设置为正常值，让浏览器解析 css。

```cpp
<link rel="stylesheet" href="//example.css" media="none" onload="this.media='all'">
```

这里用的是第二种方法。但是 webpack 注入到 html 中的外链 css 还没找到异步加载的方法。

**preconnent**

lighthouse 建议对于接下来会访问的地址可以提前建立连接。一般有一下几种方式。

**dns-prefetch**
域名预解析

```cpp
<link rel="dns-prefetch" href="//example.com">
```

**preconnet**
预连接

```cpp
<link rel="preconnect" href="//example.com">
<link rel="preconnect" href="//cdn.example.com" crossorigin>
```

**prefetch**
预加载

```cpp
<link rel="prefetch" href="//example.com/next-page.html" as="html" crossorigin="use-credentials">
<link rel="prefetch" href="library.js" as="script">
```

**prerender**
预渲染

```cpp
<link rel="prerender" href="//example.com/next-page.html">
```

这四种层层递进，但是不要连接不需要的资源，反而损耗性能。我在页面上对某些资源用了`preconnect`，但并没有明显的效果。应该对于在线小说，在线漫画这种场景预加载会更适用。

**代码优化**

lighthouse 上显示主线程耗时最多的是样式和布局，所以对这部分进行优化。主要有一下几点：

*   去掉页面上用于布局的 table，table 本身性能较低，且维护性差，是一种过时的布局方案。
*   在去掉 table 布局的同时减少一些无意义的 DOM 元素，减少 DOM 元素的数量和嵌套。
*   减少 css 选择器的嵌套。用 sass，less 这种 css 预处理器很容易造成多层嵌套。优化前代码里最多的有七八层嵌套，对性能有一定影响。重构后不超过三层。

通过上面的重构后，样式布局和渲染时间从 lighthouse 上看大概减少了 300ms。但样式和布局的时间还是最长的，感觉还有优化空间。

接下来是 js 代码的优化和重构。因为移除 Vue 框架，以及用服务端端直出，现在 js 代码已经减少了大部分。主要有以下几部分：

*   拆分函数，将功能复杂的函数拆分成小函数，让每个函数只做一件事。
*   优化分支结构，用对象`Object`，代替`if...else`和`switch...case`

如下面这段代码，优化后变得更加简洁，也便于维护。

```cpp
// 优化前
var getState = function (state) {
  switch (state) {
    case 1:
      return 'up';
    case 0:
      return 'stay';
    case 2:
      return 'down';
  }
}

// 优化后
var getState = function(state) {
  var stateMap = {
    1: 'up', 0: 'stay', 2: 'down'
  }
  return stateMap[state]
}
```

*   优化 DOM 操作

DOM 操作如改变样式，改变内容可能会引起页面的重绘重排，是比较消耗性能的。网上也有很多优化 jq 操作的方法。
如将查询到的 DOM 使用变量存起来，避免重复查询。以及将多次 DOM 操作变成一次等。这里重点讲一下第二种。
常见的需求是渲染一个列表，如果直接在 for 循环里面 append 到父元素中，性能是非常差的。幸好原来的操作是将所有 DOM 用字符串拼接起来，再用`html()`方法一次性添加到页面中。
还有另一种方法是使用文档碎片(`fragment`)。通过`document.createDocumentFragment()`可以新建一个 fragment。向`fragment`中`appendChild`元素的时候是不会阻塞渲染进程的。最后将`fragment`替换掉页面上的元素。将`fragment`元素用`appendChild`的方法添加到页面上时，实际上添加上去的是它内部的元素，也就是它的子元素。

```cpp
var fragment = document.createDocumentFragment()
for (var i = 0; i < data.length; i++) {
  var str = '<div>' + i + '</div>'
  fragment.appendChild($(str)[0])
}
$('.container').append(fragment)
```

经过测试，在当前的场景下，使用`fragment`的速度和`html()`是差不多的，都是 10ms 左右。区别在于最后将`fragment`添加到页面上`$('.container').append(fragment)`这行代码仅仅花费 1ms。也就是说，将`fragment`插入页面时不会引起页面重绘重排，不会引起阻塞。

#### 3.3 尾调用优化

**参考答案**：

尾调用是指某个函数的最后一步是调用另一个函数。

函数调用会在内存形成一个“调用记录”，又称“调用帧”，保存调用位置和内存变量等信息。如果在函数`A`的内部调用函数`B`，那么在`A`的调用帧上方，还会形成一个`B`的调用帧。等到`B`运行结束，将结果返回到`A`，`B`的调用帧才会消失。如果函数`B`内部还调用函数`C`，那就还有一个`C`的调用帧，依次类推。所有的调用帧，就形成一个“调用栈”。

**尾调用由于是函数的最后一步操作，所有不需要保留外层函数的调用帧，因为调用位置、内部变量等信息都不会再用到了，只要直接用内层函数的调用帧，取代外层函数的调用帧就可以了**。

如果所有函数都是尾调用，那么完全可以做到每次执行时，调用帧只有一项，这将大大节省内存。这就是“尾调用优化”。注意，只有不再用到外层函数的内部变量，内层函数的调用帧才会取代外层函数的调用帧，否则就无法进行“尾调用优化”。

**尾调用案例：**

```cpp
function addOne(a) {
    var one = 1;
    function inner(b) {
        return b + one;
    }
    return inner(a);
}
```