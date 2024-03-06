# 第六章 第 4 节 前端进阶-浏览器 4

> 原文：[`www.nowcoder.com/tutorial/10072/271149f01c4544a9912d9361a48fd238`](https://www.nowcoder.com/tutorial/10072/271149f01c4544a9912d9361a48fd238)

#### 1.14 token 能放在 cookie 中吗

**参考答案**：

能

**解析**：

*   token 是在客户端频繁向服务端请求数据，服务端频繁的去数据库查询用户名和密码并进行对比，判断用户名和密码正确与否，并作出相应提示，在这样的背景下，token 便应运而生。
*   **「简单 token 的组成」**:uid(用户唯一的身份标识)、time(当前时间的时间戳)、sign（签名，token 的前几位以哈希算法压缩成的一定长度的十六进制字符串）

**token 认证流程**

![img](img/8cd63fc961a4ca9067b9f86c78b5dd77.png)

1.  客户端使用用户名跟密码请求登录
2.  服务端收到请求，去验证用户名与密码
3.  验证成功后，服务端签发一个 token ，并把它发送给客户端
4.  客户端接收 token 以后会把它存储起来，比如放在 cookie 里或者 localStorage 里
5.  客户端每次发送请求时都需要带着服务端签发的 token（把 token 放到 HTTP 的 Header 里）
6.  服务端收到请求后，需要验证请求里带有的 token ，如验证成功则返回对应的数据

#### 1.15 js 如何获取/禁用 cookie

**参考答案**：

**js 如何获取 cookie**

假设 cookie 中存储的内容为：name=jack;password=123

则在 B 页面中获取变量 username 的值的 JS 代码如下：

```cpp
arusername=document.cookie.split(";")[0].split("=")[1];

//JS 操作 cookies 方法!

//写 cookies

function setCookie(name,value){

var Days = 30;

var exp =newDate();

exp.setTime(exp.getTime() + Days*24*60*60*1000);

document.cookie = name +"="+ escape (value) +";expires="+ exp.toGMTString();

}

//读取 cookies

function getCookie(name){

var arr,reg=new RegExp("(^| )"+name+"=([^;]*)(;|$)");

if(arr=document.cookie.match(reg))

return unescape(arr[2]);

else

return null;

}
```

#### 1.16 cookie

**参考答案**：

**1\. cookie 是什么？** 　

*   cookie 是存储于访问者计算机中的变量。每当一台计算机通过浏览器来访问某个页面时，那么就可以通过 JavaScript 来创建和读取 cookie。
*   实际上 cookie 是存于用户硬盘的一个文件，这个文件通常对应于一个域名，当浏览器再次访问这个域名时，便使这个 cookie 可用。因此，cookie 可以跨越一个域名下的多个网页，但不能跨越多个域名使用。
*   不同浏览器对 cookie 的实现也不一样。即保存在一个浏览器中的 cookie 到另外一个浏览器是 不能获取的。

PS：cookie 和 session 都能保存计算机中的变量，但是 session 是运行在服务器端的，而客户端我们只能通过 cookie 来读取和创建变量

**2\. cookie 能做什么？**

*   用户在第一次登录某个网站时，要输入用户名密码，如果觉得很麻烦，下次登录时不想输入了，那么就在第一次登录时将登录信息存放在 cookie 中。下次登录时我们就可以直接获取 cookie 中的用户名密码来进行登录。

    PS:虽然 浏览器将信息保存在 cookie 中是加密了，但是可能还是会造成不安全的信息泄露

*   类似于购物车性质的功能，第一次用户将某些商品放入购物车了，但是临时有事，将电脑关闭了，下次再次进入此网站，我们可以通过读取 cookie 中的信息，恢复购物车中的物品。

    PS：实际操作中，这种方法很少用了，基本上都是将这些信息存储在数据库中。然后通过查询数据库的信息来恢复购物车里的物品

*   页面之间的传值。在实际开发中，我们往往会通过一个页面跳转到另外一个页面。后端服务器我们可以通过数据库，session 等来传递页面所需要的值。但是在浏览器端，我们可以将数据保存在 cookie 中，然后在另外页面再去获取 cookie 中的数据。

    PS：这里要注意 cookie 的时效性，不然会造成获取 cookie 中数据的混乱。

**3\. 怎么使用 cookie？**

*   语法

    **document.cookie = "name=value;expires=evalue; path=pvalue; domain=dvalue; secure;”**

*   对各个参数的解释

    1.  name=value 必选参数

　　　　这是一个键值对，分别表示要存入的 属性 和 值。

　　　　比如：

```cpp
document.cookie="name=中文";
//为了防止中文乱码，我们可以使用 encodeURIComponent()编码；decodeURIComponent()解码
document.cookie = encodeURIComponent("name")+"="+encodeURIComponent("中文");
```

​ 2\. expires=evalue 可选参数

　　　　该对象的有效时间（可选）只支持 GTM 标准时间，即要将时间转换，toUTCString()（默认为当前浏览器 会话有用，关闭浏览器就消失）;

　　　　比如：　　

```cpp
var date = new Date(); 　
date.setTime(date.getTime()+2000);//获取当前时间并加上 2 秒钟 　
alert(date.toUTCString());//格林威治时间 (GMT) 把 Date 对象转换为字符串，并返回结果
alert(date.toGMTString());//与上面的结果一样，但是这个方法已经被上面取代了 
document.cookie="name=vae;expires="+date.toUTCString(); 
alert(document.cookie); // name=vae 　
setTimeout(function(){alert(document.cookie)},4000);//4 秒后打印空的字符串
```

​ 3\. path=pvalue 可选参数

　　　　限制访问 cookie 的目录，默认情况下对于当前网页所在的同一目录下的所有页面有效

​ 4.domain=dvalue 可选参数

　　　　用于限制只有设置了的域名才可以访问；如果没有设置，则默认在当前域名访问

　　　　比如设置 test*.com 表示域名为 test*.com 的服务器共享该 Cookie

​ 5.secure=true|false 可选参数，默认是 true 不安全传输

​ 安全设置，指明必须通过 安全的通信通道来传输（https) 才能获得 cookie,true 不安全，默认值；false 安 全，必须通过 https 来访问

