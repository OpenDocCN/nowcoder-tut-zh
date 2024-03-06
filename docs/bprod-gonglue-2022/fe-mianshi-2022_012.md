# 第四章 第 3 节 前端框架 vue-3

> 原文：[`www.nowcoder.com/tutorial/10072/6f903d4a87334922a222ab8fe8887bd4`](https://www.nowcoder.com/tutorial/10072/6f903d4a87334922a222ab8fe8887bd4)

#### 1.13 js 是如何监听 HistoryRouter 的变化的

**参考答案：**

通过浏览器的地址栏来改变切换页面，前端实现主要有两种方式：

1.  通过 hash 改变，利用 window.onhashchange 监听。

2.  **HistoryRouter：**通过 history 的改变，进行 js 操作加载页面，然而 history 并不像 hash 那样简单，因为 history 的改变，除了浏览器的几个前进后退（使用 history.back(), history.forward()和 history.go() 方法来完成在用户历史记录中向后和向前的跳转。）等操作会主动触发 popstate 事件，pushState，replaceState 并不会触发 popstate 事件，要解决 history 监听的问题，方法是：

    首先完成一个订阅-发布模式，然后重写 history.pushState, history.replaceState,并添加消息通知，这样一来只要 history 的无法实现监听函数就被我们加上了事件通知，只不过这里用的不是浏览器原生事件，而是通过我们创建的 event-bus 来实现通知，然后触发事件订阅函数的执行。

具体操作如下：

1.  订阅-发布模式示例

```cpp
class Dep {                  // 订阅池
    constructor(name){
        this.id = new Date() //这里简单的运用时间戳做订阅池的 ID
        this.subs = []       //该事件下被订阅对象的集合
    }
    defined(){              // 添加订阅者
        Dep.watch.add(this);
    }
    notify() {              //通知订阅者有变化
        this.subs.forEach((e, i) => {
            if(typeof e.update === 'function'){
                try {
                   e.update.apply(e)  //触发订阅者更新函数
                } catch(err){
                    console.warr(err)
                }
            }
        })
    }
}
Dep.watch = null;
class Watch {
    constructor(name, fn){
        this.name = name;       //订阅消息的名称
        this.id = new Date();   //这里简单的运用时间戳做订阅者的 ID
        this.callBack = fn;     //订阅消息发送改变时->订阅者执行的回调函数     
    }
    add(dep) {                  //将订阅者放入 dep 订阅池
       dep.subs.push(this);
    }
    update() {                  //将订阅者更新方法
        var cb = this.callBack; //赋值为了不改变函数内调用的 this
        cb(this.name);         
    }
}
```

2.  重写 history 方法，并添加 window.addHistoryListener 事件机制。

```cpp
var addHistoryMethod = (function(){
        var historyDep = new Dep();
        return function(name) {
            if(name === 'historychange'){
                return function(name, fn){
                    var event = new Watch(name, fn)
                    Dep.watch = event;
                    historyDep.defined();
                    Dep.watch = null;       //置空供下一个订阅者使用
                }
            } else if(name === 'pushState' || name === 'replaceState') {
                var method = history[name];
                return function(){
                    method.apply(history, arguments);
                    historyDep.notify();
                }
            }
        }
}())
window.addHistoryListener = addHistoryMethod('historychange');
history.pushState =  addHistoryMethod('pushState');
history.replaceState =  addHistoryMethod('replaceState');
```

#### 1.14 HashRouter 和 HistoryRouter 的区别和原理

**参考答案：**

**vue-router**是 Vue 官方的路由管理器。它和 Vue.js 的核心深度集成，让构建单页面应用变得易如反掌。`vue-router` 默认 hash 模式，还有一种是 history 模式。

原理：

1.  hash 路由：hash 模式的工作原理是 hashchange 事件，可以在 window 监听 hash 的变化。我们在 url 后面随便添加一个#xx 触发这个事件。vue-router 默认的是 hash 模式—使用 URL 的 hash 来模拟一个完整的 URL,于是当 URL 改变的时候,页面不会重新加载,也就是单页应用了,当#后面的 hash 发生变化,不会导致浏览器向服务器发出请求,浏览器不发出请求就不会刷新页面,并且会触发 hasChange 这个事件,通过监听 hash 值的变化来实现更新页面部分内容的操作

    对于 hash 模式会创建 hashHistory 对象,在访问不同的路由的时候,会发生两件事:
    HashHistory.push()将新的路由添加到浏览器访问的历史的栈顶,和 HasHistory.replace()替换到当前栈顶的路由

2.  history 路由：

    主要使用 HTML5 的 pushState()和 replaceState()这两个 api 结合 window.popstate 事件（监听浏览器前进后退）来实现的,pushState()可以改变 url 地址且不会发送请求,replaceState()可以读取历史记录栈,还可以对浏览器记录进行修改

区别：

1.  hash 模式较丑，history 模式较优雅
2.  pushState 设置的新 URL 可以是与当前 URL 同源的任意 URL；而 hash 只可修改#后面的部分，故只可设置与当前同文档的 URL
3.  pushState 设置的新 URL 可以与当前 URL 一模一样，这样也会把记录添加到栈中；而 hash 设置的新值必须与原来不一样才会触发记录添加到栈中
4.  pushState 通过 stateObject 可以添加任意类型的数据到记录中；而 hash 只可添加短字符串
5.  pushState 可额外设置 title 属性供后续使用
6.  hash 兼容 IE8 以上，history 兼容 IE10 以上
7.  history 模式需要后端配合将所有访问都指向 index.html，否则用户刷新页面，会导致 404 错误

使用方法:

```cpp
<script>
        // hash 路由原理***************************
        // 监听 hashchange 方法
        window.addEventListener('hashchange',()=>{
            div.innerHTML = location.hash.slice(1)
        })
        // history 路由原理************************
        // 利用 html5 的 history 的 pushState 方法结合 window.popstate 事件（监听浏览器前进后退）
        function routerChange (pathname){
            history.pushState(null,null,pathname)
            div.innerHTML = location.pathname
        }
        window.addEventListener('popstate',()=>{
            div.innerHTML = location.pathname
        })
</script>
```

#### 1.15 Vue router 原理, 哪个模式不会请求服务器

**参考答案：**

Vue router 的两种方法，hash 模式不会请求服务器

**解析：**

1.  url 的 hash，就是通常所说的锚点#，javascript 通过 hashChange 事件来监听 url 的变化，IE7 以下需要轮询。比如这个 URL：`http://www.abc.com/#/hello`，hash 的值为 `#/hello`。它的特点在于：hash 虽然出现在 URL 中，但不会被包括在 HTTP 请求中，对后端完全没有影响，因此**改变 hash 不会重新加载页面**。

2.  HTML5 的 History 模式，它使 url 看起来像普通网站那样，以“/”分割，没有#，单页面并没有跳转。不过使用这种模式需要服务端支持，服务端在接收到所有请求后，都只想同一个 html 文件，不然会出现 404。因此单页面应用只有一个 html，整个网站的内容都在这一个 html 里，通过 js 来处理。

#### 1.16 组件通信的方式

**参考答案：**

组件通信的方式的方式有以下 8 种方法：

1.  props 和$emit

    这是最最常用的父子组件通信方式，父组件向子组件传递数据是通过 prop 传递的，子组件传递数据给父组件是通过$emit 触发事件来做到的

2.  ![](img/3c0f853db986c9041a774f6c50772a92.png)listeners

    第一种方式处理父子组件之间的数据传输有一个问题：如果多层嵌套，父组件 A 下面有子组件 B，组件 B 下面有组件 C,这时如果组件 A 想传递数据给组件 C 怎么办呢?

    如果采用第一种方法，我们必须让组件 A 通过 prop 传递消息给组件 B，组件 B 在通过 prop 传递消息给组件 C;要是组件 A 和组件 C 之间有更多的组件，那采用这种方式就很复杂了。从 Vue 2.4 开始，提供了![](img/3c0f853db986c9041a774f6c50772a92.png)listeners 来解决这个问题，能够让组件 A 之间传递消息给组件 C。

3.  v-model

    父组件通过 v-model 传递值给子组件时，会自动传递一个 value 的 prop 属性，在子组件中通过 this.$emit(‘input',val)自动修改 v-model 绑定的值

4.  provide 和 inject

    父组件中通过 provider 来提供变量，然后在子组件中通过 inject 来注入变量。不论子组件有多深，只要调用了 inject 那么就可以注入 provider 中的数据。而不是局限于只能从当前父组件的 prop 属性来获取数据，只要在父组件的生命周期内，子组件都可以调用。

5.  中央事件总线

    上面方式都是处理的父子组件之间的数据传递，那如果两个组件不是父子关系呢?也就是兄弟组件如何通信?

    这种情况下可以使用中央事件总线的方式。新建一个 Vue 事件 bus 对象，然后通过 bus.![](img/aac42761149677d978912c6dc0e1d8bb.png)on 监听触发的事件。

    ![](img/f2fe61e0a0dc29f38762f871f6d6cc00.png)

6.  parent 和 children

7.  boradcast 和 dispatch

    vue1.0 中提供了这种方式，但 vue2.0 中没有，但很多开源软件都自己封装了这种方式，比如 min ui、element ui 和 iview 等。 比如如下代码，一般都作为一个 mixins 去使用, broadcast 是向特定的父组件，触发事件，dispatch 是向特定的子组件触发事件，本质上这种方式还是 on 和 on 和 emit 的封装，但在一些基础组件中却很实用

8.  vuex 处理组件之间的数据交互

    如果业务逻辑复杂，很多组件之间需要同时处理一些公共的数据，这个时候才有上面这一些方法可能不利于项目的维护，vuex 的做法就是将这一些公共的数据抽离出来，然后其他组件就可以对这个公共数据进行读写操作，这样达到了解耦的目的

#### 1.17 vue 组件间传值， attrs 和 listeners 了解过吗？

**参考答案：**

![](img/3c0f853db986c9041a774f6c50772a92.png)listeners 的作用：解决多层嵌套情况下，父组件 A 下面有子组件 B，组件 B 下面有组件 C，组件 A 传递数据给组件 B 的问题，这个方法是在 Vue 2.4 提出的。

![](img/3c0f853db986c9041a774f6c50772a92.png)listeners 解决问题的过程：

C 组件

```cpp
Vue.component('C',{ 
     template:` 
     <div> 
     <input type="text" v-model="$attrs.messageC" @input="passCData($attrs.messageC)"> 
     </div> 
     `, 
     methods:{ 
         passCData(val){ 
             //触发父组件 A 中的事件 
             this.$emit('getCData',val) 
         } 
     } 
}) 
```

B 组件

```cpp
Vue.component('B',{ 
 data(){ 
     return { 
         myMessage:this.message 
     } 
 }, 
 template:` 
 <div> 
 <input type="text" v-model="myMessage" @input="passData(myMessage)"> 
 <C v-bind="$attrs" v-on="$listeners"></C> 
 </div> 
 `, 
 //得到父组件传递过来的数据 
 props:['message'], 
 methods:{ 
     passData(val){ 
         //触发父组件中的事件 
         this.$emit('getChildData',val) 
     } 
 } 
}) 
```

A 组件

```cpp
Vue.component('A',{ 
 template:` 
 <div> 
 <p>this is parent compoent!</p> 
 <B  
 :messageC="messageC"  
 :message="message"  
 v-on:getCData="getCData"  
 v-on:getChildData="getChildData(message)"> 
 </B> 
 </div> 
 `, 
 data(){ 
     return { 
         message:'Hello', 
         messageC:'Hello c' 
     } 
 }, 
 methods:{ 
     getChildData(val){ 
         console.log('这是来自 B 组件的数据') 
     }, 
     //执行 C 子组件触发的事件 
     getCData(val){ 
            console.log("这是来自 C 组件的数据："+val) 
     } 
 } 
}) 
var app=new Vue({ 
 el:'#app', 
 template:` 
 <div> 
 <A></A> 
 </div> 
 ` 
}) 
```

**解析：**

*   C 组件中能直接触发 getCData 的原因在于 B 组件调用 C 组件时 使用 v-on 绑定了$listeners 属性
*   通过 v-bind 绑定$attrs 属性，C 组件可以直接获取到 A 组件中传递下来的 props(除了 B 组件中 props 声明的)

​ ![](img/52b46288fcf038b4d40ec411f7a2832b.png)

#### 1.18 组建传值，事件总线是怎么用的

**参考答案：**

**中央事件总线**主要用来解决兄弟组件通信的问题。

实现方式：新建一个 Vue 事件 bus 对象，然后通过 bus.![](img/aac42761149677d978912c6dc0e1d8bb.png)on 监听触发的事件。

```cpp
Vue.component('brother1',{ 
 data(){ 
     return { 
        myMessage:'Hello brother1' 
     } 
 }, 
 template:` 
 <div> 
 <p>this is brother1 compoent!</p> 
 <input type="text" v-model="myMessage" @input="passData(myMessage)"> 
 </div> 
 `, 
 methods:{ 
     passData(val){ 
         //触发全局事件 globalEvent 
         bus.$emit('globalEvent',val) 
     } 
 } 
}) 
Vue.component('brother2',{ 
 template:` 
 <div> 
 <p>this is brother2 compoent!</p> 
 <p>brother1 传递过来的数据：{{brothermessage}}</p> 
 </div> 
 `, 
 data(){ 
     return { 
         myMessage:'Hello brother2', 
         brothermessage:'' 
     } 
 }, 
 mounted(){ 
      //绑定全局事件 globalEvent 
     bus.$on('globalEvent',(val)=>{ 
        this.brothermessage=val; 
     }) 
 } 
}) 
//中央事件总线 
var bus=new Vue(); 
var app=new Vue({ 
 el:'#app', 
 template:` 
 <div> 
 <brother1></brother1> 
 <brother2></brother2> 
 </div> 
 ` 
}) 
```

![](img/f2fe61e0a0dc29f38762f871f6d6cc00.png)

#### 1.19 vue 生命周期中异步加载在 mouted 还是 create 里实现

**参考答案:**

最常用的是在 created 钩子函数中调用异步请求

**解析：**

一般来说，可以在，created，mounted 中都可以发送数据请求，但是，大部分时候，会在 created 发送请求。
Created 的使用场景：如果页面首次渲染的就来自后端数据。因为，此时 data 已经挂载到 vue 实例了。
在 created（如果希望首次选的数据来自于后端，就在此处发请求）（只发了异步请求，渲染是在后端响应之后才进行的）、beforeMount、mounted（在 mounted 中发请求会进行二次渲染） 这三个钩子函数中进行调用。
因为在这三个钩子函数中，data 已经创建，可以将服务端端返回的数据进行赋值。但是**最常用的是在 created 钩子函数中调用异步请求**，因为在 created 钩子函数中调用异步请求有两个优点：
第一点：能更快获取到服务端数据，减少页面 loading 时间；
第二点：放在 created 中有助于一致性，因为 ssr 不支持 beforeMount 、mounted 钩子函数。

#### 1.20 vue 钩子函数(重点问了 keep-alive)

**参考答案：**

![](img/f0d4efd4fb5ef18587586a2fcaa74083.png)

Vue 生命周期经历哪些阶段：

1.  总体来说：初始化、运行中、销毁
2.  详细来说：开始创建、初始化数据、编译模板、挂载 Dom、渲染→更新→渲染、销毁等一系列过程

生命周期经历的阶段和钩子函数:

1.  实例化 vue(组件)对象：new Vue()

2.  初始化事件和生命周期 init events 和 init cycle

3.  beforeCreate 函数：

    在实例初始化之后，数据观测 (data observer) 和 event/watcher 事件配置之前被调用。

    即此时 vue（组件）对象被创建了，但是 vue 对象的属性还没有绑定，如 data 属性，computed 属性还没有绑定，即没有值。

    此时还没有数据和真实 DOM。

    即：属性还没有赋值，也没有动态创建 template 属性对应的 HTML 元素（二阶段的 createUI 函数还没有执行）

4.  挂载数据（属性赋值）

    包括 属性和 computed 的运算

5.  Created 函数：

    vue 对象的属性有值了，但是 DOM 还没有生成，$el 属性还不存在。

    此时有数据了，但是还没有真实的 DOM

    ​ 即：data，computed 都执行了。属性已经赋值，但没有动态创建 template 属性对应的 HTML 元素，所以，此时如果更改数据不会触发 updated 函数

    ​ 如果：数据的初始值就来自于后端，可以发送 ajax，或者 fetch 请求获取数据，但是，此时不会触发 updated 函数

    6.  检查

​ 6.1 检查是否有 el 属性
​ 检查 vue 配置，即 new Vue{}里面的 el 项是否存在，有就继续检查 template 项。没有则等到手动绑定调用 vm.![](img/ce5a8e349fdc0baf9f19a1353d9fc1cc.png)el 的绑定。

​ 6.2 检查是否有 template 属性

​ 检查配置中的 template 项，如果没有 template 进行填充被绑定区域，则被绑定区域的 el 对 outerHTML（即 整个#app DOM 对象，包括

和标签）都作为被填充对象替换掉填充区域。即： 如果 vue 对象中有 template 属性，那么，template 后面的 HTML 会替换$el 对应的内容。如果有 render 属 性，那么 render 就会替换 template。 即：优先关系时： render > template > el

7.  beforeMount 函数：

    模板编译(template)、数据挂载(把数据显示在模板里)之前执行的钩子函数

    此时 this.$el 有值，但是数据还没有挂载到页面上。即此时页面中的{{}}里的变量还没有被数据替换

8.  模板编译：用 vue 对象的数据（属性）替换模板中的内容

9.  Mounted 函数：

    模板编译完成，数据挂载完毕

    即：此时已经把数据挂载到了页面上，所以，页面上能够看到正确的数据了。

    一般来说，我们在此处发送异步请求（ajax，fetch，axios 等），获取服务器上的数据，显示在 DOM 里。

10.  beforeUpdate 函数：

    组件更新之前执行的函数，只有数据更新后，才能调用（触发）beforeUpdate，注意：此数据一定是在模板上出现的数据，否则，不会，也没有必要触发组件更新（因为数据不出现在模板里，就没有必要再次渲染）

    数据更新了，但是，vue（组件）对象对应的 dom 中的内部（innerHTML）没有变，所以叫作组件更新前

11.  updated 函数：

    组件更新之后执行的函数

    vue（组件）对象对应的 dom 中的内部（innerHTML）改变了，所以，叫作组件更新之后

12.  activated 函数：keep-alive 组件激活时调用

13.  activated 函数：keep-alive 组件停用时调用

14.  beforeDestroy：vue（组件）对象销毁之前

15.  destroyed：vue 组件销毁后

keep-alive

`<keep-alive></keep-alive>`包裹动态组件时，会缓存不活动的组件实例,主要用于保留组件状态或避免重新渲染。

**解析：** 比如有一个列表和一个详情，那么用户就会经常执行打开详情=>返回列表=>打开详情…这样的话列表和详情都是一个频率很高的页面，那么就可以对列表组件使用`<keep-alive></keep-alive>`进行缓存，这样用户每次返回列表的时候，都能从缓存中快速渲染，而不是重新渲染