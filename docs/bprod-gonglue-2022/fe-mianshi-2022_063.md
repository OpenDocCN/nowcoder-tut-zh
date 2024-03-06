# 第六章 第 15 节 前端进阶-网络传输 8

> 原文：[`www.nowcoder.com/tutorial/10072/df18d5a488874b7b94356735bf023cb2`](https://www.nowcoder.com/tutorial/10072/df18d5a488874b7b94356735bf023cb2)

#### 5.41 axios 的拦截器原理及应用

**参考答案**：

**应用场景**
请求拦截器用于在接口请求之前做的处理，比如为每个请求带上相应的参数（token，时间戳等）。
返回拦截器用于在接口返回之后做的处理，比如对返回的状态进行判断（token 是否过期）。

**拦截器的使用**

1.  在 src 目录下建立 api 文件夹
2.  文件夹内建立 axios.js 文件，进行接口请求的初始化配置

```cpp
import axios from 'axios'
let instance = axios.create({
    baseURL: "http://localhost:3000/",
    headers: {
        'content-type': 'application/x-www-form-urlencoded'
    }
})
//请求拦截器
instance.interceptors.request.use(config => { //拦截请求，做统一处理
    const token = "asdasdk"
    //在每个 http header 都加上 token
    config.headers.authorization = token
    return config
}, err => {//失败
    return Promise.reject(err)
})
//响应拦截器
instance.interceptors.response.use(response => { //拦截响应，做统一处理
    if (response.data.code) {
        switch (response.data.code) {
            case 200:
                console.log("1111")
        }
    }
    return response
}, err => { //无响应时的处理
    return Promise.reject(err.response.status)
})
export default instance
```

3.  在 main.js 中引入，并将其绑定到 Vue 原型上，设为全局，不用在每个页面重新引入

```cpp
import instance from './api/axios'
Vue.prototype.$http = instance
```

4.  页面使用

```cpp
this.$http.get(url).then(r => console.log(r)).catch(err => console.log(err))
this.$http.post(url, params).then(r => console.log(r)).catch(err => console.log(err))
```

5.  效果展示

![](img/601e6dc5b244e23d63e438582b609ea5.png)

![](img/ccdc342c86acf9ab4a43c8a82922de96.png)

**axios 拦截器实现原理剖析**

axios 接口请求内部流程

![](img/26c7aed7e3fa7ea8f179938fbed3a702.png)

axios 原理简化

```cpp
function Axios(){
    this.interceptors = {
        //两个拦截器
        request: new interceptorsManner(),
        response: new interceptorsManner()
    }
}
//真正的请求
Axios.prototype.request = function(){
    let chain = [dispatchRequest, undefined];//这儿的 undefined 是为了补位，因为拦截器的返回有两个
    let promise = Promise.resolve();
    //将两个拦截器中的回调加入到 chain 数组中
    this.interceptors.request.handler.forEach((interceptor)=>{
        chain.unshift(interceptor.fulfilled, interceptor.rejected);
    })
    this.interceptors.response.handler.forEach((interceptor)=>{
        chain.push(interceptor.fulfilled, interceptor.rejected);
    })
    while(chain.length){
        //promise.then 的链式调用，下一个 then 中的 chain 为上一个中的返回值，每次会减去两个
        //这样就实现了在请求的时候，先去调用请求拦截器的内容，再去请求接口，返回之后再去执行响应拦截器的内容
        promise = promise.then(chain.shift(),chain.shift());
    }
}
function interceptorsManner(){
    this.handler = [];
}
interceptorsManner.prototype.use = function(fulfilled,rejected){
    //将成功与失败的回调 push 到 handler 中
    this.handler.push({
        fulfilled: fulfilled,
        rejected: rejected
    })
}
//类似方法批量注册,实现多种请求
util.forEach(["get","post","delete"],(methods)=>{
    Axios.prototype[methods] = function(url,config){
        return this.request(util.merge(config||{},{//合并
            method: methods,
            url: url
        }))
    }
})
```

#### 5.42 介绍下 HTTPS 中间人攻击

**参考答案**：

https 协议由 http + ssl 协议构成。

中间人攻击过程如下：

1.  服务器向客户端发送公钥；
2.  攻击者截获公钥，保留在自己手上；
3.  然后攻击者自己生成一个【伪造的】公钥，发给客户端；
4.  客户端收到伪造的公钥后，生成加密 hash（秘钥） 值发给服务器；
5.  攻击者获得加密 hash 值，用自己的私钥解密获得真秘钥；
6.  同时生成假的加密 hash 值，发给服务器；
7.  服务器用私钥解密获得假秘钥；
8.  服务器用假秘钥加密传输信息；

防范方法：

服务器在发送浏览器的公钥中加入 CA 证书，浏览器可以验证 CA 证书的有效性；（现有 HTTPS 很难被劫持，除非信任了劫持者的 CA 证书）。

#### 5.43 SSL 连接断开后如何恢复？

**参考答案**：

**Session ID**

每一次的会话都有一个编号，当对话中断后，下一次重新连接时，只要客户端给出这个编号，服务器如果有这个编号的记录，那么双方就可以继续使用以前的密钥，而不用重新生成一把。

**Session Ticket**

session ticket 是服务器在上一次对话中发送给客户的，这个 ticket 是加密的，只有服务器可能够解密，里面包含了本次会话的信息，比如对话密钥和加密方法等。这样不管我们的请求是否转移到其他的服务器上，当服务器将 ticket 解密以后，就能够获取上次对话的信息，就不用重新生成对话秘钥了。

#### 5.44 hosts 文件是什么？

**参考答案**：

hosts 文件是个没有扩展名的系统文件，其作用就是将网址域名和其对应的 IP 地址建立一个关联“数据库”，当用户在浏览器中输入一个 url 时，系统会首先自动从 hosts 文件中寻找对应的 IP 地址。

#### 5.45 同域请求的并发数限制的原因

**参考答案**：

浏览器的并发请求数目限制是针对同一域名的，同一时间针对同一域名下的请求有一定数量限制，超过限制数目的请求会被阻塞（chorme 和 firefox 的限制请求数都是 6 个）。

限制其数量的原因是：基于浏览器端口的限制和线程切换开销的考虑，浏览器为了保护自己不可能无限量的并发请求，如果一次性将所有请求发送到服务器，也会造成服务器的负载上升。

#### 5.46 cdn 加速原理

**参考答案**：

1.  当用户点击网站页面上的 url 时，经过本地 dns 系统解析，dns 系统会将域名的解析权给交 cname 指向的 cdn 专用 dns 服务器。

2.  cdn 的 dns 服务器将 cdn 的全局负载均衡设备 ip 地址返回给用户。

3.  用户向 cdn 的全局负载均衡设备发起内容 url 访问请求。

4.  cdn 全局负载均衡设备根据用户 ip，以及用户请求的内容 url，选择一台用户所属区域的区域负载均衡设备

5.  区域负载均衡设备会为用户选择一台合适的缓存服务器提供服务，选择的依据包括：根据用户 IP 地址，判断哪一台服务器距用户最近；根据用户所请求的 URL 中携带的内容名称，判断哪一台服务器上有用户所需内容；查询各个服务器当前的负载情况，判断哪一台服务器尚有服务能力。基于以上这些条件的综合分析之后，区域负载均衡设备会向全局负载均衡设备返回一台缓存服务器的 IP 地址全局负载均衡设备把服务器的 IP 地址返回给用户。

6.  用户向缓存服务器发起请求，缓存服务器响应用户请求，将用户所需内容传送到用户终端。如果这台缓存服务器上没有用户想要的内容，而区域均衡设备依然将它分配给了用户，那么这台服务器 就要向它的上一级缓存服务器发起请求内容，直至追溯到网站的源服务器将内容拉回给用户。

#### 5.47 创建 ajax 过程

**参考答案**：

1.  创建 XMLHttpRequest 对象,也就是创建一个异步调用对象.
2.  创建一个新的 HTTP 请求,并指定该 HTTP 请求的方法、URL 及验证信息.
3.  设置响应 HTTP 请求状态变化的函数.
4.  发送 HTTP 请求.
5.  获取异步调用返回的数据.
6.  使用 JavaScript 和 DOM 实现局部刷新.