# 第六章 第 16 节 前端进阶-网络传输 9

> 原文：[`www.nowcoder.com/tutorial/10072/0a0c36dc6d36472c91ce61641997779e`](https://www.nowcoder.com/tutorial/10072/0a0c36dc6d36472c91ce61641997779e)

#### 5.48 常用的 http 请求头以及响应头详

**参考答案：**

**一、常用的 http 请求头**

**1.Accept**

*   **Accept: text/html** 浏览器可以接受服务器回发的类型为 text/html。
*   **Accept: */*** 代表浏览器可以处理所有类型,(一般浏览器发给服务器都是发这个)。

**2.Accept-Encoding**

*   **Accept-Encoding: gzip, deflate** 浏览器申明自己接收的编码方法，通常指定压缩方法，是否支持压缩，支持什么压缩方法（gzip，deflate），（注意：这不是只字符编码）。

**3.Accept-Language**

*   **Accept-Language:zh-CN,zh;q=0.9** 浏览器申明自己接收的语言。

**4.Connection**

*   **Connection: keep-alive** 当一个网页打开完成后，客户端和服务器之间用于传输 HTTP 数据的 TCP 连接不会关闭，如果客户端再次访问这个服务器上的网页，会继续使用这一条已经建立的连接。
*   **Connection: close** 代表一个 Request 完成后，客户端和服务器之间用于传输 HTTP 数据的 TCP 连接会关闭， 当客户端再次发送 Request，需要重新建立 TCP 连接。

**5.Host（发送请求时，该报头域是必需的）**

*   **Host：** 请求报头域主要用于指定被请求资源的 Internet 主机和端口号，它通常从 HTTP URL 中提取出来的。

**6.Referer**

*   **Referer:** 当浏览器向 web 服务器发送请求的时候，一般会带上 Referer，告诉服务器我是从哪个页面链接过来的，服务器籍此可以获得一些信息用于处理。

**7.User-Agent**

*   **User-Agent:Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.110 Safari/537.36** 告诉 HTTP 服务器， 客户端使用的操作系统和浏览器的名称和版本。

**8.Cache-Control**

*   **Cache-Control:private** 默认为 private 响应只能够作为私有的缓存，不能再用户间共享
*   **`\**Cache-Control:public** `**响应会被缓存，并且在多用户间共享。正常情况, 如果要求 HTTP 认证,响应会自动设置为 private.
*   **Cache-Control:must-revalidate** 响应在特定条件下会被重用，以满足接下来的请求，但是它必须到服务器端去验证它是不是仍然是最新的。
*   **Cache-Control:no-cache** 响应不会被缓存,而是实时向服务器端请求资源。
*   **Cache-Control:max-age=10** 设置缓存最大的有效时间，但是这个参数定义的是时间大小（比如：60）而不是确定的时间点。单位是[秒 seconds]。
*   **`Cache-Control:no-store`**在任何条件下，响应都不会被缓存，并且不会被写入到客户端的磁盘里，这也是基于安全考虑的某些敏感的响应才会使用这个。

**9.Cookie**

Cookie 是用来存储一些用户信息以便让服务器辨别用户身份的（大多数需要登录的网站上面会比较常见），比如 cookie 会存储一些用户的用户名和密码，当用户登录后就会在客户端产生一个 cookie 来存储相关信息，这样浏览器通过读取 cookie 的信息去服务器上验证并通过后会判定你是合法用户，从而允许查看相应网页。当然 cookie 里面的数据不仅仅是上述范围，还有很多信息可以存储是 cookie 里面，比如 sessionid 等。

**10.Range（用于断点续传）**

*   **Range:bytes=0-5** 指定第一个字节的位置和最后一个字节的位置。用于告诉服务器自己想取对象的哪部分。

**二、常用的 http 响应头**

**1.Cache-Control（对应请求中的 Cache-Control）**

*   **Cache-Control:private** 默认为 private 响应只能够作为私有的缓存，不能再用户间共享
*   ***\*Cache-Control:public**** 浏览器和缓存服务器都可以缓存页面信息。
*   **Cache-Control:must-revalidate** 对于客户机的每次请求，代理服务器必须想服务器验证缓存是否过时。
*   **Cache-Control:no-cache** 浏览器和缓存服务器都不应该缓存页面信息。
*   **Cache-Control:max-age=10** 是通知浏览器 10 秒之内不要烦我，自己从缓冲区中刷新。
*   **Cache-Control:no-store** 请求和响应的信息都不应该被存储在对方的磁盘系统中。

**2.Content-Type**

*   **Content-Type：text/html;charset=UTF-8** 告诉客户端，资源文件的类型，还有字符编码，客户端通过 utf-8 对资源进行解码，然后对资源进行 html 解析。通常我们会看到有些网站是乱码的，往往就是服务器端没有返回正确的编码。

**3.Content-Encoding**

*   **Content-Encoding:gzip** 告诉客户端，服务端发送的资源是采用 gzip 编码的，客户端看到这个信息后，应该采用 gzip 对资源进行解码。

**4.Date**

*   **Date: Tue, 03 Apr 2018 03:52:28 GMT** 这个是服务端发送资源时的服务器时间，GMT 是格林尼治所在地的标准时间。http 协议中发送的时间都是 GMT 的，这主要是解决在互联网上，不同时区在相互请求资源的时候，时间混乱问题。

**5.Server**

*   **Server：Tengine/1.4.6** 这个是服务器和相对应的版本，只是告诉客户端服务器信息**。**

**6.Transfer-Encoding**

*   **Transfer-Encoding：chunked** 这个响应头告诉客户端，服务器发送的资源的方式是分块发送的。一般分块发送的资源都是服务器动态生成的，在发送时还不知道发送资源的大小，所以采用分块发送，每一块都是独立的，独立的块都能标示自己的长度，最后一块是 0 长度的，当客户端读到这个 0 长度的块时，就可以确定资源已经传输完了。

**7.Expires**

*   **Expires:Sun, 1 Jan 2000 01:00:00 GMT** 这个响应头也是跟缓存有关的，告诉客户端在这个时间前，可以直接访问缓存副本，很显然这个值会存在问题，因为客户端和服务器的时间不一定会都是相同的，如果时间不同就会导致问题。所以这个响应头是没有 Cache-Control：max-age=*这个响应头准确的，因为 max-age=date 中的 date 是个相对时间，不仅更好理解，也更准确。

**8.Last-Modified**

*   **Last-Modified: Dec, 26 Dec 2015 17:30:00 GMT** 所请求的对象的最后修改日期(按照 RFC 7231 中定义的“超文本传输协议日期”格式来表示)

**9.Connection**

*   **Connection：keep-alive** 这个字段作为回应客户端的 Connection：keep-alive，告诉客户端服务器的 tcp 连接也是一个长连接，客户端可以继续使用这个 tcp 连接发送 http 请求。

**10.Etag**

*   **ETag: "737060cd8c284d8af7ad3082f209582d"** 就是一个对象（比如 URL）的标志值，就一个对象而言，比如一个 html 文件，如果被修改了，其 Etag 也会别修改，所以，ETag 的作用跟 Last-Modified 的作用差不多，主要供 WEB 服务器判断一个对象是否改变了。比如前一次请求某个 html 文件时，获得了其 ETag，当这次又请求这个文件时，浏览器就会把先前获得 ETag 值发送给 WEB 服务器，然后 WEB 服务器会把这个 ETag 跟该文件的当前 ETag 进行对比，然后就知道这个文件有没有改变了。

**11.Refresh**

*   **Refresh: ** 用于重定向，或者当一个新的资源被创建时。默认会在 5 秒后刷新重定向。

**12.Access-Control-Allow-Origin**

*   **Access-Control-Allow-Origin: *** *号代表所有网站可以跨域资源共享，如果当前字段为*那么 Access-Control-Allow-Credentials 就不能为 true
*   **Access-Control-Allow-Origin: [www.baidu.com](http://www.baidu.com)** 指定哪些网站可以跨域资源共享

**13.Access-Control-Allow-Methods**

*   **Access-Control-Allow-Methods：GET,POST,PUT,DELETE** 允许哪些方法来访问

**14.Access-Control-Allow-Credentials**

*   **Access-Control-Allow-Credentials: true** 是否允许发送 cookie。默认情况下，Cookie 不包括在 CORS 请求之中。设为 true，即表示服务器明确许可，Cookie 可以包含在请求中，一起发给服务器。这个值也只能设为 true，如果服务器不要浏览器发送 Cookie，删除该字段即可。如果 access-control-allow-origin 为*，当前字段就不能为 true

**15.Content-Range**

*   **Content-Range: bytes 0-5/7877** 指定整个实体中的一部分的插入位置，他也指示了整个实体的长度。在服务器向客户返回一个部分响应，它必须描述响应覆盖的范围和整个实体长度。

#### 5.49 fetch 请求方式

**参考答案**：

**fetch**

Fetch API 是近年来被提及将要取代`XHR`的技术新标准，是一个 HTML5 的 API。
Fetch 并不是`XHR`的升级版本，而是从一个全新的角度来思考的一种设计。Fetch 是基于 Promise 语法结构，而且它的设计足够低阶，这表示它可以在实际需求中进行更多的弹性设计。对于**XHR**所提供的能力来说，Fetch 已经足够取代`XHR`，并且提供了更多拓展的可能性。

**基本用法**

```cpp
// 获取 some.json 资源  
fetch('some.json')  
 .then(function(response) {  
    return response.json();  
 })  
 .then(function(data) {  
    console.log('data', data);  
 })  
 .catch(function(error) {  
    console.log('Fetch Error: ', error);  
 });  

// 采用 ES2016 的 async/await 语法  
async function() {  
 try {  
    const response = await fetch('some.json');  
    const data = response.json();  
    console.log('data', data);  
 } catch (error) {  
    console.log('Fetch Error: ', error)  
 }  
}
```

**fetch.Post 请求**

```cpp
fetch('https://www.api.com/api/xxx', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8'
    },
    body: 'a=1&b=2',
}).then(resp => resp.json()).then(resp => {
    console.log(resp)
});
```

**fetch.Get 请求**

```cpp
fetch('https://www.api.com/api/xxx?location=北京&key=bc08513d63c749aab3761f77d74fe820',{
    method:'GET'
}) // 返回一个 Promise 对象
.then((res)=>{
    return res.json();
})
.then((res)=>{
    console.log(res) // res 是最终的结果
})
```

**fetch 请求网页**

```cpp
fetch('https://www.api.com/api/xxx')
    .then(response => response.text())
    .then(data => console.log(data));
```

**自定义 header**

```cpp
var headers = new Headers({
    "Content-Type": "text/plain",
    "X-Custom-Header": "aaabbbccc",
});

var formData = new FormData();
formData.append('name', 'lxa');
formData.append('file', someFile);

var config = {
    credentials: 'include', // 支持 cookie
    headers: headers, // 自定义头部
    method: 'POST', // post 方式请求
    body: formData // post 请求携带的内容
};

fetch('https://www.api.com/api/xxx', config)
    .then(response => response.json())
    .then(data => console.log(data));

// 或者这样添加头部
var content = "Hello World";
var myHeaders = new Headers();
myHeaders.append("Content-Type", "text/plain");
myHeaders.append("Content-Length", content.length.toString());
myHeaders.append("X-Custom-Header", "ProcessThisImmediately");
```

**fetch 其他参数**

*   method: 请求的方法，例如：`GET`,`POST`。
*   headers: 请求头部信息，可以是一个简单的对象，也可以是 Headers 类实例化的一个对象。
*   body: 需要发送的信息内容，可以是`Blob`,`BufferSource`,`FormData`,`URLSearchParams`或者`USVString`。注意，`GET`,`HEAD`方法不能包含 body。
*   mode: 请求模式，分别有`cors`,`no-cors`,`same-origin`,`navigate`这几个可选值。
    *   cors: 允许跨域，要求响应中`Acess-Control-Allow-Origin`这样的头部表示允许跨域。
    *   no-cors: 只允许使用`HEAD`,`GET`,`POST`方法。
    *   same-origin: 只允许同源请求，否则直接报错。
    *   navigate: 支持页面导航。
*   credentials: 表示是否发送`cookie`，有三个选项
    *   omit: 不发送`cookie`。
    *   same-origin: 仅在同源时发送`cookie`。
    *   include: 发送`cookie`。
*   cache: 表示处理缓存的策略。
*   redirect: 表示发生重定向时，有三个选项
    *   follow: 跟随。
    *   error: 发生错误。
    *   manual: 需要用户手动跟随。
*   integrity: 包含一个用于验证资资源完整性的字符串

```cpp
var URL = 'https://www.api.com/api/xxx';  
// 实例化 Headers  
var headers = new Headers({  
    "Content-Type": "text/plain",  
    "Content-Length": content.length.toString(),  
    "X-Custom-Header": "ProcessThisImmediately",  
});  
var getReq = new Request(URL, {method: 'GET', headers: headers });  
fetch(getReq).then(function(response) {  
    return response.json();  
}).catch(function(error) {  
    console.log('Fetch Error: ', error);  
});
```