　　　　比如：如果你设置 document.cookie = "name=vae;secure"

　　　　　　　上面的代码如果是在 http 的协议中访问，那么是访问不了的

```cpp
　　　　　　　//设置 cookie
            function setCookie(objName, objValue, objHours){//添加 cookie
                var str = objName + "=" + encodeURIComponent(objValue);
                if (objHours > 0) {//为 0 时不设定过期时间，浏览器关闭时 cookie 自动消失
                    var date = new Date();
                    var ms = objHours * 3600 * 1000;
                    date.setTime(date.getTime() + ms);
                    str += "; expires=" + date.toUTCString();
                }
                document.cookie = str;

            }
            //获取 cookie
            function getCookie(objName){//获取指定名称的 cookie 的值
                //多个 cookie 保存的时候是以 ;空格  分开的
                var arrStr = document.cookie.split("; ");
                for (var i = 0; i < arrStr.length; i++) {
                    var temp = arrStr[i].split("=");
                    if (temp[0] == objName){
                        return decodeURIComponent(temp[1]);
                    }else{
                        return "";
                    }

                }
            }

            //为了删除指定名称的 cookie，可以将其过期时间设定为一个过去的时间
            function delCookie(name){
                var date = new Date();
                date.setTime(date.getTime() - 10000);
                document.cookie = name + "=a; expires=" + date.toUTCString();
            }
```

注意：

（1）cookie 可能被禁用。当用户非常注重个人隐私保护时，他很可能禁用浏览器的 cookie 功能；

（2）cookie 是与浏览器相关的。这意味着即使访问的是同一个页面，不同浏览器之间所保存的 cookie 也是不能互相访问的；

（3）cookie 可能被删除。因为每个 cookie 都是硬盘上的一个文件，因此很有可能被用户删除； （4）cookie 安全性不够高。所有的 cookie 都是以纯文本的形式记录于文件中，因此如果要保存用户名密码等信息时，最好事先经过加密处理。

（5）cookie 在保存时，只要后面保存的 name 相同，那么就会覆盖前面的 cookie，注意是完全覆盖，包括失效时间，pat

#### 1.17 cookie 禁用

**参考答案**：

**问题描述：**

sessionID 通过 cookie 保存在客户端，如果将 cookie 禁用，必将对 session 的使用造成一定的影响。

**解决这个问题的办法是**：URL 重写

**URL 重写**

1.  servlet 中涉及向客户端输出页面元素的时候，可以在相应的请求地址外面包上一层方法，也就是说使用 response.encodeURL(“请求地址”);为请求地址添加一个 JSESSIONID 的值
2.  servlet 中涉及跳转到新的页面时，可以使用 response.encodeRedirectURL(“请求地址”);为请求地址添加一个 JSESSIONID 的值

#### 1.18 调试工具

**参考答案**：

**谷歌浏览器自带的调试工具：**

1.  Elements：可查看网页页面代码（修改只是当前使用有效），也可实时调试修改页面 ccs 代码样式。
2.  console：记录开发者开发过程中的日志信息，也可在里面写 js 代码。一般页面运行时 js 报错都是可以在这里看到反馈和定位 bug 原因及其位置。
3.  Sources：断点调试 JS，可以查看程序代码执行的过程，断点调试对于每一个程序员来说可是很重要。
4.  Network：从发起网页页面请求开始，分析 HTTP 请求后得到的各个请求资源信息（“小编有时候就利用这下载一些网站不给下载的在线视频，比如教学视频”）。
5.  Timeline：记录并分析网站的生命周期所发生的各类事件，分析渲染 js 执行的每一个阶段。
6.  Application：记录网站加载的各个资源信息。
7.  Security：判断网页是否安全。
8.  Audits：对当前网页的网络利用及网页性能进行检测，并给出一些优化建议。

