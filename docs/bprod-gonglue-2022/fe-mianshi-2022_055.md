# 第六章 第 7 节 前端进阶-安全

> 原文：[`www.nowcoder.com/tutorial/10072/07d287ec11c54994814b6b1897825aa0`](https://www.nowcoder.com/tutorial/10072/07d287ec11c54994814b6b1897825aa0)

#### 4.1 如何提高网站的安全性？

**参考答案：**

前端常见安全问题的 7 个方面：

1.  iframe
2.  opener
3.  CSRF（跨站请求伪造）
4.  XSS（跨站脚本攻击）
5.  ClickJacking（点击劫持）
6.  HSTS（HTTP 严格传输安全）
7.  CND 劫持

**解析**：

1.  iframe

    a.如何让自己的网站不被其他网站的 iframe 引用？

```cpp
// 检测当前网站是否被第三方 iframe 引用
// 若相等证明没有被第三方引用，若不等证明被第三方引用。当发现被引用时强制跳转百度。
if(top.location != self.location){
    top.location.href = 'http://www.baidu.com'
}
```

​ b.如何禁用，被使用的 iframe 对当前网站某些操作？

> sandbox 是 html5 的新属性，主要是提高 iframe 安全系数。iframe 因安全问题而臭名昭著，这主要是因为 iframe 常被用于嵌入到第三方中，然后执行某些恶意操作。
> 现在有一场景：我的网站需要 iframe 引用某网站，但是不想被该网站操作 DOM、不想加载某些 js（广告、弹框等）、当前窗口被强行跳转链接等，我们可以设置 sandbox 属性。如使用多项用空格分隔。

*   allow-same-origin：允许被视为同源，即可操作父级 DOM 或 cookie 等
*   allow-top-navigation：允许当前 iframe 的引用网页通过 url 跳转链接或加载
*   allow-forms：允许表单提交
*   allow-scripts：允许执行脚本文件
*   allow-popups：允许浏览器打开新窗口进行跳转
*   “”：设置为空时上面所有允许全部禁止

2.  opener

> 如果在项目中需要 **打开新标签** 进行跳转一般会有两种方式

```cpp
// 1) HTML -> <a target='_blank' href='http://www.baidu.com'>
// 2)  JS  -> window.open('http://www.baidu.com')

/* 
 * 这两种方式看起来没有问题，但是存在漏洞。
 * 通过这两种方式打开的页面可以使用 window.opener 来访问源页面的 window 对象。
 * 场景：A 页面通过 <a> 或 window.open 方式，打开 B 页面。但是 B 页面存在恶意代码如下：
 * window.opener.location.replace('https://www.baidu.com') 【此代码仅针对打开新标签有效】
 * 此时，用户正在浏览新标签页，但是原来网站的标签页已经被导航到了百度页面。
 * 恶意网站可以伪造一个足以欺骗用户的页面，使得进行恶意破坏。
 * 即使在跨域状态下 opener 仍可以调用 location.replace 方法。
 */
```

​ a.

```cpp
<a target="_blank" href="" rel="noopener noreferrer nofollow">a 标签跳转 url</a>

<!-- 
  通过 rel 属性进行控制：
  noopener：会将 window.opener 置空，从而源标签页不会进行跳转（存在浏览器兼容问题）
  noreferrer：兼容老浏览器/火狐。禁用 HTTP 头部 Referer 属性（后端方式）。
  nofollow：SEO 权重优化，详情见 https://blog.csdn.net/qq_33981438/article/details/80909881
 -->
```

​ b.window.open()

```cpp
<button onclick='openurl("http://www.baidu.com")'>click 跳转</button>

function openurl(url) {
    var newTab = window.open();
    newTab.opener = null;
    newTab.location = url;
}
```

3.  CSRF / XSRF（跨站请求伪造）

> 你可以这么理解 CSRF 攻击：攻击者盗用了你的身份，以你的名义进行恶意请求。它能做的事情有很多包括：以你的名义发送邮件、发信息、盗取账号、购买商品、虚拟货币转账等。总结起来就是：个人隐私暴露及财产安全问题。

```cpp
/*
 * 阐述 CSRF 攻击思想：（核心 2 和 3）
 * 1、浏览并登录信任网站（举例：淘宝）
 * 2、登录成功后在浏览器产生信息存储（举例：cookie）
 * 3、用户在没有登出淘宝的情况下，访问危险网站
 * 4、危险网站中存在恶意代码，代码为发送一个恶意请求（举例：购买商品/余额转账）
 * 5、携带刚刚在浏览器产生的信息进行恶意请求
 * 6、淘宝验证请求为合法请求（区分不出是否是该用户发送）
 * 7、达到了恶意目标
 */
```

防御措施（推荐添加 token / HTTP 头自定义属性）

*   涉及到数据修改操作严格使用 post 请求而不是 get 请求
*   HTTP 协议中使用 Referer 属性来确定请求来源进行过滤（禁止外域）
*   请求地址添加 token ，使黑客无法伪造用户请求
*   HTTP 头自定义属性验证（类似上一条）
*   显示验证方式：添加验证码、密码等

4.  XSS/CSS（跨站脚本攻击）

> XSS 又叫 CSS（Cross Site Script），跨站脚本攻击：攻击者在目标网站植入恶意脚本（js / html），用户在浏览器上运行时可以获取用户敏感信息（cookie / session）、修改 web 页面以欺骗用户、与其他漏洞相结合形成蠕虫等。

浏览器遇到 html 中的 script 标签时，会解析并执行其中的 js 代码

针对这种情况，我们对特殊字符进行转译就好了（vue/react 等主流框架已经避免类似问题，vue 举例：不能在 template 中写 script 标签，无法在 js 中通过 ref 或 append 等方式动态改变或添加 script 标签）

XSS 类型：

*   持久型 XSS：将脚本植入到服务器上，从而导致每个访问的用户都会执行
*   非持久型 XSS：对个体用户某 url 的参数进行攻击

防御措施（对用户输入内容和服务端返回内容进行过滤和转译）

*   现代大部分浏览器都自带 XSS 筛选器，vue / react 等成熟框架也对 XSS 进行一些防护
*   即便如此，我们在开发时也要注意和小心
*   对用户输入内容和服务端返回内容进行过滤和转译
*   重要内容加密传输
*   合理使用 get/post 等请求方式
*   对于 URL 携带参数谨慎使用
*   我们无法做到彻底阻止，但是能增加黑客攻击成本，当成本与利益不符时自然会降低风险

5.  ClickJacking（点击劫持）

> ClickJacking 翻译过来被称为点击劫持。一般会利用透明 iframe 覆盖原网页诱导用户进行某些操作达成目的。

防御措施

*   在 HTTP 投中加入 X-FRAME-OPTIONS 属性，此属性控制页面是否可被嵌入 iframe 中【DENY：不能被所有网站嵌套或加载；SAMEORIGIN：只能被同域网站嵌套或加载；ALLOW-FROM URL：可以被指定网站嵌套或加载。】
*   判断当前网页是否被 iframe 嵌套（详情在第一条 firame 中）

6.  HSTS（HTTP Strict Transport Security：HTTP 严格传输安全）

> 网站接受从 HTTP 请求跳转到 HTTPS 请求的做法，例如我们输入“[`www.baidu.com`](https://link.zhihu.com/?target=http%3A//www.baidu.com)”或“[www.baidu.com”最终都会被 302 重定向到“[`www.baidu.com`](https://link.zhihu.com/?target=https%3A//www.baidu.com)”。这就存在安全风险，当我们第一次通过](http://www.baidu.com%E2%80%9D%E6%9C%80%E7%BB%88%E9%83%BD%E4%BC%9A%E8%A2%AB302%E9%87%8D%E5%AE%9A%E5%90%91%E5%88%B0%E2%80%9C%5Bhttps://www.baidu.com%5D(https://link.zhihu.com/?target=https%3A//www.baidu.com)%E2%80%9D%E3%80%82%E8%BF%99%E5%B0%B1%E5%AD%98%E5%9C%A8%E5%AE%89%E5%85%A8%E9%A3%8E%E9%99%A9%EF%BC%8C%E5%BD%93%E6%88%91%E4%BB%AC%E7%AC%AC%E4%B8%80%E6%AC%A1%E9%80%9A%E8%BF%87) HTTP 或域名进行访问时，302 重定向有可能会被劫持，篡改成一个恶意或钓鱼网站。
> HSTS：通知浏览器此网站禁止使用 HTTP 方式加载，浏览器应该自动把所有尝试使用 HTTP 的请求自动替换为 HTTPS 进行请求。用户首次访问时并不受 HSTS 保护，因为第一次还未形成链接。我们可以通过 **浏览器预置 HSTS 域名列表** 或 **将 HSTS 信息加入到域名系统记录中**，来解决第一次访问的问题。

7.  CDN 劫持

> 出于性能考虑，前端应用通常会把一些静态资源存放到 CDN（Content Delivery Networks）上面，例如 js 脚本和 style 文件。这么做可以显著提高前端应用的访问速度，但与此同时却也隐含了一个新的安全风险。如果攻击者劫持了 CDN，或者对 CDN 中的资源进行了污染，攻击者可以肆意篡改我们的前端页面，对用户实施攻击。
> 现在的 CDN 以支持 SRI 为荣，script 和 link 标签有了新的属性 integrity，这个属性是为了防止校验资源完整性来判断是否被篡改。它通过 **验证获取文件的哈希值是否和你提供的哈希值一样来判断资源是否被篡改**。
> 使用 SRI 需要两个条件：一是要保证 **资源同域** 或开启跨域，二是在<script>中 **提供签名** 以供校验。

integrity 属性分为两个部分，第一部分是指定哈希值的生成算法（例：sha384），第二部分是经过编码的实际哈希值，两者之前用一个短横(-)来分隔

#### 4.2 前端安全相关-XSS 和 CSRF

**参考答案**：

**XSS（Cross-site scripting）**，指的是跨站脚本攻击，攻击者通过向页面 A 注入代码，达到窃取信息等目的，本质是数据被当作程序执行。XSS 危害是很大的，一般 XSS 可以做到以下的事情：

*   获取页面的数据，包括 dom、cookies、localStorage 等
*   劫持前端逻辑
*   发送请求

**CSRF(Cross Site Request Frogy)指的是跨站请求伪造**。与 XSS 不同的是，XSS 是攻击者直接对我们的网站 A 进行注入攻击，CSRF 是通过网站 B 对我们的网站 A 进行伪造请求。

举个例子，你登录购物网站 A 之后点击一个恶意链接 B，B 请求了网站 A 的下单接口，结果是你在网站 A 的帐号真的会生成一个订单。其背后的原理是：网站 B 通过表单、get 请求来伪造网站 A 的请求，这时候请求会带上网站 A 的 cookies，若登录态是保存在 cookies 中，则实现了伪造攻击。

**解析：**

XSS

1.  XSS 的类型

    *   反射型（非持久）：通过 URL 参数直接注入
    *   存储型（持久）：存储到数据库后读取时注入
    *   基于 DOM：被执行的恶意脚本会修改页面脚本结构
2.  XSS 的注入点

    *   HTML 的节点内容或属性
    *   javascript 代码
    *   富文本
3.  XSS 的防御

    3.1 浏览器的防御

    防御和“X-XSS-Protection”有关，默认值为 1，即默认打开 XSS 防御，可以防御反射型的 XSS，不过作用有限，只能防御注入到 HTML 的节点内容或属性的 XSS，例如 URL 参数中包含 script 标签。不建议只依赖此防御手段。

    3.2 防御 HTML 节点内容

    存在风险的代码：

    ```cpp
    <template>
        <p>{{username}}</p>
    </template>

    <script>
        username = "<script>alert('xss')</script>"
    </script>
    ```

    编译后的代码：

    ```cpp
    <p>
        <script>alert('xss')</script>
    </p>
    ```

    以上例子是采用 vue 语法，但其实在 vue 这样的框架中，{{username}}中的内容是经过字符串化的，所以是不会被浏览器执行的，若换其他模板语言例如 jade，则可能存在风险。下同。

    防御代码：

    通过转义`<`为`&lt`以及`>`为`&gt`来实现防御 HTML 节点内容。

    ```cpp
    <template>
        <p>{{username}}</p>
    </template>
    <script>
        escape = function(str){
            return str.replace(/</g, '&lt;').replace(/>/g, '&gt;')
        }
        username = escape("<script>alert('xss')</script>")
    </script>
    ```

    3.3 防御 HTML 属性

    ```cpp
    <template>
        <img :src="image" />
    </template>
    <script>
        image = 'www.a.com/c.png" onload="alert(1)'
    </script>
    ```

    编译后代码：

    ```cpp
    <img src="www.a.com/c.png" onload="alert(1)" />
    ```

    防御代码：

    通过转义`"`为`&quto;`、`'`为`'`来实现防御，一般不转义空格，但是这要求属性必须带引号！

    ```cpp
    <template>
        <img :src="image" />
    </template>
    <script>
        escape = function(str){
            return str.replace(/"/g, '&quto;').replace(/'/g, '&#39;').replace(/ /g, '&#32;')
        }
        image = escape('www.a.com/c.png" onload="alert(1)')
    </script>
    ```

    3.4 防御 javaScript 代码

    假设访问页面地址为`www.a.com?id=1";alert(1);"`

    风险代码：

    ```cpp
    var id = getQuery('id')
    ```

    编译后代码：

    ```cpp
    var id = "1";alert(1);""
    ```

    防御代码：

    通过将数据进行 JSON 序列化

    ```cpp
    escape = function(str){
        return JSON.stringify(str)
    }
    ```

    3.5 防御富文本

    风险代码：

    ```cpp
    <template>
        <p v-html="richTxt"></p>
    </template>

    <script>
        richTxt = '<a onmouseover=alert(document.cookie)>点击</a>'
    </script>
    ```

    上面的这段代码中，当鼠标移动到“点击”上面时，就会触发 alert 弹窗！这在 vue 中是会发生的。

    防御富文本是比较复杂的工程，因为富文本可以包含 HTML 和 script，这些难以预测与防御，建议是通过白名单的方式来过滤允许的 HTML 标签和标签的属性来进行防御，大概的实现方式是：

    *   将 HTML 代码段转成树级结构的数据

    *   遍历树的每一个节点，过滤节点的类型和属性，或进行特殊处理

    *   处理完成后，将树级结构转化成 HTML 代码

        当然，也可以通过开源的第三方库来实现，类似的有 js-xss

    3.6 CSP 内容安全策略

    CSP(content security policy)，是一个额外的安全层，用于检测并削弱某些特定类型的攻击，包括跨站脚本 (XSS) 和数据注入攻击等。

    CSP 可以通过 HTTP 头部（Content-Security-Policy）或``元素配置页面的内容安全策略，以控制浏览器可以为该页面获取哪些资源。比如一个可以上传文件和显示图片页面，应该允许图片来自任何地方，但限制表单的 action 属性只可以赋值为指定的端点。一个经过恰当设计的内容安全策略应该可以有效的保护页面免受跨站脚本攻击。

#### 4.3 url 的加密解密

**参考答案**：

JavaScript 中有三个可以对字符串编码的函数，分别是： escape,encodeURI,encodeURIComponent，相应 3 个解码函数：unescape,decodeURI,decodeURIComponent 。

**三种方式的特点：**escape()除了 ASCII 字母、数字和特定的符号外，对传进来的字符串全部进行转义编码，因此如果想对 URL 编码，最好不要使用此方法。而 encodeURI() 用于编码整个 URI,因为 URI 中的合法字符都不会被编码转换。encodeURIComponent 方法在编码单个 URIComponent（指请求参数）应当是最常用的，它可以讲参数中的中文、特殊字符进行转义，而不会影响整个 URL。

**解析**：

**1.escape()函数**

定义和用法
escape() 函数可对字符串进行编码，这样就可以在所有的计算机上读取该字符串。

语法
escape(string)

参数 描述
string 必需。要被转义或编码的字符串。

返回值
已编码的 string 的副本。其中某些字符被替换成了十六进制的转义序列。

说明
该方法不会对 ASCII 字母和数字进行编码，也不会对下面这些 ASCII 标点符号进行编码： - _ . ! ~ * ' ( ) 。其他所有的字符都会被转义序列替换。

**2.encodeURI()函数
**定义和用法
encodeURI() 函数可把字符串作为 URI 进行编码。

语法
encodeURI(URIstring)

参数 描述
URIstring 必需。一个字符串，含有 URI 或其他要编码的文本。

返回值
URIstring 的副本，其中的某些字符将被十六进制的转义序列进行替换。

说明
该方法不会对 ASCII 字母和数字进行编码，也不会对这些 ASCII 标点符号进行编码： - _ . ! ~ * ' ( ) 。

该方法的目的是对 URI 进行完整的编码，因此对以下在 URI 中具有特殊含义的 ASCII 标点符号，encodeURI() 函数是不会进行转义的：;/?:@&=+$,#

**3.encodeURIComponent() 函数**

定义和用法
encodeURIComponent() 函数可把字符串作为 URI 组件进行编码。

语法
encodeURIComponent(URIstring)

参数 描述
URIstring 必需。一个字符串，含有 URI 组件或其他要编码的文本。

返回值
URIstring 的副本，其中的某些字符将被十六进制的转义序列进行替换。

说明
该方法不会对 ASCII 字母和数字进行编码，也不会对这些 ASCII 标点符号进行编码： - _ . ! ~ * ' ( ) 。

其他字符（比如 ：;/?:@&=+$,# 这些用于分隔 URI 组件的标点符号），都是由一个或多个十六进制的转义序列替换的。

提示和注释
提示：请注意 encodeURIComponent() 函数 与 encodeURI() 函数的区别之处，前者假定它的参数是 URI 的一部分（比如协议、主机名、路径或查询字符串）。因此 encodeURIComponent() 函数将转义用于分隔 URI 各个部分的标点符号。

#### 4.4 前端安全问题

**参考答案**：

**1\. XSS：跨站脚本攻击**

就是攻击者想尽一切办法将可以执行的代码注入到网页中。

存储型（server 端）：

*   场景：见于带有用户保存数据的网站功能，如论坛发帖、商品评论、用户私信等。
*   攻击步骤：
    *   i）攻击者将恶意代码提交到目标网站的数据库中
    *   ii）用户打开目标网站时，服务端将恶意代码从数据库中取出来，拼接在 HTML 中返回给浏览器
    *   iii）用户浏览器在收到响应后解析执行，混在其中的恶意代码也同时被执行
    *   iv）恶意代码窃取用户数据，并发送到指定攻击者的网站，或者冒充用户行为，调用目标网站的接口，执行恶意操作

反射型（Server 端）

与存储型的区别在于，存储型的恶意代码存储在数据库中，反射型的恶意代码在 URL 上

*   场景：通过 URL 传递参数的功能，如网站搜索、跳转等。
*   攻击步骤：
    *   i）攻击者构造出特殊的 URL，其中包含恶意代码。
    *   ii）用户打开带有恶意代码的 URL 时，网站服务端将恶意代码从 URL 中取出，拼接在 HTML 中返回给浏览器。
    *   iii）用户浏览器接收到响应后解析执行，混在其中的恶意代码也被执行。
    *   iv）恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。

Dom 型(浏览器端）

DOM 型 XSS 攻击中，取出和执行恶意代码由浏览器端完成，属于前端 JavaScript 自身的安全漏洞，而其他两种 XSS 都属于服务端的安全漏洞。

*   场景：通过 URL 传递参数的功能，如网站搜索、跳转等。
*   攻击步骤：
    *   i）攻击者构造出特殊的 URL，其中包含恶意代码。
    *   ii）用户打开带有恶意代码的 URL。
    *   iii）用户浏览器接收到响应后解析执行，前端 JavaScript 取出 URL 中的恶意代码并执行。
    *   iv）恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。

预防方案：（防止攻击者提交恶意代码，防止浏览器执行恶意代码）

*   i）对数据进行严格的输出编码：如 HTML 元素的编码，JS 编码，CSS 编码，URL 编码等等
    *   避免拼接 HTML；Vue/React 技术栈，避免使用 v-html / dangerouslySetInnerHTML
*   ii）CSP HTTP Header，即 Content-Security-Policy、X-XSS-Protection
    *   增加攻击难度，配置 CSP(本质是建立白名单，由浏览器进行拦截)
    *   `Content-Security-Policy: default-src 'self'`-所有内容均来自站点的同一个源（不包括其子域名）
    *   `Content-Security-Policy: default-src 'self' *.trusted.com`-允许内容来自信任的域名及其子域名 (域名不必须与 CSP 设置所在的域名相同)
    *   `Content-Security-Policy: default-src https://yideng.com`-该服务器仅允许通过 HTTPS 方式并仅从 yideng.com 域名来访问文档
*   iii）输入验证：比如一些常见的数字、URL、电话号码、邮箱地址等等做校验判断
*   iv）开启浏览器 XSS 防御：Http Only cookie，禁止 JavaScript 读取某些敏感 Cookie，攻击者完成 XSS 注入后也无法窃取此 Cookie。
*   v）验证码

**2\. CSRF：跨站请求伪造**

攻击者诱导受害者进入第三方网站，在第三方网站中，向被攻击网站发送跨站请求。利用受害者在被攻击网站已经获取的注册凭证，绕过后台的用户验证，达到冒充用户对被攻击的网站执行某项操作的目的。

攻击流程举例

*   i）受害者登录 a.com，并保留了登录凭证（Cookie）
*   ii）攻击者引诱受害者访问了 b.com
*   iii）b.com 向 a.com 发送了一个请求：a.com/act=xx 浏览器会默认携带 a.com 的 Cookie
*   iv）a.com 接收到请求后，对请求进行验证，并确认是受害者的凭证，误以为是受害者自己发送的请求
*   v）a.com 以受害者的名义执行了 act=xx
*   vi）攻击完成，攻击者在受害者不知情的情况下，冒充受害者，让 a.com 执行了自己定义的操作

攻击类型

*   i）GET 型：如在页面的某个 img 中发起一个 get 请求
*   ii）POST 型：通过自动提交表单到恶意网站
*   iii）链接型：需要诱导用户点击链接

预防方案：

CSRF 通常从第三方网站发起，被攻击的网站无法防止攻击发生，只能通过增强自己网站针对 CSRF 的防护能力来提升安全性。）

*   i）同源检测：通过 Header 中的 Origin Header 、Referer Header 确定，但不同浏览器可能会有不一样的实现，不能完全保证
*   ii）CSRF Token 校验：将 CSRF Token 输出到页面中（通常保存在 Session 中），页面提交的请求携带这个 Token，服务器验证 Token 是否
    正确
*   iii）双重 cookie 验证：
    *   流程：
        *   步骤 1：在用户访问网站页面时，向请求域名注入一个 Cookie，内容为随机字符串（例如 csrfcookie=v8g9e4ksfhw）
        *   步骤 2：在前端向后端发起请求时，取出 Cookie，并添加到 URL 的参数中（接上例 POST [`www.a.com/comment?csrfcookie=v8g9e4ksfhw）`](https://www.a.com/comment?csrfcookie=v8g9e4ksfhw%EF%BC%89)
        *   步骤 3：后端接口验证 Cookie 中的字段与 URL 参数中的字段是否一致，不一致则拒绝。
    *   优点：
        *   无需使用 Session，适用面更广，易于实施。
        *   Token 储存于客户端中，不会给服务器带来压力。
        *   相对于 Token，实施成本更低，可以在前后端统一拦截校验，而不需要一个个接口和页面添加。
    *   缺点：
        -Cookie 中增加了额外的字段。
        -如果有其他漏洞（例如 XSS），攻击者可以注入 Cookie，那么该防御方式失效。
        -难以做到子域名的隔离。
        -为了确保 Cookie 传输安全，采用这种防御方式的最好确保用整站 HTTPS 的方式，如果还没切 HTTPS 的使用这种方式也会有风险。
*   iv）Samesite Cookie 属性：Google 起草了一份草案来改进 HTTP 协议，那就是为 Set-Cookie 响应头新增 Samesite 属性，它用来标明这个 Cookie 是个“同站 Cookie”，同站 Cookie 只能作为第一方 Cookie，不能作为第三方 Cookie，Samesite 有两个属性值，Strict 为任何情况下都不可以作为第三方 Cookie ，Lax 为可以作为第三方 Cookie , 但必须是 Get 请求

**3\. iframe 安全**

说明：

*   i）嵌入第三方 iframe 会有很多不可控的问题，同时当第三方 iframe 出现问题或是被劫持之后，也会诱发安全性问题
*   ii）点击劫持
    *   攻击者将目标网站通过 iframe 嵌套的方式嵌入自己的网页中，并将 iframe 设置为透明，诱导用户点击。
*   iii）禁止自己的 iframe 中的链接外部网站的 JS

预防方案：

*   i）为 iframe 设置 sandbox 属性，通过它可以对 iframe 的行为进行各种限制，充分实现“最小权限“原则
*   ii）服务端设置 X-Frame-Options Header 头，拒绝页面被嵌套，X-Frame-Options 是 HTTP 响应头中用来告诉浏览器一个页面是否可以嵌入 >iframe< 中
    *   eg.`X-Frame-Options: SAMEORIGIN`
    *   SAMEORIGIN: iframe 页面的地址只能为同源域名下的页面
    *   ALLOW-FROM: 可以嵌套在指定来源的 iframe 里
    *   DENY: 当前页面不能被嵌套在 iframe 里
*   iii）设置 CSP 即 Content-Security-Policy 请求头
*   iv）减少对 iframe 的使用

**4\. 错误的内容推断**

说明：

文件上传类型校验失败后，导致恶意的 JS 文件上传后，浏览器 Content-Type Header 的默认解析为可执行的 JS 文件

预防方案：

设置 X-Content-Type-Options 头

**5\. 第三方依赖包**

减少对第三方依赖包的使用，如之前 npm 的包如：event-stream 被爆出恶意攻击数字货币；

**6.HTTPS**

描述：

黑客可以利用 SSL Stripping 这种攻击手段，强制让 HTTPS 降级回 HTTP，从而继续进行中间人攻击。

预防方案：

使用 HSTS（HTTP Strict Transport Security），它通过下面这个 HTTP Header 以及一个预加载的清单，来告知浏览器和网站进行通信的时候强制性的使用 HTTPS，而不是通过明文的 HTTP 进行通信。这里的“强制性”表现为浏览器无论在何种情况下都直接向务器端发起 HTTPS 请求，而不再像以往那样从 HTTP 跳转到 HTTPS。另外，当遇到证书或者链接不安全的时候，则首先警告用户，并且不再
用户选择是否继续进行不安全的通信。

**7.本地存储数据**

避免重要的用户信息存在浏览器缓存中

**8.静态资源完整性校验**

描述

使用 内容分发网络 (CDNs) 在多个站点之间共享脚本和样式表等文件可以提高站点性能并节省带宽。然而，使用 CDN 也存在风险，如果攻击者获得对 CDN 的控制权，则可以将任意恶意内容注入到 CDN 上的文件中 （或完全替换掉文件），因此可能潜在地攻击所有从该 CDN 获取文件的站点。

预防方案

将使用 base64 编码过后的文件哈希值写入你所引用的 <script> 或 标签的 integrity 属性值中即可启用子资源完整性能。

**9.网络劫持**

描述：

*   DNS 劫持（涉嫌违法）：修改运行商的 DNS 记录，重定向到其他网站。DNS 劫持是违法的行为，目前 DNS 劫持已被监管，现在很少见 DNS 劫持
*   HTTP 劫持：前提有 HTTP 请求。因 HTTP 是明文传输，运营商便可借机修改 HTTP 响应内容（如加广告）。

预防方案

全站 HTTPS

**10.中间人攻击：**

中间人攻击（Man-in-the-middle attack, MITM），指攻击者与通讯的两端分别创建独立的联系，并交换其所收到的数据，使通讯的两端认为他们正在通过一个私密的连接与对方直接对话，但事实上整个会话都被攻击者窃听、篡改甚至完全控制。没有进行严格的证书校验是中间人攻击着手点。目前大多数加密协议都提供了一些特殊认证方法以阻止中间人攻击。如 SSL （安全套接字层）协议可以验证参与通讯的用户的证书是否有权威、受信任的数字证书认证机构颁发，并且能执行双向身份认证。攻击场景如用户在一个未加密的 WiFi 下访问网站。在中间人攻击中，攻击者可以拦截通讯双方的通话并插入新的内容。

场景

*   i）在一个未加密的 Wi-Fi 无线接入点的接受范围内的中间人攻击者，可以将自己作为一个中间人插入这个网络
*   ii）Fiddler / Charles （花瓶）代理工具
*   iii）12306 之前的自己证书

过程

*   i）客户端发送请求到服务端，请求被中间人截获
*   ii）服务器向客户端发送公钥
*   iii）中间人截获公钥，保留在自己手上。然后自己生成一个【伪造的】公钥，发给客户端
*   iv）客户端收到伪造的公钥后，生成加密 hash 值发给服务器
*   v）中间人获得加密 hash 值，用自己的私钥解密获得真秘钥,同时生成假的加密 hash 值，发给服务器
*   vi）服务器用私钥解密获得假密钥,然后加密数据传输给客户端

使用抓包工具 fiddle 来进行举例说明

*   首先通过一些途径在客户端安装证书
*   然后客户端发送连接请求，fiddle 在中间截取请求，并返回自己伪造的证书
*   客户端已经安装了攻击者的根证书，所以验证通过
*   客户端就会正常和 fiddle 进行通信，把 fiddle 当作正确的服务器
*   同时 fiddle 会跟原有的服务器进行通信，获取数据以及加密的密钥，去解密密钥

常见攻击方式

*   嗅探：嗅探是一种用来捕获流进和流出的网络数据包的技术，就好像是监听电话一样。比如：抓包工具
*   数据包注入：在这种，攻击者会将恶意数据包注入到常规数据中的，因为这些恶意数据包是在正常的数据包里面的，用户和系统都很难发现这个内容。
*   会话劫持：当我们进行一个网站的登录的时候到退出登录这个时候，会产生一个会话，这个会话是攻击者用来攻击的首要目标，因为这个会话，包含了用户大量的数据和私密信息。
*   SSL 剥离：HTTPS 是通过 SSL/TLS 进行加密过的，在 SSL 剥离攻击中，会使 SSL/TLS 连接断开，让受保护的 HTTPS，变成不受
    保护的 HTTP（这对于网站非常致命）
*   DNS 欺骗，攻击者往往通过入侵到 DNS 服务器，或者篡改用户本地 hosts 文件，然后去劫持用户发送的请求，然后转发到攻击者想要转发到的服务器
*   ARP 欺骗，ARP(address resolution protocol)地址解析协议，攻击者利用 APR 的漏洞，用当前局域网之间的一台服务器，来冒充客户端想要请求的服务端，向客户端发送自己的 MAC 地址，客户端无从得到真正的主机的 MAC 地址，所以，他会把这个地址当作真正
    的主机来进行通信，将 MAC 存入 ARP 缓存表。
*   代理服务器

预防方案：

*   i）用可信的第三方 CA 厂商
*   ii）不下载未知来源的证书，不要去下载一些不安全的文件
*   iii）确认你访问的 URL 是 HTTPS 的，确保网站使用了 SSL，确保禁用一些不安全的 SSL，只开启：TLS1.1，TLS1.2
*   iv）不要使用公用网络发送一些敏感的信息
*   v）不要去点击一些不安全的连接或者恶意链接或邮件信息

**11.sql 注入**

描述

就是通过把 SQL 命令插入到 Web 表单递交或输入域名或页面请求的查询字符串，最终达到欺骗数据库服务器执行恶意的 SQL 命令,从而达到和服务器
进行直接的交互

预防方案：

*   i）后台进行输入验证，对敏感字符过滤。
*   ii）使用参数化查询，能避免拼接 SQL，就不要拼接 SQL 语句。

**12.前端数据安全：**

描述

反爬虫。如猫眼电影、天眼查等等，以数据内容为核心资产的企业

预防方案：

*   i）font-face 拼接方式：猫眼电影、天眼查
*   ii）background 拼接：美团
*   iii）伪元素隐藏：汽车之家
*   iv）元素定位覆盖式：去哪儿
*   v）iframe 异步加载：网易云音乐

**13.其他建议**

*   i）定期请第三方机构做安全性测试，漏洞扫描
*   ii）使用第三方开源库做上线前的安全测试，可以考虑融合到 CI 中
*   iii）code review 保证代码质量
*   iv）默认项目中设置对应的 Header 请求头，如 X-XSS-Protection、 X-Content-Type-Options 、X-Frame-Options Header、Content-Security-Policy 等等
*   v）对第三方包和库做检测：NSP(Node Security Platform)，Snyk