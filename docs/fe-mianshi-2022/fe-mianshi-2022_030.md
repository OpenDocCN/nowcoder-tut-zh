# 第五章 第 11 节 前端基础-文件引入方式

> 原文：[`www.nowcoder.com/tutorial/10072/70ff27b911dd45259abed6eea5b9c038`](https://www.nowcoder.com/tutorial/10072/70ff27b911dd45259abed6eea5b9c038)

#### 5.1 link 和 @import

**参考答案：**

作用：样式的导入方式

link 的使用

```cpp
<link href="index.css" rel="stylesheet">
```

@import 的使用

```cpp
<style type="text/css">
@import url(index.css);
</style>
```

link 和 @import 的区别

1.  引入的内容不同

    link 除了引用样式文件，还可以引用图片等资源文件，而 @import 只引用样式文件

2.  加载顺序不同

    link 引用 CSS 时，在页面载入时同时加载；@import 需要页面网页完全载入以后加载

3.  兼容性不同

    link 是 XHTML 标签，无兼容问题；@import 是在 CSS2.1 提出的，低版本的浏览器不支持

4.  对 JS 的支持不同

    link 支持使用 Javascript 控制 DOM 去改变样式；而 @import 不支持

#### 5.2 为什么 link 用 href 获取资源 script 和 img 用 src

**参考答案：**

src 用于替换当前元素，href 用于在当前文档和引用资源之间确立联系。

src

*   src 是 source 的缩写，指向外部资源的位置，指向的内容将会嵌入到文档中当前标签所在位置；在请求 src 资源时会将其指向的资源下载并应用到文档内，例如 js 脚本，img 图片和 frame 等元素

    ```cpp
    <script src ="js.js"></script> 
    ```

​ 当浏览器解析到该元素时，**会暂停其他资源的下载和处理**，直到将该资源加载、编译、执行完毕，图片和框架 等元素也如此，类似于将所指向资源嵌入当前标签内。**这也是为什么将 js 脚本放在底部而不是头部**

href

*   `href`是`Hypertext Reference`的缩写，指向网络资源所在位置，建立和当前元素（锚点）或当前文档（链接）之间的链接

*   在文档中添加`link`标签，浏览器会识别该文档为`css`文件，就会并行下载资源并且**不会**停止对当前文档的处理。这也是为什么建议使用`link`方式来加载`css`，而不是使用`@import`方式

    ```cpp
    <link href="common.css" rel="stylesheet"/>
    ```