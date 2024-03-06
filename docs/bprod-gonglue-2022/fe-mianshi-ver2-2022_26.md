# 第六章 第 3 节 必考知识（下）

> 原文：[`www.nowcoder.com/tutorial/10091/4892ebc68e7f4172b7ad34b7db96c78c`](https://www.nowcoder.com/tutorial/10091/4892ebc68e7f4172b7ad34b7db96c78c)

### 3 必考知识（下）

#### 3.1 请谈谈你对浏览器的理解？

【考点映射】

*   浏览器的功能、组成

【频率】★★★★

【难度】☆

【参考答案】

浏览器的主要功能是将用户选择的 web 资源呈现出来，它需要从服务器请求资源，并将其显示在浏览器窗口中，资源的格式通常是 HTML，也包括 PDF、image 及其他格式

HTML 和 CSS 规范中规定了浏览器解释 html 文档的方式，由 W3C 组织对这些规范进行维护，但各浏览器厂商纷纷开发自己的扩展功能，对规范遵循并不完善，这便导致了严重的兼容性问题

简单来说浏览器可以分为两部分，shell 和 内核。其中 shell 的种类相对比较多，内核则比较少

**shell** 是指浏览器外壳：例如菜单，工具栏等，主要是提供给用户界面操作，参数设置，是调用内核来实现各种功能的

**内核** 是浏览器的核心，是基于标记语言显示内容的程序或模块

#### 3.2 请谈谈你对浏览器内核的理解？

【考点映射】

*   浏览器内核

【频率】★★★★★

【难度】☆

【参考答案】

浏览器内核主要分成两部分：

**渲染引擎：**其职责就是渲染，即在浏览器窗口中显示所请求的内容。默认情况下，渲染引擎可以显示 html、xml 文档及图片，它也可以借助插件显示其他类型数据，例如使用 PDF 阅读器插件显示 PDF 格式

**JS 引擎：**解析和执行 javascript 来实现网页的动态效果

起初渲染引擎和 JS 引擎区分并不明确，而后 JS 引擎越来越独立，内核就倾向于只指渲染引擎

**目前常见的浏览器内核：**

**IE 浏览器：**Trident 内核，也俗称 IE 内核

**Chrome 浏览器：**统称为 Chromium 内核或 Chrome 内核，以前是 Webkit 内核，现在是 Blink 内核

F**irefox 浏览器：**Gecko 内核，俗称 Firefox 内核

**Safari 浏览器：**Webkit 内核

**Opera 浏览器：**最初是自家公司的 Presto 内核，后来从 Webkit 又转到了 Blink 内核

**360 浏览器、猎豹浏览器内核：**IE + Chrome 双内核

**搜狗、遨游、QQ 浏览器内核：**Trident（兼容模式）+ Webkit（高速模式）

#### 3.3 请问你了解 ajax 请求吗？

【考点映射】

*   ajax 请求

【频率】★★★★★

【难度】☆

【参考答案】

传统请求方法的缺点:

传统的 web 请求是用户触发一个 http 请求服务器，服务器收到后，响应到用户，并返回一个新的页面，每当服务器处理客户端请求时，客户都只能空闲等待，哪怕只是一次很小的交互，都要返回一个完整的 HTML 页，并且浪费时间和带宽去重新读取整个页面，这导致了用户界面的响应比本地应用慢得多

ajax 是一种用于创建快速***页的技术，通过在后台与服务器进行少量数据交换，可使网页实现异步更新，在不重新加载整个网页的情况下，对网页的某部分进行更新

ajax 请求步骤：

a、创建 XMLHTTPRequest 对象（通过 XMLHttpRequest 可以在不刷新页面的情况下请求特定 URL，获取数据，常用于 ajax 请求）

b、使用 open 方法设置和服务器的交互信息

c、设置发送的数据，开始和服务器端交互

d、注册事件

e、更新界面

#### 3.4 请问 ajax 是如何发请求的？

【考点映射】

*   ajax 请求过程/原理

【频率】★★★★★

【难度】☆☆

【参考答案】

get 请求：

```cpp
//一、创建 XMLHTTPRequest 对象
var ajax = new XMLHttpRequest();
//二、设置请求 url 参数，（请求的类型，请求的 url，可以带参数，动态的传递参数 starName 到服务端）
ajax.open('get','getStar.php?starName='+name);
//三、发送请求
ajax.send();
//四、注册事件 onreadystatechange 状态改变就会调用
ajax.onreadystatechange = function () {
    if (ajax.readyState==4 &&ajax.status==200) {
//五、if 条件为 true，数据请求成功，并且请求的页面是存在的　　　　           
    console.log(ajax.responseText);
    }
}

```

关于 if 判断语句中的状态值：

**status：**服务器的 Http 状态码，若是 200，则表示 OK，404，表示为未找到

**readyState 属性**有五个状态值：

0：uninitialized：未初始化，已经创建了 XMLHttpRequest 对象但是未初始化

1：loading：已经开始准备好要发送了

2：loaded,：已经发送，但是还没有收到响应

3：interactive：正在接受响应，但是还没接收完

4：completed：接受响应完毕

**post 请求：**

```cpp
//一、创建 XMLHTTPRequest 对象
var ajax = new XMLHttpRequest();
//二、设置请求的类型及 url
//post 请求一定要添加请求头才行不然会报错
ajax.setRequestHeader("Content-type","application/x-www-form-urlencoded");
ajax.open('post', '02.post.php' );
//三、发送请求
ajax.send('name=fox&age=18');
ajax.onreadystatechange = function () {
// 判断服务器是否正确响应
    if (ajax.readyState == 4 && ajax.status == 200) {
        console.log(ajax.responseText);
    } 
};

```

可进行一定的封装：

```cpp
function ajax_method(url,data,method,success) {
    //创建 XMLHTTPRequest 对象
    var ajax = new XMLHttpRequest();
    // get 请求
    if (method=='get') {
        if (data) {
            // 如果有值
            url+='?';
            url+=data;
        }else{
        }
        // 设置请求方法以及 url
        ajax.open(method,url);
        // 发送请求
        ajax.send();
    }else{
        // post 请求，url 是不需要改变
        ajax.open(method,url);
        // 需要设置请求报文
        ajax.setRequestHeader("Content-type","application/x-www-form-urlencoded");
        // 判断 data，send 发送数据
        if (data) {
            // 如有值从 send 发送
            ajax.send(data);
        }else{
            //直接发送
            ajax.send();
        }
    }
    // 注册事件
    ajax.onreadystatechange = function () {
        // 在事件中获取数据，并修改显示界面
        if (ajax.readyState==4 && ajax.status==200) {
            // console.log(ajax.responseText);
            // 将数据让外面可以使用
            // return ajax.responseText;
            // 当 onreadystatechange 被调用时，证明数据请求成功
            // ajax.responseText;
            // 可外面传入一个 function 作为参数 success
            success(ajax.responseText);
        }
    }
}

```

Promise 版本的 ajax 请求：

```cpp
var getJSON = function(url) {
    var promise = new Promise(function(resolve, reject {
        var client = new XMLHttpRequest();
        client.open("GET", url);
        client.onreadystatechange = handler;
        client.responseType = "json";
        client.setRequestHeader("Accept", "application/json");
        client.send();

        function handler() {
            if (this.readyState !== 4) {
                return;      
            }
            if (this.status === 200) {
                resolve(this.response);      
            } else {
                reject(new Error(this.statusText));      
            }    
        };  
    });
    return promise;
};  

getJSON("http://rap.taobao.org/mockjs/9768/Rap/get").then(function(response) {
    console.log('Contents: ',response);
}, function(error) {
    console.error('出错了', error);
});

```

#### 3.5 请问 ajax、axios、fetch 请求有何区别？

【考点映射】

*   ajax/axios/fetchfetch 请求区别

【频率】★★★★★

【难度】☆☆

【参考答案】

*   **axios**

一个基于 Promise 用于浏览器和 nodejs 的 HTTP 客户端，本质上也是对原生 XMLHTTPRequest 对象的封装，只不过它是 Promise 的实现版本，符合最新的 ES 规范，具有以下特征：

a、从浏览器中创建 XMLHttpRequest

b、支持 Promise API

c、客户端防止 CSRF

d、提供了一些并发请求的接口（实际开发中方便了很多的操作）

e、从 node.js 创建 http 请求

f、拦截请求和响应

g、转换请求和响应数据

h、支持取消请求

i、自动转换 JSON 数据

*   **fetch**

号称是 ajax 替代品，在 ES6 时产生，是基于 promise 设计的。相比 ajax，fetch 代码结构更加简单，参数类型与 ajax 类似，但 fetch 不是 ajax 的进一步封装，而是原生 js，没有使用 XMLHttpRequest 对象

**fetch 优点：**

a、符合关注分离，没有将输入、输出和用事件来跟踪的状态混杂在一个对象里

b、写法更加简洁方便

### 3.6 总结

本章主要针对浏览器相关高频考点进行总结与分析，对每个考点进行原理阐述，并提供了一些代码示例，在日常开发中，浏览器涉及到的技术种类繁多，因篇幅有限，本章仅针对常见考点进行解析，所提供的参考答案仅是个人所学与各类参考书所得，若存在欠缺与不妥的地方，欢迎大家及时在评论区指出

下一章将对计算机网络相关考点进行总结与分析，希望大家能在通过本专刊更好地掌握此方面的知识与考点！