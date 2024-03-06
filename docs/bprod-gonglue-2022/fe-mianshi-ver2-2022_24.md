# 第六章 第 1 节 必考知识（上）

> 原文：[`www.nowcoder.com/tutorial/10091/755f9a3f3bdd47bf8d042b3b37e3d03c`](https://www.nowcoder.com/tutorial/10091/755f9a3f3bdd47bf8d042b3b37e3d03c)

## 【考点简介】

浏览器是页面的载体与根基，其相关知识是前端开发岗的必备基础，也是笔试面试的必考部分，本章将重点分析浏览器相关的高频考点，主要知识点如下图所示![](img/d787fe037916e0cc5291fc11ab4f6cb6.png)

## 【例题示例】

#### 1.1 请问什么是同源策略？

【考点映射】

*   浏览器的同源策略

【频率】★★★★★（前端岗必考题）

【难度】☆

【参考答案】

源：就是协议、域名和端口号

同源策略：SOP（Same origin policy）是由 Netscape 公司 1995 年引入浏览器的一种约定，是浏览器最核心、最基本的安全功能，若缺少了同源策略，浏览器很容易受到 XSS、CSFR 等攻击。所谓同源是指"协议+域名+端口"三者相同，若两个 URL 的协议、域名、端口号都相同，则两者为同源，有一个不同则非同源，即便两个不同的域名指向同一个 ip 地址，也是非同源的

非同源的 URL 在没有明确授权的情况下，不能读写对方资源（不能相互通信）

举一些实际例子，判断各 URL 是否符合同源策略：

| URL | 是否同源 | 是否允许通信 |
| [`www.domain.com/a.js`](http://www.domain.com/a.js) | 同源，不同文件或路径 | 允许 |
| [`www.domain.com/b.js`](http://www.domain.com/b.js)  |
| [`www.domain.com:8000/a.js`](http://www.domain.com:8000/a.js) | 非同源，不同端口 | 不允许 |
| [`www.domain.com/b.js`](http://www.domain.com/b.js) |
| [`www.domain.com/a.js`](http://www.domain.com/a.js) | 非同源，不同协议 | 不允许 |
| [`www.domain.com/b.js`](https://www.domain.com/b.js)  |
| [`www.domain.com/a.js`](http://www.domain.com/a.js) | 非同源，不同域名 | 不允许 |
| http://192.168.4.12/b.js |
| [`www.domain.com/a.js`](http://www.domain.com/a.js) | 非同源，不同域名 | 不允许 |
| [`x.domain.com/b.js`](http://x.domain.com/b.js)  |
| [`domain.com/c.js`](http://domain.com/c.js) |
| [`www.domain1.com/a.js`](http://www.domain1.com/a.js) | 非同源，不同域名 | 不允许 |
| [`www.domain2.com/b.js`](http://www.domain2.com/b.js)  |

同源策略将限制以下几种行为：

（1）Cookie、LocalStorage 和 IndexDB 无法读取

（2）DOM 和 Js 对象无法获得

（3）AJAX 请求不能发送

以下两种不受同源策略的限制：

（1）页面中的链接，重定向以及表单提交是不会受到同源策略限制

（2）跨域资源的引入，但是 js 不能读写加载的内容，如嵌入到页面中的<script src="..."></script>，<img>，<link>，<iframe>等

注意：

同源策略是浏览器做的限制，对服务器与服务器之间的通信不做限制

当 A 与 B 非同源时，从域名 A 下的一个页面获取域名 B 下的一个资源，是不被浏览器允许的， 浏览器还是会发出这个请求，但是它会拦截响应内容（因为非同源，不安全）（请求是页面发出的，页面在浏览器中显示，故发出的请求是通过浏览器代为执行）

#### 1.2 请问什么是跨域？有哪些常用的跨域方式？

【考点映射】

*   常见跨域方式

【频率】★★★★★（前端岗必考题）

【难度】☆☆

【参考答案】

受浏览器同源策略的限制，非同源的两个 URL 间不能通信，非同源的脚本不能操作其他源下面的对象，但在实际开发场景中， 这种情况经常出现， 此时需要通过跨域进行非同源通信

常见跨域解决方式：

（1）通过 jsonp 跨域

注意：jsonp 和 json 并没有什么关系

原理：利用<script>标签进行跨域 只能实现 GET 请求

举例：a.com/jsonp.html 想得到 b.com/main.js 中的数据

在 a.com 的 jsonp.html 里创建一个回调函数，动态添加<script>元素，向服务器发送请求，请求地址后面加上查询字符串，通过 callback 参数指定回调函数的名字，请求地址为：[`b.com/main.js?callback=xxx`](http://b.com/main.js?callback=xxx)，在 main.js 中调用这个回调函数 xxx，并且以 JSON 数据形式作为参数传递，完成回调，我们来看看代码：

```cpp
function addScriptTag(src) { 
    var script = document.createElement('script');
    script.setAttribute("type","text/javascript");  
    script.src = src;   
    document.body.appendChild(script); 
}  
window.onload = function () {
    addScriptTag('http://b.com/main.js?callback=foo');
}  //window.onload 是为了让页面加载完成后再执行
function foo(data) {
    console.log(data.name+"欢迎您"); 
};

```

**（2）nginx 反向代理跨域**

**原理：** 同源策略是浏览器的安全策略，不是 HTTP 协议的一部分，服务器端调用 HTTP 接口只是使用 HTTP 协议，不会执行 JS 脚本，不需要同源策略，也就不存在跨越问题。

**实现：**通过 nginx 配置一个代理服务器（域名与 domain1 相同，端口不同）做跳板机，反向代理访问 domain2 接口，并且可以顺便修改 cookie 中 domain 信息，方便当前域 cookie 写入，实现跨域登录

**（3） 跨域资源共享（CORS）**

**普通跨域请求：**只服务端设置 Access-Control-Allow-Origin 即可，前端无须设置

**带 cookie 请求**：前后端都需要设置。

```cpp
// 前端设置是否带 cookie 
xhr.withCredentials = true

```

注意：由于同源策略的限制，所读取的 cookie 为跨域请求接口所在域的 cookie，而非当前页

**（4）WebSocket 协议跨域**

WebSocket 是 HTML5 一种新的协议，实现了浏览器与服务器全双工通信，同时允许跨域通讯，是 server push 技术的一种很好的实现（具体原理分析详见第三章-1.1-延伸考点 4）

**（5）document.domain + iframe 跨域**

**注意：**仅限主域相同，子域不同的跨域应用场景

**原理：**两个页面都通过 js 强制设置 document.domain 为基础主域，就实现了同域

**（6）location.hash + iframe**

**原理：** a 与 b 跨域相互通信，通过中间页 c 来实现， 三个页面，不同域之间利用 iframe 的 location.hash 传值，相同域之间直接 js 访问来通信

**A 域：**a.html -> B 域：b.html -> A 域：c.html

a 与 b 不同域只能通过 hash 值单向通信，b 与 c 也不同域也只能单向通信，但 c 与 a 同域，所以 c 可通过 parent.parent 访问 a 页面所有对象

```cpp
//a.html
<iframe id="iframe" src="http://www.domain2.com/b.html" style="display:none;"></iframe>
<script>
    var iframe = document.getElementById('iframe');

    // 向 b.html 传 hash 值
    setTimeout(function() {
        iframe.src = iframe.src + '#user=admin';
    }, 1000);

    // 开放给同域 c.html 的回调方法
    function onCallback(res) {
        alert('data from c.html ---> ' + res);
    }
</script>

```

```cpp
//b.html
<iframe id="iframe" src="http://www.domain1.com/c.html" style="display:none;"></iframe>
<script>
    var iframe = document.getElementById('iframe');
    // 监听 a.html 传来的 hash 值，再传给 c.html
    window.onhashchange = function () {
        iframe.src = iframe.src + location.hash;
    };
</script>

```

```cpp
//c.html
<script>
    // 监听 b.html 传来的 hash 值
    window.onhashchange = function () {
        // 再通过操作同域 a.html 的 js 回调，将结果传回
        window.parent.parent.onCallback('hello: ' + location.hash.replace('#user=', ''));
    };
</script>

```

**（7）window.name + iframe 跨域**

window.name 属性的独特之处：name 值在不同的页面（甚至不同域名）加载后依旧存在，并且可以支持非常长的 name 值（2MB）

通过 iframe 的 src 属性由外域转向本地域，跨域数据即由 iframe 的 window.name 从外域传递到本地域，可以巧妙地绕过了浏览器的跨域访问限制，同时又是安全操作

**（8）postMessage 跨域**

postMessage 是 HTML5 XMLHttpRequest Level 2 中的 API，且是为数不多可以跨域操作的 window 属性之一，它可解决以下问题：

a.页面和其打开的新窗口的数据传递

b.多窗口之间消息传递

c.页面与嵌套的 iframe 消息传递

d.上面三个场景的跨域数据传递

用法：postMessage(data，origi)

data：html5 规范支持任意基本类型或可复制的对象，但部分浏览器只支持字符串，所以传参时最好用 JSON.stringify()序列化

origin：协议+主机+端口号，也可以设置为"*"，表示可以传递给任意窗口，如果要指定和当前窗口同源的话设置为"/"

例如：从 a.html ([`www.domain1.com/a.html`](http://www.domain1.com/a.html)) ->               b.html([`www.domain2.com/b.html`](http://www.domain2.com/b.html)) 

```cpp
//a.html
<iframe id="iframe" src="http://www.domain2.com/b.html" style="display:none;"></iframe>
<script>       
    var iframe = document.getElementById('iframe');
    iframe.onload = function() {
        var data = {
            name: 'aym'
        };
        iframe.contentWindow.postMessage(JSON.stringify(data), 'http://www.domain2.com');   // 向 domain2 传送跨域数据
    };
    window.addEventListener('message', function(e) {
        alert('data from domain2 ---> ' + e.data);
    }, false);     // 接受 domain2 返回数据
</script>

```

```cpp
//b.html
<script>
    window.addEventListener('message', function(e) {             alert('data from domain1 ---> ' + e.data);  //接收 domain1 的数据 
    var data = JSON.parse(e.data);         
    if (data) {             
    data.number = 16;             
    window.parent.postMessage(JSON.stringify(data), 'http://www.domain1.com');     // 处理后再发回 domain1
    }     
}, false);
 </script>

```

#### 1.3 请问你了解浏览器的渲染机制？

【考点映射】

*   浏览器的渲染机制

【频率】★★★★★（前端岗必考题）

【难度】☆☆

【参考答案】

首先，浏览器的渲染机制中有几个基本概念名称：

（1）、DOM：浏览器将 HTML 解析成树形的数据结构

（2）、CSSOM：浏览器将 CSS 解析成树形的数据结构

（3）、Render Tree：DOM 和 CSSOM 合并后生成 Render Tree（渲染树）

（4）、Layout：计算出 Render Tree 每个节点的具***置

（5）、Painting：通过显卡，将 Layout 后的节点内容分别呈现到屏幕上

浏览器整个渲染流程如下：

（1）、用户输入一个 URL 后，浏览器就会向服务器发出一个请求，请求 URL 对应的资源

（2）、接到服务器的响应内容后，浏览器的 HTML 解析器，会将 HTML 文件解析成一颗 DOM 树，DOM 树的构建是一个深度遍历的过程，当前节点的所有子节点都构建完成后，才会去构建当前节点的下一个兄弟节点

（3）、将 CSS 解析成 CSSOM 树

（4）、根据 DOM 树与 CSSOM 树，构建 Render Tree

（5）、浏览器会根据 Render Tree 能知道网页中哪些有节点，各个节点的 CSS，以及各个节点的从属关系

（6）、计算出每个节点在屏幕中的位置后，最后一步就是 Painting，根据计算出的规则，把内容画到屏幕上

![](img/37f60f83eddb82b7f6be08f52876d598.png)

注意：

浏览器在获得 HTML 文件后，是自上而下的加载，并在加载过程中进行解析与渲染

加载过程中：

遇到外部 CSS 文件和图片等静态资源时，浏览器会另外发送一个异步请求

遇到 js 文件时，HTML 文件会挂起渲染的进程，等待 js 文件加载完毕后，再继续进行渲染，因为 js 可能会修改 DOM，导致后续 HTML 资源白白加载，这也是为什么建议将 js 文件写在底部 body 标签前

#### 1.4 请问如何解决浏览器加载 js 时的阻塞问题吗？

【考点映射】

*   解决加载 js 阻塞

【频率】★★★★★

【难度】☆

【参考答案】

**（1）、推迟加载**

若页面初始的渲染并不依赖于 js 或 CSS 可以用推迟加载，最后在加载 js 和 CSS，把引用外部文件的代码写在最后

**（2）、defer 延迟加载**

```cpp
<script src="" defer></script>

```

在文档解析完成开始执行，并且在 DOMContentLoaded 事件之前执行完成，会按照在文档出现的顺序去下载解析，效果和把 script 放在文档最后</body>之前是一样

        注意：defer 最好用在引用外部文件中使用，用了 defer 不要使用 document.write()方法，使用 defer 时最好不要请求样式信息，因为样式表可能尚未加载，浏览器会禁止该脚本等待样式表加载完成，相当于样式表阻塞脚本执行

**（3）、异步加载**

async 异步加载：就是告诉浏览器不必等到加载完外部文件，可以边渲染边下载，什么时候下载完成什么时候执行

 ```cpp
 <script type="text/javascript" src="a.js" async></script>

```

**script dom element：**用 js 动态创建一个 script 元素添加在 document 中

注意：会阻止 onload 事件

```cpp
<script type="text/javascript">
    (function() {
     var s = document.createElement('script');
     s.type = 'text/javascript';
     s.async = true;                   //这句可以删除，但是效果不变。
     s.src = 'js/a.js';
     var x = document.getElementsByTagName('script')[0];
     x.parentNode.insertBefore(s, x);
 })();
</script>

```

**onload 时异步加载：**与 script dom element 法差不多，但不是同时执行 js 和 html，是等 html 的文件、图片之类的、页面所有的资源全部加载完成后再下载执行 js，这样的方法可以避免阻塞 onload 事件的触发

**注意：**DOMContentLoaded 与 onload 事件不同，DOMContentLoaded 是页面解析完成，页面的 dom 元素可以使用，但是页面的图片、视频等资源可能还没加载完成

#### 1.5 请问你了解什么是重绘？什么是回流？两者有何区别？

【考点映射】

*   重绘、回流、两者区别

【频率】★★★★★

【难度】☆

【参考答案】

**重绘：**

更换某个元素颜色，这样的行为是不影响页面布局，DOM 树不会变化，但颜色变了，使该元素所在的 Layer 重新渲染

常见情况：

（1）、回流必定引发重绘，但重绘也会单独触发

（2）、背景色、颜色、字体改变（字体大小改变，会触发回流）

**回流：**

增删 DOM 节点，或修改一个元素的宽高，页面布局发生变化，DOM 树结构发生变化，需要重新构建 DOM 树，而 DOM 树与渲染树是紧密相连的，DOM 树构建完，渲染树也会随之对页面进行再次渲染

**常见情况：**

（1）、页面渲染初始化

（2）、DOM 树变化（添加或者删除可见的 DOM 元素、元素位置改变）

（3）、Render 树变化（元素尺寸改变：边距、填充、边框、宽度和高度）

（4）、浏览器窗口尺寸改变，resize 事件发生

（5）、内容改变：文本改变或者图片大小改变而引起的计算值宽度和高度改变；

（6）、查询布局信息，包括 offestLeft/Top/Width/Height、scrollLeft/Top/Width/Height、clientLeft/Top/Width/Height、浏览为了返回最新值，会触发回流