**Postman**

地址：[`www.postman.com/`](https://www.postman.com/)

几乎所有前端应用程序都发送和接收 JSON 响应和请求。 应用程序通过请求 API 可以做很多事情，例如身份验证，用户数据传输，甚至是一些简单的事情，例如获取所在位置的当前天气。

Postman 是调试接口的最佳工具之一。 它适用于 **MacOS**，**Windows** 和**Linux**的系统， 可以快速轻松地直接发送**REST**，**SOAP**和**GraphQL**请求。

使用 Postman，我们可以调整请求，分析响应和调试问题。 当不确定问题出在前端还是后端时，这是很有帮助的。

**CSS Lint**

地址： [`csslint.net/`](http://csslint.net/)

**CSSLint** 是一个用来帮你找出 CSS 代码中问题的工具，它可做基本的语法检查以及使用一套预设的规则来检查代码中的问题，规则是可以扩展的。

**JSON Formatter & Validator**

地址：[`jsonformatter.curiousconcept.com/`](https://jsonformatter.curiousconcept.com/)

在未格式化的 JSON 中很难发现语法错误或键值不正确的键，因为它很难读取。 对于 压缩的 JSON 文件，要发现其中的错误是比较难的，所以我们需要一种格式化的工具。

JSON Formatter & Validator tool 就是一个格式化 JSON 的工具，只需输入压缩的`JSON`格式，就能获得正确格式。该工具也可以验证 JSON 到 RFC 标准。

**Sentry**

地址：[`sentry.io/welcome/`](https://sentry.io/welcome/)

无论测试如何完善的程序，bug 总是免不了会存在的，有些 bug 不是每次都会出现，测试时运行好好的代码可能在某个用户使用时就歇菜了，可是当程序在用户面前崩溃时，你是看不到错误的，当然你会说:”Hey, 我有记日志呢”。 但是说实话，程序每天每时都在产生大量的日志，而且分布在各个服务器上，并且如果你有多个服务在维护的话，日志的数量之多你是看不过来的吧。等到某天某个用户实在受不了了，打电话来咆哮的时候，你再去找日志你又会发现日志其实没什么用：缺少上下文，不知道用户什么操作导致的异常，异常太多（从不看日志的缘故）不知如何下手 等等。

**Sentry**就是来帮我们解决这个问题的，它是是一个实时事件日志记录和聚合平台。它专门用于监视错误和提取执行适当的事后操作所需的所有信息, 而无需使用标准用户反馈循环的任何麻烦。

Sentry 是一个日志平台, 它分为客户端和服务端，客户端(目前客户端有 Python, PHP,C#, Ruby 等多种语言)就嵌入在你的应用程序中间，程序出现异常就向服务端发送消息，服务端将消息记录到数据库中并提供一个 web 节目方便查看。**Sentry** 由 python 编写，源码开放，性能卓越，易于扩展，目前著名的用户有`Disqus, Path, mozilla, Pinterest`等。

**JSHint**

地址：[`jshint.com/`](https://jshint.com/)

JSHint 是一个 Javascript 代码分析检测工具，不仅可以帮助我们检测到 JS 代码错误和潜在问题，也能帮助我们规范代码开发。

**JSHint** 扫描一个用 JavaScript 编写的程序，并报告常见的错误和潜在的 bug。潜在的问题可能是语法错误、隐式类型转换导致的错误、泄漏变量或其他完全的问题。

**JSHint** 扫描用 JavaScript 编写的程序，并报告常见的错误和潜在的错误。 潜在的问题可能是语法错误，由于隐式类型转换导致的错误，变量泄漏或其他完全原因。

**BrowserStack**

地址：[`www.browserstack.com/`](https://www.browserstack.com/)

现在拥有各自内核的浏览器越来越多，各自的特性也千差万别。如果作为一个前端攻城师想要检测网站在不同的操作系统和移动平台下的各种浏览器的兼容性，那是相当痛苦不堪的。看到有在自己电脑上装虚拟机配置各种环境，有自己的电脑上组建好这样的环境，然后一一测试，可是人的精力毕竟有限，我们没法在同一台电脑上装那么多系统，那么多浏览器的。幸好出了个 BrowserStack 是前端的福音呀。

BrowserStack 是一款提供网站浏览器兼容性测试的在线云端测试工具，从而开发测试人员不必再准备很多虚拟机或者手机模拟器。

**BrowserStack** 是一个提供网站浏览器兼容性测试的在线云端应用，支持 9 大操作系统上的 100 多款浏览器。支持本地测试，支持与 Visual Studio 集成。或者你也可以直接前往 [`modern.ie`](http://modern.ie) 在线测试，现在注册可以免费试用三个月，三个月后是收费的，三个月后要是你想用又不想付费作为天朝的开发者你懂得。