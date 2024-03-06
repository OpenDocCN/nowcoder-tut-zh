# 第三章 第 4 节 前端方向优质面经合集（下）

 # 篇四：字节跳动架构部实习+秋招提前批共八轮面试（均已拿 offer）

**牛油：@洛霞**

先进行说明一下，本人刚刚大三结束，去年十二月的时候是投递了字节的视频架构的实习，共三轮技术面+一轮 hr 面，成功拿到 offer 实习了五个月。今年秋招提前批是投了抖音架构，共三轮技术面+一轮 hr 面，已经成功拿到 offer。由于去年实习面经没有在牛客上发，所以跟着本次提前批面经一共八轮面试一起发了。面经更多的记录我当时面试的思考和一些相关问题回答的切入点，可能会跟其他面经直接给答案有所不同，其实掌握了切入点再进行扩展就是一份好的回答了~

**实习一面（50min）**

面试我的的是一个二十出头的小哥哥，第一次面试我还是比较紧张的，还好小哥哥待人和蔼可亲，针对不会的问题并没有刁难我，反而给我不少提示（给点个赞）

首先是自我介绍，问了下我学习前端的途径和方法啥的，算是暖了个开场吧，然后是具体的问题。

1\. ES6 新特性说一下？

我首先说的是 let、const，学长就直接让我详细说一下这两个。let、const 有三个方面可说：

1.1 let、const 声明的变量只在其所在的块代码内有效，隐式截取了作用域，有了块级作用域的概念。

1.2 暂时性死区，变量声明提升但在不会被赋值为`undefined`，不能在声明之前使用。

1.3 不允许重复声明。

然后给了两个例子说一下：

const a = 1

const a = 2

console.log(a)

const b = []

b.push(1)

console.log(b)

2\. ES6 之前的模块引入方式和区别

ES6 之前模块引入主要是 CommonJS 和 AMD 两种。

然后学长让说明一下 CommonJs 和 ES6 模块引入的区别：

2.1. 首先，CommonJS 导出值是**浅拷贝**，一旦输出某个值，模块内部的变化就影响不到这个值。而 ES6 导出是采用实时绑定的方式，是将其内存地址导出，导入是动态加载     模块取值，并且变量总是绑定其所在的模块，不能重新赋值。

2.2\. ES6 模块化导入是异步导入，CommonJS 导入是同步导入。这跟 ES6 模块通常用于 web 端，而 CommonJS 用于服务器端有关。

2.3\. CommonJS 导入支持动态导入 require(`${path}/xx.js`)，ES6 模块化导入不支持，目前已有草案。

2.4\. ES6 模块化会编译成 require/exports 来执行的。

3. 一道数组遍历

实现`[1,2,3] => [2,4,6]`，这个太简单了：

function fn(arr) {

return arr.map(item => item * 2)

}

4. 字符串算法题

对输入的字符串，去除其中的字符'b'以及连续出现的'a'和'c'，例如：

  'aacbd' -> 'ad'

  'aabcd' -> 'ad'

  'aaabbccc' -> ''

可以使用正则：

function fn(str) {

  let res = str.replace(/b+/g, '');

  while(res.match(/(ac)+/)) {

    res = res.replace(/ac/, '')

  }

  return res;

}

5\. CSS9 宫格

问题：创建出 CSS9 宫格，3*3 宫格，每个宫格的长宽未知，要求达到自适应并且精确分配。

学长特意说要精确分配，所以用 1/3 肯定是不行的，于是使用 flex 布局：

<!--HTML-->

<div class="container">

  <div class="wrapper">

    <div class="item"></div>

    <div class="item"></div>

    <div class="item"></div>

  </div>

  <div class="wrapper">

    <div class="item"></div>

    <div class="item"></div>

    <div class="item"></div>

  </div>

  <div class="wrapper">

    <div class="item"></div>

    <div class="item"></div>

    <div class="item"></div>

  </div>

</div>

<!--CSS-->

.container {

  display: flex;

  flex-direction: column;

  flex-wrap: nowrap;

}

.wrapper {

  display: flex;

  flex: 1;

  flex-direction: row;

  flex-wrap: nowrap;

}

.item {

  flex: 1;

}

6. 说下 React 虚拟 DOM

虚拟 DOM 是用 JS 模拟的 DOM 结构，将 DOM 变化的对比放在 JS 来做。可以从虚拟 DOM 的两个优势点来说：

6.1 函数式的 UI 编程，即 UI 取决于数据。

6.2 打开了跨平台的大门。提供了统一的 JS 层对象，根据不同平台制定不同的渲染方式，带来了跨平台渲染的能力。

7. 说一下 diff 算法

创建一个 React 元素树之后，在更新的时候将创建一个新的 React 元素树，React 使用 Diff 算法对元素树进行比对，只更新发生了改变的部分，避免多余的性能消耗。

主要是三个思想，可以从这三个谈：

7.1. 永远只比较同层节点。

7.2. 不同的两个节点产生两个不同的树。

7.3. 通过 key 值指定哪些更新是相同的。

8\. NodeJS 中的事件循环：

process.nextTick(function() {

  console.log('a')

})

process.nextTick(function() {

  console.log('b')

})

setImmediate(function() {

  console.log('c')

  process.nextTick(function() {

    console.log('d')

  })

})

setImmediate(function() {

  console.log('e')

})

console.log('f')

输出顺序应该是：f -> a -> b -> c -> d -> e

这个值得注意的是 setImmediate 只能占用一次，这块 setImmediate 当时就回答错了。

9\. Promise 实现网络超时判断

使用 Promise 实现网络请求超时判断，超过三秒视为超时。

借助的是 Promise.race 这个方法，网络请求和三秒定时器进行竞争：

const uploadFile = (url, params) => {

  return Promise.race([

    uploadFilePromise(url, params),

    uploadFileTimeout(3000)

  ])

}

function uploadFilePromise(url, params) {

  return new Promise((resolve, reject) => {

    axios.post(url, params, {

      headers: {'Content-Type': 'multipart/form-data'}, // 以 formData 形式上传文件

      withCredentials: true

    }).then(res => {

      if(res.status===200 && res.data.code===0) {

        resolve(res.data.result)

      }else {

        reject(res.data)

      }

    })

  })

}

function uploadFileTimeout(time) {

  return new Promise((resolve, reject) => {

    setTimeout(() => {

      reject({timeoutMsg: '上传超时'})

    }, time)

  })

}

**实习二面（50min）**

一面完等待 10min，二面就开始了。

1. 进程和线程

这是操作系统的问题，可以从四方面思考：

1.1. 线程是程序执行的最小单位，而进程是操作系统分配资源的最小单位。

1.2. 一个进程由一个或多个线程组成，线程是一个进程中代码的不同执行路线。

1.3. 进程之间相互独立，但同一进程下的各个线程之间共享程序的内存空间。

1.4. 调度和切换：线程上下文切换比进程上下文切换要快得多。

2. 说下操作系统中的死锁

死锁是指两个或两个以上的进程在执行过程中,因争夺资源而造成的一种互相等待的现象,若无外力作用,它们都将无法推进下去.此时称系统处于死锁状态或系统产生了死锁,这些永远在互相等待的进程称为死锁进程，两羊过独木桥。

死锁的解除与预防，资源的合理分配。。

这一块答的不是很好。。。

3. 说一下链表类型

单向链表、双向链表、循环链表

4. 栈和队列的区别？

栈是后进先出的数据结构，常用的比如调用栈。

队列是先进先出的数据结构，常用的就是 NodeJS 和浏览器中的任务队列了。

5. 遍历一个二叉树所有节点，返回它们的和

function numSum(root) {

    if(!root) return 0;

    return root.val + numSum(root.left) + numSum(root.right);

}

6. 连续自串最大和

给出一个数组，求出连续元素组成子串和的最大值：

输入: [-2,1,-3,4,-1,2,1,-5,4],

输出: 6

双层循环的话时间复杂度是： O(n * n), 使用分治的话时间复杂度可以压缩到 O(n * logn)，问了下学长说直接用笨方法写一下，给出代码：

function numSum(arr) {

  if(!arr.length) return null;

  if(arr.length < 2) return arr[0];

  let sumMax = arr[0];

  let sumTemp;

  for(let i = 0; i < arr.length; i++) {

    sumTemp = arr[i];

    if(sumTemp > sumMax) sumMax = sumTemp;

    for(let j = i + 1; j < arr.length; j++) {

      sumTemp += arr[j];

      if(sumTemp > sumMax) sumMax = sumTemp;

    }

  }

  return sumMax;

}

7. 介绍一下你的项目？

略。。。

8. 微信小程序的原理说一下？

从微信小程序的四层说起：JS、WXML、WXSS、JSON。然后可以分析一下小程序的 webview 和 jscore 部分。

**实习三面（1h）**

然后紧接着就是三面压力面，也是我入职的部门 leader。

1. 列举下 ES6 新特性并简要说明一波

可以列举一下以下几大块：

1.1 let、const

1.2 解构赋值

1.3 字符串、正则、数值、函数、数组、对象的扩展

1.4 symbol

1.5 Set、Map

1.6 Promise

1.7 Generator

1.8 async

1.9 class

1.10 修饰器

1.11 module

2\. Generator 实现一个自执行

Generator 自执行就是著名的自执行模块 co，可以使用递归思想，不停地的调用 next 方法。

3. 说一下微信小程序的生命周期

这个我没温习到，没有回答完整哈~

页面生命周期：

- onLoad // 页面创建时执行

- onShow // 页面出现在前台执行

- onReady // 页面首次渲染完毕时执行

- onHide // 页面从前台变为后台时执行

- onUnload // 页面销毁时执行

- onPullDownRefresh // 触发下拉刷新时执行

- onReachBottom // 页面触底时执行

- onPageScroll // 页面滚动时执行

- onResize // 页面尺寸变化时执行

- onTabItemTap // tab 点击时执行

组件声明周期(在 lifetimes 字段内进行声明)：

- created // 在组件实例刚刚被创建时执行

- attached // 在组件实例进入页面节点树时执行

- ready // 在组件在视图层布局完成后执行

- moved // 在组件实例被移动到节点树的另一个位置时执行

- detached // 在组件实例被从页面节点移除时执行

- error // 每当组件方法抛出错误时执行

4. 说一下你会的 React 技术栈

略。。。

5\. React-Router 实现简单路由

import { BroserRouter, Route, Switch, Redirect } from 'react-router-dom'

<BroserRouter>

  <Switch>

    <Route path='/login' exact component={Login} />

    <Route path='/' render={() => (

      <Switch>

        <Route paht='/home' component={Home} />

        <Redirect to='/home' />

      </Switch>

    )} />

  </Switch>

</BroserRouter>

6. 说一下 Redux 原理

Redux 就是一个数据状态管理，React 单向数据流带来的数据跨组件传递问题。

简要说下 Redux 数据流，store、action、reducer，搭配 React 的使用。

可以有空看下源码，内容不是很多。

7. 说一下你的项目

略。。。（遵循 STAR 原则）

8. 说一下 Koa 洋葱模型

Koa 洋葱模型指的就是：一个请求从外到里一层一层地经过中间件，响应时从里到外一层一层地经过中间件。

观看 Koa 源码，你会发现：Koa 中间件被存储在 middleware 数组中，实现洋葱模型关键依赖于 koa-compose，分析一波 koa-compose 的源码：

'use strict'

module.exports = compose

function compose (middleware) {

  // 类型判断 middleware 必需是一个函数数组

  if (!Array.isArray(middleware)) throw new TypeError('Middleware stack must be an array!');

  for (const fn of middleware) {

    if (typeof fn !== 'function') throw new TypeError('Middleware must be composed of functions!');

  }

  // 返回一个函数，接受 context 和 next 参数，koa 在调用 koa-compose 时只传入 context，所以此处 next 为 undefined

  return function (context, next) {

    // last called middleware #

    // 初始化 index

    let index = -1;

    // 从第一个中间件开始执行

    return dispatch(0);

    function dispatch (i) {

      // 在一个中间件出现两次 next 函数时，抛出异常

      if (i <= index) return Promise.reject(new Error('next() called multiple times'));

      // 设置 index，作用是判断在同一个中间件中是否调用多次 next 函数

      index = i;

      // 中间件函数

      let fn = middleware[i]

      // 跑完所有中间件时，fn=next，即 fn=undefined，可以理解为终止条件

      if (i === middleware.length) fn = next

      // fn 为空时，返回一个空值的 promise 对象

      if (!fn) return Promise.resolve();

      try {

        // 返回一个定值的 promise 对象，值为下一个中间件的返回值

        // 这里时最核心的逻辑，递归调用下一个中间件，并将返回值返回给上一个中间件

        return Promise.resolve(fn(context, dispatch.bind(null, i + 1)));

      } catch (err) {

        return Promise.reject(err)

      }

    }

  }

}

9. 实现一个 Koa 中间件

看懂洋葱模型的源码，中间就很简单了，中间件都遵循这个结构：

module.exports = function(options) {

  // 配置处理

  return async (ctx, next) => {

    // 中间件逻辑...

  }

}

中间件就是一个函数，接受两个函数：ctx（全局上下文）、next（调用下一个中间件的方法）

10. 参与比赛项目中，你自我感觉发挥比较重要的一个项目？

略。。。

**实习 hr 面（40min）**

四面是 hr 面，聊了一下选择前端的原因和对于前端发展的看法，平时的学习途径和学习方法，大学校园生活、参加的一些比赛和活动，对于考研和就业的一些看法，别人对你的看法，随后也是顺利拿到了字节的实习 offer。字节的面试官还是比较善解人意的，对于没有思路的问题会引导着你去思考而不是以难倒人为乐趣，算法题嘛整体也是比较简单的，多关注于实际实现和知识基础。面试的节奏也是比较快的，三轮技术面基本都是连着面中间隔个 15min。也可以看到字节对于基础知识是比较看重的，知识基础还是需要多看，还有一些框架的源码和原理看了的话也是加分很多的。三面的效果并不是很理想，但最后还是成功拿到了实习 offer，还是比较幸运的。

**提前批一面（1h）**

1. 跨域说一下

可以从跨域的原因和解决跨域的方法说。

1.1 跨域主要是由于浏览器的同源策略，是对跨域响应进行了拦截。

1.2 跨域的解决方法有 JSONP、CORS、反向代理等等，具体说一说。

2. 输入 URL 发生了什么

寻找强缓存 -> 构建请求 -> DNS 解析 -> 建立 TCP 连接 -> 发送 HTTP 请求 -> 响应（200/304 协商缓存） -> 构建 DOM 树 -> 构建 CSSOM 树 -> 声称布局树 -> 建图层树 -> 生成绘制列表 -> 生成图块 -> 显示器显示

3. 重绘和回流说一下

重绘、回流的触发条件、过程，可以针对此做的优化策略。

4\. new 操作符进行的操作

以构造函数.prototype 为原型创建空对象，将构造函数的 this 绑定到建立的空对象并执行，结果是引用数据类型则返回结果否则返回创建的对象。

5. 说一下 CommonJS 和 ES 模块化的区别

这个实习问过，答案在上面就不说了。

接着算法题：

6. 版本号排序

输入 ['1.1.1.1.1.1', '6', '5.4.3', '2.3.1', '2.3.1.1'] 返回从大到小的版本号数组

我的写法是将每一个版本号以.分隔为数组，然后从第一位往后比较。

7. 判断一个链表是否有环。

快慢指针即可。

8. 实现一个类的 add 方法，使得同时的并行请求只有两个，并行请求完成后自动执行下一个任务直到全部执行完。

class Scheduler {

    async add(promiseFunc) {

    }

}

const scheduler = new Scheduler()

const timeout = (time) => {

    return new Promise(r => setTimeout(r, time))

}

const addTask = (time, order) => {

    scheduler.add(() => timeout(time)).then((res) => console.log(res))

}

addTask(1000, 1)

addTask(500, 2)

addTask(300, 3)

addTask(400, 4)

这个解法我详细总结过，可以查看：[`juejin.im/post/5f194ab1e51d45346d310b30`](https://juejin.im/post/5f194ab1e51d45346d310b30)

9. 使用 Koa 实现请求-响应的时间监听

**提前批二面（1h）**

上面先是两道题：

1. ES5 实现数组去重，要求时间复杂度是 O(n). [1, 2, 2, '1', 'test'] => [1, '1', 'test'] 要求可以区分到 1 和'1'
我问了是否可以使用额外空间，面试官回答是可以的。那就定义一个 hash 对象，遍历存入，key 值的话需要添加 typeof，用来区分 1 和''1'

2\. Promise 并行

[promiseGenerator]

promiseGenerator => Promise

dispatch(arr, n) {

}

// 实现 dispatch 方法

// 要求：并行限制 2 个

跟上一面的思路差不多

3. 简历上的实习项目

这个过程很长，问的东西也很多，所以多准备准备自己的项目吧。

4\. HTTPS 相对于 HTTP 做了些什么？

从 SSL+数字签名这两个方面说。

5\. HTTP2.0 的优化？

头部压缩、多路复用、服务器推送这三个方面详细回答。

6. 说一下为什么 HTTP1 有队头阻塞的问题

7\. HTTP2.0 实现了服务器主动 PUSH，那么如果服务端文件(html)没有发生改变，那么此时还会主动 PUSH，如何解决这个问题？

这个我有点懵，因为面试官对于服务端主动推送问的很深，我不会，他回答是服务端可以主动释放。

8\. React 看你比较熟悉，说一下？

可以说一下解决的问题、优势。

9. 看过相关的源码没，说一下印象深刻的地方？

10\. Virtual DOM 的优势

函数式 UI 编程、跨平台。

11\. webpack 的原理说一下？

其实就是依赖关系图的构建过程

12\. webpack 自己实现过 loader 或 plugin 吗？

13\. webpack loader 和 plugin 的区别？

**提前批三面（35min）**

三面进行的时间比较短，首先是围绕项目进行的一些提问，这个持续的时间比较长。

然后其他没问，直接给了两道题：

1. 一个非常长的非递减数组，找出一个数出现的次数。比如：[1, 2, 3, 3, 4, ......, 45, 45, 69, ......, 1000]找出 80 出现的次数

使用二分查找，找出这个数字出现的索引，然后向左向右扩展。

2. 同花顺扑克牌问题

将扑克牌去除大小王，剩余 1~13 的黑桃、红桃、梅花、方块，1~13 黑桃编号 1～13，1～13 红桃编号 14～26，一次类推，求解取出 5 张牌是同花顺（同一颜色，顺子）

解决方法很简单，先排序，最大与最小和需要是 4，然后判断最小或最大是否在指定区间。

const fn = arr => {

  if (!arr || arr.length !== 5) return false;

  arr.sort((a, b) => a - b);

  if (arr[4] - arr[0] !== 4) return false;

  if (arr[0] % 13 >= 9) return false;

  return true;

}

三面进行的很简单时间也很短，主要是项目，算法也很简单，应该是前两面反馈比较好就没过多问什么问题。

**提前批 hr 面（40min）**

三面完就约了当天进行 hr 面，不得不说字节的效率，hr 面是个可爱的小姐姐~

主要沟通了一些工作、实习、学校、学习方面的事情吧。

为什么实习离职了，而不是留下来转正？

你觉的你的优势在哪里？

你觉得你的缺点在哪里？

你对薪资待遇有什么要求？

薪资在你心中的地位和分量？

实习期间对于公司的工作时间怎么看的？能接受吗？

对于加班怎么看待的呢？

你未来两三年的职业规划？

你是会学习一些后端、人工智能，还是专注于前端？

平时学习的方式？

大概是有这些问题，当时还有其他问题想不起来了，沟通了四十分钟过程还是挺 OK 的。

反问环节我问她怎么看待印度最近封杀了抖音海外版的情况，她笑了笑说说明了公司这方面的工作，然后通知我三个工作日 offer 会到。

如今意向书到了也快有一星期了，也祝愿大家都能拿到心仪的 offer~

整理不易，求个点赞关注~

# 篇五：前端秋招面经

**牛油：****@bbln**

## 个人情况

本硕 211，通信专业，前端岗位，秋招拿到阿里本地生活，字节抖音互娱，美团，滴滴，快手，拼多多，贝壳找房等大厂 offer，感觉自己还是比较幸运的，9 月 7 号拿了阿里的意向书以后也就正式上岸了。😁秋招期间牛客的各种帖子对自己帮助很大，因此自己也整理一份面经，回馈牛客，希望对大家有帮助。（后续也会发一个 offer 比较选择的帖子希望大家帮忙投个票给个建议😁）

![](img/6d38f562fe291a49d4a2e87316de2557.png)![](img/4455aa96bdd7ff525db157544368625f.png)

前端基础知识由于太多啦，所以就不整理啦，不过在牛客上看到一篇比较好的帖子，介绍的也挺全的，分享给大家：[`www.nowcoder.com/discuss/258810`](https://www.nowcoder.com/discuss/258810)都可以参考一下~由于好多公司都比较注重手撕代码，包括数据结构和一些 js 的底层实现，所以我把我面试碰到的和一些别人面试碰到的比较高频的 ***手撕题***整理一下，希望对大家有帮助。注：后面的答案仅供参考，可以根据自己的代码风格自行整理，这样有助于记忆。

★★★：高频，很重要

★★ ：中频，最好掌握

★ ：了解即可

## JS

1.  模拟实现函数的 call、apply、bind 方法    ★★
2.  模拟实现函数节流、防抖方法        ★★★
3.  模拟实现对象的深拷贝        ★★
4.  嵌套数组指定层次展开 flat 扁平化（多种方法，至少掌握两种）★★★
5.  模拟实现 reduce 数组方法    ★
6.  模拟实现数组 map 方法    ★★
7.  模拟实现 Array.fill()、Array.filter()    ★★
8.  模拟实现 Array.find()、Array.findIndex()★★
9.  模拟实现 Promise.all 方法(Promise.race 也需要了解)★★
10.  使用原生的 JavaScript 实现 ajax 请求（也可能让你说 ajax 实现原理，答案一样）★★★
11.  模拟实现构造函数 new 的过程（也可能让你口述 new 的过程，答案一样）★★★
12.  模拟实现 Object.create 方法    ★★
13.  模拟实现 instanceof 的功能        ★★★
14.  使用 setTimeout 实现 setInterval 方法 ★★
15.  实现 jsonp★★★
16.  promise 实现 sleep 函数★★
17.  实现 promise retry 重试★
18.  js 实现观察者模式★

## 数据结构和算法

1.  排序算法（要会分析时间空间复杂度）：冒泡、选择、插入、快排    ★★★ 归并     ★
2.  二分查找（非递归递归）★★★
3.  字符串逆序（翻转整数字符串）★★★
4.  数组乱序（打乱数组，至少掌握两种方法）★★★
5.  数组去重（至少掌握两种方法）★★★
6.  两个栈来实现一个队列（两个队列实现栈可以了解一下）★
7.  链表相关

**入门**：★★★

*   反转单链表
*   未排序链表去重 O（n2）
*   排序链表去重 O（n）
*   单链表删除节点
*   链表 partition
*   寻找链表倒数第 K 个节点
*   删除链表倒数第 N 个节点
*   判断链表是否为回文链表
*   判断链表是否有环
*   环形链表第一个入环节点
*   两个链表的第一个公共节点

**复杂**：

*   合并两个排序的链表★★
*   合并 K 个排序的链表★★
*   奇偶链表★
*   复制带随机指针的单向链表★
*   有序链表转换二叉搜索树（BST）★
*   二叉树展开为链表★
*   K 个一组翻转链表★

*   二叉树各种遍历（前中后序遍历，递归非递归，DFS,BFS）★★★*   二叉树遍历涉及到的一些算法题

（好多题其实就是二叉树深度或者广度非递归遍历稍微改一下即可）

*   前序和中序重建二叉树★★
*   BST 第 K 大的数和第 K 小的数★
*   二叉树按层求和（层序遍历改进）★
*   Z 字型（之字形）遍历二叉树★

*   二叉树深度相关

*   二叉树深度★★★
*   二叉树最小深度★★
*   树找两(叶子)节点最长距离（相隔的最长路径）★
*   判断二叉树是否为平衡二叉树★★
*   二叉树右视图（左）★

*   二叉树路径相关

*   路径总和 1★★★
*   路径总和 2（回溯法）★

*   DP

*   斐波那契数列★★★
*   最长公共子序列 LCS ★★
*   最长上升子序列 ★★
*   连续子数组（子串）的最大和★★
*   硬币找零（最少硬币个数）★★
*   0-1 背包问题★
*   完全背包问题（了解即可）★

*   全排列（回溯法）★

## CSS

1.  CSS 画各种图形（等腰三角形、等腰梯形、扇形、圆、半圆）    ★
2.  三列布局（至少掌握三种方法）    ★★★
3.  垂直水平居中（至少掌握三种方法）    ★★★

## 仅供参考：

## JS![](img/8298a3a8f799555808f687ef3f0574f4.png)

1.  模拟实现函数的 call、apply、bind 方法    ★★

    ```cpp
    /* 先将传入的指定执行环境的对象 context 取到
    将需要执行的方法(调用 call 的对象) 作为 context 的一个属性方法 fn
    处理传入的参数， 并执行 context 的属性方法 fn， 传入处理好的参数
    删除私自定义的 fn 属性
    返回执行的结果 */
    // 模拟 call 方法
    Function.prototype.defineCall = function (context) {
        context = context || window;
        context.fn = this; //this 指向 sayName 函数实例
        let args = [];
        for (let i = 1; i < arguments.length; i++) { //i 从 1 开始 
            args.push(arguments[i]);
        } //或者 args = [...arguments].slice(1);
        let result = context.fn(args.join(','));
        delete context.fn;
        return result;
    }
    let sayName = function (age) {
        console.log('current name: ' + this.name, "current age: " + age);
    }
    let obj1 = {
        name: "obj's name"
    }
    sayName.defineCall(obj1, 22); //this 指向 sayName 函数实例
    // current name: obj's name current age: 22

    // 模拟 apply 方法
    Function.prototype.defineApply = function (context, arr) {
        context = context || window;
        context.fn = this;
        let result;
        if (!arr) { // 第二个参数不传
            result = context.fn();
        } else { // 第二个参数是数组类型
            let args = [];
            for (let i = 0; i < arr.length; i++) { //i 从 0 开始
                args.push(arr[i]);
            }
            result = context.fn(args.join(','));
        }
        delete context.fn;
        return result;
    }
    let obj2 = {
        name: ['Tom', 'Johy', 'Joe', 'David']
    }
    sayName.defineApply(obj2, [3, 4, 5, 6, 7]);
    // current name: Tom,Johy,Joe,David current age: 3,4,5,6,7

    //用 call、apply 模拟实现 bind
    Function.prototype.bind = function (context) {
        let self = this; // 保存函数的引用
        return function () { // 返回一个新的函数
            // console.log(arguments);
            // return self.apply(context, arguments);
            return self.call(context, arguments);
        }
    };

    let obj = {
        name: 'seven'
    }

    let func = function () {
        console.log(this.name)
    }.bind(obj);
    func('zhangsan', 20);
    ```

2.  模拟实现函数节流、防抖方法        ★★★

    ```cpp
    function debounce(fn, delay) {//防抖
      // 维护一个 timer，用来记录当前执行函数状态
      let timer = null;

      return function() {
        // 通过 ‘this’ 和 ‘arguments’ 获取函数的作用域和变量
        let context = this;
        let args = arguments;
        // 清理掉正在执行的函数，并重新执行
        clearTimeout(timer);
        timer = setTimeout(function() {
          fn.apply(context, args);
        }, delay);
      }
    }
    let flag = 0; // 记录当前函数调用次数
    // 当用户滚动时被调用的函数
    function foo() {
      flag++;
      console.log('Number of calls: %d', flag);
    }

    // 在 debounce 中包装我们的函数，过 2 秒触发一次
    document.body.addEventListener('scroll', debounce(foo, 2000));

    function throttle(func, delay){//节流
      let timer = null;

      return function(){
        let context = this;
        let args    = arguments;
        if(!timer){
          timer = setTimeout(function(){
            func.apply(context, args);
            timer = null;
          }, delay);
        }
      }
    }
    ```

3.  模拟实现对象的深拷贝        ★★

    ```cpp
    //第一种：JSON.parse(JSON.stringify())方法实现深拷贝
    var obj={a:"hello",b:1,c:true,d:[1,2],e:{x:1,y:2},f:function(){console.log("copytest");},g:null,h:undefined};
    var copyobj=JSON.parse(JSON.stringify(obj));
    console.log(copyobj);
    copyobj.a="change";
    copyobj.d[0]=9;
    copyobj.e.x=8;
    console.log(obj);

    //第二种：递归的方法实现深拷贝
    function deepclone(obj,copyobj){
        var copyobj=copyobj||{};
        for (var keys in obj) { //使用 for..in 进行遍历   数组的话 keys 为 0,1..... keys 是 string 类型
            if(obj.hasOwnProperty(keys)){//剥离原型链的数据
                if((typeof(obj[keys])) ==='object' && obj[keys]!==null){//判断是否为引用数据类型
                    if (Object.prototype.toString.call(obj[keys]) === '[object Array]') { //Object 原型方法得到类型
                        copyobj[keys]=[];
                    }else{
                        copyobj[keys]={};
                    }
                    deepclone(obj[keys],copyobj[keys]);
                }else{
                    copyobj[keys]=obj[keys];
                }
            }
        }
        return copyobj;
    }
    ```

4.  嵌套数组指定层次展开 flat 扁平化（多种方法，至少掌握两种）★★★

    ```cpp
    // 嵌套数组指定层次展开   flat 扁平化
    // 1. 普通方法 递归
    function flattenMd() {
        let result = []
        return function flatten(arr) {//闭包
            arr.forEach(item => {
                if (Array.isArray(item)) {
                    flatten(item)
                } else {
                    result.push(item)
                }
            })
            return result
        }
    }
    // var ary = [1, [2, [3, [4, 5]]], 6]
    // flattenMd()(ary);  函数柯里化  部分求值

    //2.concat  与方法 1 类似 没用闭包
    function flattenMd(arr) {
        let result = [] // 利用函数作用域保存 result var result = []也可
        arr.forEach(item => {
            if (Array.isArray(item)) {
                result = result.concat(flattenMd(item))
            } else {
                result.push(item)
            }
        })
        return result
    }

    //3. reduce 
    function flattenMd(arr) { //.concat([3,4])和.concat(3,4)均可
        return arr.reduce((prev, item) => prev.concat(Array.isArray(item) ? flattenMd(item) : item), [])
    }

    // 4. 展开运算符
    function flattenMd(arr) {
        let flatten = arr => [].concat(...arr)//可去掉一层[]
        return flatten(arr.map(item => Array.isArray(item) ? flattenMd(item) : item))
    }

    // 5. join 和 split 组合（ 只适用字符串数组， 最简单粗暴）
    //[ '1', '2', '3', '4', '5', '6' ] 得到的是字符串数组  再转换一下才行
    function flattenMd(arr) {
        return arr.join().split(',')
        //join() 方法用于把数组中的所有元素放入一个字符串 默认用,隔开
    }
    ```

5.  模拟实现 reduce 数组方法    ★

    ```cpp
    //reduce（）函数接受两个参数，一个函数一个累积变量的初始值。
    //函数有四个参数：累计变量初值（默认第一个成员），当前变量值（默认第二个成员），当前位置，数组自身。
    //arr.reduce(function(prev, cur, index, arr){}, initialValue)
    Array.prototype.myReduce=function(fn,base){
        if(typeof fn !== 'function'){
            throw new TypeError("arguments[0] is not a function");//TypeError 是错误的类型之一：类型错误
        }

        var initialArr=this;//调用 myReduce()函数的当前对象
        var arr=initialArr.concat();//目的是返回一个等于初始数组的新数组，后面的操作都基于 arr，这样初始数组不会发生改动
        var index,newValue;

        if(arguments.length==2){
            arr.unshift(base);
            index = 0; //！！当前位置 指的是当前变量（第二个参数）针对调用该方法的数组位置即 initialArr
        }else{
            index=1;
        }

        if(arr.length===1){//长度为 1 直接返回
            newValue=arr[0];
        }

        while(arr.length>1){
            newValue=fn.call(null,arr[0],arr[1],index,initialArr);
            index++;
            arr.splice(0,2,newValue);//删除前两位 然后把累加值添加到第一位
        }
        return newValue;
    };
    ```

6.  模拟实现数组 map 方法    ★★

    ```cpp
    // 模拟实现 map
    // arr.map(function (currentValue, index, arr) {
    // })
    // currentValue 必须。 当前元素的值
    // index 可选。 当期元素的索引值
    // arr 可选。 当期元素属于的数组对象

    Array.prototype.newMap = function (fn) { //写法一
        var newArr = [];
        for (var i = 0; i < this.length; i++) {
            newArr.push(fn(this[i], i, this)) //this 指向调用 newMap 方法的数组
        }
        return newArr;
    }

    // arr.reduce((previousValue, currentValue, currentIndex, array) => {}, initialValue？)
    // reduce 若不指定初始值， 那么第一次迭代的 previousValue 为 ar[[0], currentValue 为 arr[1], currentIndex 为 1，
    // 若指定初始值， 那么第一次迭代的 previousValue 为 initialValue, currentValue 为 arr[0], currentIndex 为 0.

    Array.prototype.newMap = function (fn, Arg) { ////写法二：用数组的 reduce 方法实现数组的 map
        var res = [];
        this.reduce((prev, curr, index, array) => {
            res.push(fn.call(Arg, curr, index, array));
        }, 0) //指定初始值 initialValue=0，所以从 currentIndex=0 开始，即第一个开始  不这样会缺第一项，结果为[3,4]
        return res;
    }

    let arr = [1, 2, 3];
    let res = arr.newMap((a) => a + 1);
    console.log(res); //[2,3,4]
    ```

7.  模拟实现 Array.fill()、Array.filter()    ★★array.fill(value, start, end)value 必需。填充的值。start 可选。开始填充位置。end 可选。停止填充位置 (默认为 array.length)

    ```cpp
    Array.prototype.myFill = function (value, start = 0, end = this.length) {
        for (let i = start; i < end; i++) {
            this[i] = value;
        }
    }
    ```

    Array.filter()

    ```cpp
    Array.prototype.myFilter = function myFilter(fn, context) {
        if (typeof fn !== "function") {
            throw new TypeError(`${fn} is not a function`);
        }
        let arr = this;
        let temp = [];
        for (let i = 0; i < arr.length; i++) {
            let result = fn.call(context, arr[i], i, arr);
            if (result) temp.push(arr[i]);
        }
        return temp;
    };
    ```

8.  模拟实现 Array.find()、Array.findIndex()★★Array.find()用于找出第一个符合条件的数组成员，参数为一个回调函数[1, 4, -5, 10].find((n) => n < 0) // -5

    ```cpp
    Array.prototype.myFind = function (fn, start = 0, end = this.length) {
        for (let i = start; i < end; i++) {
            if (fn.call(this, this[i], i, this)) {
                return this[i]
            }
        }
    }
    ```

    Array.findIndex()

    ```cpp
    Array.prototype.myFindIndex = function (fn, start = 0, end = this.length) {
        for (let i = start; i < end; i++) {
            if (fn.call(this, this[i], i, this)) {
                return i
            }
        }
        return -1
    }
    ```

9.  模拟实现 Promise.all 方法(Promise.race 也需要了解)★★

    ```cpp
     //promise.all()
      function myall(proArr) {
        return new Promise((resolve, reject) => {
          let ret = []
          let count = 0
          let done = (i, data) => {
            ret[i] = data
            if(++count === proArr.length) resolve(ret)
          }
          for (let i = 0; i < proArr.length; i++) {
            proArr[i].then(data => done(i,data) , reject)
          }
        })
      }

    //promise.race();这么简单得益于 promise 的状态只能改变一次，即 resolve 和 reject 都只被能执行一次
     function myrace(proArr) {
        return new Promise(function (resolve, reject) {
          for(let i=0;i<proArr.length;i++){
            proArr[i].then(resolve,reject);
          }
        })
      }
    ```

10.  使用原生的 JavaScript 实现 ajax 请求（也可能让你说 ajax 实现原理，答案一样）★★★

    ```cpp
    //手写 3 遍以上
    var xhr = new XMLHttpRequest();// 创建 XMLHttqRequest
    var url = 'https://bbin.com';
    xhr.onreadystatechange=function(){// 监听状态码的变化，每次变化 均执行
        if(xhr.readyState===4){

            if (xhr.status === 200) { // 服务端 状态码
                console.log(xhr.responseText); //服务器返回的响应文本
            }else{
                console.error(xhr.statusText); //状态码的文本描述，如 200 的 statusText 是 ok
            }

        }
    }

    xhr.open('GET', url, true); // 初始化请求参数，还没发送请求   true 表示异步
    xhr.send(null); // 向服务器发送请求,但是不带有数据发送过去,一般在 get 方式发送时候多使用这个方式
    ```

11.  模拟实现构造函数 new 的过程（也可能让你口述 new 的过程，答案一样）★★★

    ```cpp
    function myNew(constructor,params){
      var args = [].slice.call(arguments);
      var constructor = args.shift();
      var obj = new Object();
      obj.__proto__ = constructor.prototype;
      var res = constructor.apply(obj,args);
      return (typeof res === 'object' && typeof res !== null) ? res : obj;
    }
    ```

12.  模拟实现 Object.create 方法    ★★

    ```cpp
    // 用于创建一个新对象,被创建的对象继承另一个对象(o)的原型
    function createObj(o) {//传入的参数 o 为返回实例的 __porto__,也就是实例构造函数的显示原型
        function F() {}//构造函数
        F.prototype = o;
        return new F();//返回实例
    }
    ```

13.  模拟实现 instanceof 的功能        ★★★

    ```cpp
    function instanceofObj(a, b) {
        // 模拟 a instanceof b
        let prototypeB = b.prototype;
        let protoA = a.__proto__;
        let state = false;
        while (true) {
            if (protoA == null) { // 可能是 undefined 
                state = false;
                break;
            }
            if (prototypeB === protoA) {
                state = true;
                break;
            }
            protoA = protoA.__proto__;
        }
        return state;
    }
    console.log(instanceofObj([], Array));
    instanceofObj([], Array); //true
    ```

14.  使用 setTimeout 实现 setInterval 方法 ★★

    ```cpp
    function mysetinterval(fn,time){
        console.log("利用 steTimeout 实现 setInterval");
        function interval(){//执行该函数，异步被挂起 time 时间后在执行，一上来就执行 fn
            setTimeout(interval,time);//异步
            //好，time 时间过去，这个异步被执行，而内部执行的函数正是 interval，就相当于进了一个循环，递归
            fn();//同步
        }
        setTimeout(interval,time);//interval 被延迟 time 时间执行
    }
    ```

15.  实现 jsonp★★★

    ```cpp
    var newscript=document.createElement('script');
    newscript.src='https://sp0.baidu.com/su?wd=Java&cb=foo';
    document.body.appendChild(newscript);
    function foo(data) { //callback 函数要绑定在 window 对象上
       console.log(data);
    }
    ```

16.  promise 实现 sleep 函数★★

    ```cpp
    async function test() {
        console.log('开始');
        await sleep(4000);
        console.log('结束');
    }

    function sleep(ms) {
        return new Promise(resolve => {
            setTimeout(resolve, ms);
        })
    }
    test();
    ```

17.  实现 promise retry 重试★实现函数 myGetData，也返回一个 promise，但是有失败重试功能

    ```cpp
    function myGetData(getData, times, delay) {//retry 函数
    return new Promise(function (resolve, reject) {
        function attempt() {
            getData().then(resolve).catch(function (erro) {
                console.log(`还有 ${times} 次尝试`)
                if (0 == times) {
                    reject(erro)
                } else {
                    times--
                    setTimeout(attempt(), delay)
                }
            })
        }
        attempt()
    })
    }
    ```

18.  js 实现观察者模式★

    ```cpp
    //ES6 实现观察者模式代码：（观察订阅模式）
    const queuedObservers = new Set();
    const observe = fn => queuedObservers.add(fn); // 依赖收集  Vue 中的 dep
    const observable = obj => new Proxy(obj, {set});// Proxy 和 Reflect 一一对应
    function set(target, key, value, receiver){
        const result = Reflect.set(target, key, value, receiver); // 执行 set
        queuedObservers.forEach(observe => observe()); // 派发更新
        return result;
    }

    // 使用 观察者模式实例
    const person = observable({
        name: 'Sun',
        age: 30
    });

    function print(){
        console.log(`name: ${person.name}, age: ${person.age}`);
    }
    observe(print);

    person.age = 31;
    // name: Sun, age: 31
    ```

## 数据结构和算法![](img/d1ccff7192644cf04ad6470443867354.png)

1.  排序算法（要会分析时间空间复杂度）：冒泡、选择、插入、快排    ★★★ 归并     ★
2.  二分查找（非递归递归）★★★

    ```cpp
    //二分查找 arr 为有序数组
    function binary_search(nums, target) { //非递归
    var left = 0;
    var right = nums.length - 1;
    while (left <= right) {
        mid = left + parseInt((right - left) / 2);
        if (target == nums[mid]) return mid;
        if (target < nums[mid]) {
            right = mid - 1;
        } else {
            left = mid + 1;
        }
    }
    return -1;
    };

    // 递归算法
    function binary_search(arr, low, high, key) {
        if (low > high) {
            return -1;
        }
        var mid = parseInt((high + low) / 2);
        if (arr[mid] == key) {
            return mid;
        } else if (arr[mid] > key) {
            high = mid - 1;
            return binary_search(arr, low, high, key);
        } else if (arr[mid] < key) {
            low = mid + 1;
            return binary_search(arr, low, high, key);
        }
    };
    ```

3.  字符串逆序（翻转整数字符串）★★★

    ```cpp
    //JavaScript 中可以使用 split 结合 数组自带的 reverse，先转成数组，再将数组拼接成字符串。
    var str = 'abc';
    var reverseStr = str.split('').reverse().join('');

    //经过此操作 str 还是不变
    //方法二：不使用 API
    var x = 501120;
    var digit;
    var ret = 0;
    var count = [];
    while (x > 0) {
        digit = x % 10;
        count.push(digit);
        ret = ret*10+digit;
        x = parseInt(x / 10);
    } 
    console.log(count.join(""));//'021105' 字符串类型
    console.log(ret);//21105 Number 类型
    ```

4.  数组乱序（打乱数组，至少掌握两种方法）★★★

    ```cpp
    // 方法 1：arr.sort((a,b) => Math.random() - 0.5);
    arr.sort(() => Math.random() - 0.5);// 使用 sort

    // 方法 2：时间复杂度 O(n²)
    function randomSortArray(arr) {
        let backArr = [];
        while (arr.length) {
            let index = parseInt(Math.random() * arr.length);
            backArr.push(arr[index]);
            arr.splice(index, 1);
        }
        return backArr;
    }

    // 方法 3：时间复杂度 O(n)
    function randomSortArray2(arr) {
        let lenNum = arr.length - 1;
        let tempData;
        for (let i = 0; i < lenNum; i++) {
            let index = parseInt(Math.random() * (lenNum + 1 - i));
            tempData = a[index];
            a[index] = a[lenNum - i]// 随机选一个放在最后
            a[lenNum - i] = tempData;
        }
        return arr;
    }
    ```

5.  数组去重（至少掌握两种方法）★★★

    ```cpp
    //方法 1：普通版  
    function arrayUnique(arr){
      let res = [];
      for(let i=0,len=arr.length;i< len;i++){
          let item = arr[i];
          (res.indexOf(item) === -1) && res.push(item);// res 中没有该数才 push 去重
      }
      return res;
    }
    // 方法 2：hashMap
    function arrayUnique2(arr) {
      let res = [];
      let hash = {};
      for (let i = 0; i < arr.length; i++) {
        let item = arr[i];
        let key = typeof(item) + item;
        if (hash[key] !== 1) {
          res.push(item);
          hash[key] = 1;
        }
      }
      return res;
    }

    //方法 3：使用 Set 进行数组去重
    // 使用 Set 完成 数组去重,只能去除字符串和数字的重复，不能去除对象的重复  
    function uniqueSet(array) {
      return Array.from(new Set(array));
    }:
    function uniqueSet2(array) {
      return [...new Set(array)];
    }

    // 方法 4：使用 filter 的版本
    function unique(a) {
      var res = a.filter(function(item, index, array) {
        return array.indexOf(item) === index;// 每次都是从左边开始做找
      });
      return res;
    }

    // 方法 5：原型方法版本
    Array.prototype.unique = function () {
      const newArray = [];
      this.forEach(item => {
        if (newArray.indexOf(item) === -1) {
          newArray.push(item);
        }
      });
      return newArray;
    }

    // 方法 6：精简版 使用 filter 和 indexOf 结合实现
    Array.prototype.unique = function () {
      return this.filter((item, index) => {
        return this.indexOf(item) === index;
      })
    }
    ```

6.  两个栈来实现一个队列（两个队列实现栈可以了解一下）★

    ```cpp
    //用两个栈来实现一个队列，完成队列的 Push 和 Pop 操作。
    var stackPush = [];
    var stackPop = [];

    function push(node) {
        // write code here
        stackPush.push(node);
    }

    function pop() {
        if (stackPop.length==0 && stackPush.length==0) {
            console.log("Queue is empty!");
        } else if (stackPop.length==0) {
            while (!stackPush.length==0) {
                stackPop.push(stackPush.pop());
            }
        }
        return stackPop.pop();
    }
    ```

7.  链表相关入门：★★★

    *   反转单链表

        ```cpp
        var reverseList = function(head) {
            //let [prev, curr] = [null, head];
            let prev=null;
            let curr=head;
            while (curr) {
                let tmp = curr.next;    // 1. 临时存储当前指针后续内容
                curr.next = prev;       // 2. 反转链表
                prev = curr;            // 3. 接收反转结果
                curr = tmp;             // 4. 接回临时存储的后续内容
            }
            return prev;
        };
        ```

    *   未排序链表去重 O（n2）

        ```cpp
        //移除未排序链表中的重复节点。保留最开始出现的节点。（两层循环）
         //输入：[1, 2, 3, 3, 2, 1]
         //输出：[1, 2, 3]
        var removeDuplicateNodes = function (head) {
            var p = head;
            while (p) {
                var q = p;
                while (q.next) {
                    if (q.next.val == p.val) {
                        q.next = q.next.next;
                    } else {
                        q = q.next;
                    }
                }
                p = p.next;
            }
            return head;
        };
        ```

    *   排序链表去重 O（n）

        ```cpp
        var deleteDuplicates = function(head) {
            let current = head //把首节点指针赋值给 current
            while(current && current.next) { //当前节点以及下一节点不为空时
                if(current.val === current.next.val) { //值相等
                    current.next = current.next.next
                }else {
                    current = current.next //值不相等
                }
            }
            return head //返回首节点
        }
        ```

    *   单链表删除节点

        ```cpp
        //给定单向链表的头指针和一个要删除的节点的值，定义一个函数删除该节点。
        //返回删除后的链表的头节点。
        var deleteNode = function(head, val) {
            let pre = new ListNode(0); // 额外添加头节点 哨兵节点 考虑要删除的节点在第一个
            pre.next = head;

            let node = pre;
            while (node.next) {
                if (node.next.val === val) {
                    node.next = node.next.next;
                    break;
                }
                node = node.next;
            }
            return pre.next;
        };
        ```

    *   链表 partition

        ```cpp
        //思路：一拆为二然后合并
        function partition(head, x) {
        var dummy1 = new ListNode(-1); //辅助节点
        var dummy2 = new ListNode(-1); //辅助节点
        var p1 = dummy1;
        var p2 = dummy2;
        var p = head;
        while (p != null) {
            if (p.val < x) {
                p1.next = p;
                p1 = p1.next;
            } else {
                p2.next = p;
                p2 = p2.next;
            }
            p = p.next;
        }
        if (dummy1.next == null) {
            return head;
        } else {
            p1.next = dummy2.next;
            p2.next = null; //以 null 结尾
            return dummy1.next;
        }
        }
        ```

    *   寻找链表倒数第 K 个节点

        ```cpp
        //给定一个链表: 1->2->3->4->5, 和 k = 2.
        //返回链表 4->5.
        var getKthFromEnd = function(head, k) {
        //用两个指针来跑，两个指针中间相距 k-1 个节点,第一个指针先跑，跑到了第 k 个节点时，第二个指针则是第一个节点。
        //这时候两个一起跑。当第一个跑到了最后一个节点时，这时候第一个指针则是倒数第 k 个节点。
          if (head === null || k <= 0) return null;
          let pNode1 = head,pNode2 = head;
          while (--k) {
            if (pNode2.next !== null) {
              pNode2 = pNode2.next;
            } else {
              return null;
            }
          }
          while (pNode2.next !== null) {
            pNode1 = pNode1.next;
            pNode2 = pNode2.next;
          }
          return pNode1;
        };
        ```

    *   删除链表倒数第 N 个节点

        ```cpp
        //给定一个链表: 1->2->3->4->5, 和 n = 2.
        //当删除了倒数第二个节点后，链表变为 1->2->3->5.

        var removeNthFromEnd = function(head, n) {
            let preHead = new ListNode(0)
            preHead.next = head
            let fast = preHead, slow = preHead
            // 快先走 n+1 步
            while(n--) {
                fast = fast.next
            }
            // fast、slow 一起前进
            while(fast && fast.next) {
                fast = fast.next
                slow = slow.next
            }
            slow.next = slow.next.next
            return preHead.next
        };
        ```

    *   判断链表是否为回文链表

        ```cpp
        //方法 1：
        var isPalindrome = function(head) {
          let nums = [];
          while (head){
            nums.push(head.val);
            head = head.next;
          }
          while(nums.length > 1){//注意是大于 1  考虑基数个
            if(nums.pop() != nums.shift()) return false;
          }
          return true;
        };
        //方法 2：双指针法/////////////////
        var isPalindrome = function(head) {
            let slow=head,fast=head;
            while(fast && fast.next){
                slow = slow.next;
                fast = fast.next.next;
            }
            //反转慢指针 翻转后半段
            let resverse = null;
            while(slow !== null){
                let next = slow.next;
                slow.next = resverse;
                resverse = slow;
                slow = next;
            }
            while(resverse !== null){
                if(resverse.val !== head.val) return false;
                resverse = resverse.next;
                head = head.next;
            } 
            return true;
        };
        ```

    *   判断链表是否有环

        ```cpp
        //给定一个链表，判断链表中是否有环。
        var hasCycle = function(head) {
            if(!head || !head.next) return false
            let slow = head
            let fast = head.next
            while(slow != fast){
                if(!fast || !fast.next) return false
                fast = fast.next.next
                slow = slow.next
            }
            return true
        };
        ```

    *   环形链表第一个入环节点

        ```cpp
        //环形链表第一个入环节点
        //给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。
        var detectCycle = function(head) {
            if(!head || !head.next) return null
            let slow = head;
            let fast = head;
            while(fast != null && fast.next != null) {
                slow = slow.next;
                fast = fast.next.next;
                if(slow == fast) {
                    fast = head;
                    while(slow != fast) {
                        slow = slow.next;
                        fast = fast.next;
                    }
                    return slow
                }
            }
            return null
        };
        ```

    *   两个链表的第一个公共节点我们使用两个指针 node1，node2 分别指向两个链表 headA，headB 的头结点，然后同时分别逐结点遍历，当 node1 到达链表 headA 的末尾时，重新定位到链表 headB 的头结点；当 node2 到达链表 headB 的末尾时，重新定位到链表 headA 的头结点。这样，当它们相遇时，所指向的结点就是第一个公共结点。

        ```cpp
        方法 3：求出两链表长度，长的先走  差值数量  步，再一起走
        方法 1：
        var getIntersectionNode = function(headA, headB) {//双指针
            let node1 = headA,node2 = headB;
            while(node1!=node2) {
                node1= node1 !== null ? node1.next : headB;
                node2 = node2 !== null ? node2.next : headA;
            }
            return node1;
        };
        方法二：
        var getIntersectionNode = function(headA, headB) {//hashmap 法
            let map = new Map();
            while(headA) {
                map.set(headA,true);
                headA = headA.next;
            }
            while(headB) {
                if(map.has(headB)) return headB;
                headB = headB.next;
            }
            return null;
        };
        ```

    复杂：

    *   合并两个排序的链表★★

        ```cpp
        function Merge(pHead1, pHead2) {//方法 1：递归法  
            let pMergeHead = null;
            if (pHead1 === null) return pHead2;
            if (pHead2 === null) return pHead1;
            if (pHead1.val < pHead2.val) {
                pMergeHead = pHead1;
                pMergeHead.next = Merge(pHead1.next, pHead2);
            } else {
                pMergeHead = pHead2;
                pMergeHead.next = Merge(pHead1, pHead2.next);
            }
            return pMergeHead;
        }
        ////////////////////////////////////////////////////////////////////////////////////////////
        var mergeTwoLists = function (pHead1, pHead2) { //方法 2：迭代法    
            if (!pHead1) {
                return pHead2;
            } else if (!pHead2) {
                return pHead1;
            }

            let preHead = new ListNode(-1);
            let node = preHead;

            while (pHead1 && pHead2) {
                if (pHead1.val <= pHead2.val) {
                    node.next = pHead1;
                    pHead1 = pHead1.next;
                } else {
                    node.next = pHead2;
                    pHead2 = pHead2.next;
                }
                node = node.next;
            }

            if (pHead1) {
                node.next = pHead1;
            } else if (pHead2) {
                node.next = pHead2;
            }

            return preHead.next;
        };
        ```

    *   合并 K 个排序的链表★★给你一个链表数组，每个链表都已经按升序排列。请你将所有链表合并到一个升序链表中，返回合并后的链表。示例 1：输入：lists = [[1,4,5],[1,3,4],[2,6]]输出：[1,1,2,3,4,4,5,6]解释：链表数组如下：[1->4->5,1->3->4,2->6]将它们合并到一个有序链表中得到。1->1->2->3->4->4->5->6

        ```cpp
        方法 1：两两合并 逐一合并两条链表
        var mergeKLists = function (lists) {
            let mergeTwoLists = (list1, list2) => {
                let preHead = new ListNode(-1)
                let preNode = preHead
                while (list1 && list2) {
                    if (list1.val <= list2.val) {
                        preNode.next = list1
                        list1 = list1.next
                    } else {
                        preNode.next = list2
                        list2 = list2.next
                    }
                    preNode = preNode.next
                }
                preNode.next = list1 ? list1 : list2
                return preHead.next
            }
            let n = lists.length
            if (n == 0) return null
            let res = lists[0]
            for (let i = 1; i < n; i++) {
                if (lists[i]) {
                    res = mergeTwoLists(res, lists[i])
                }
            }
            return res
        };

        方法 2：
        思路
            遍历所有链表元素，将元素值放到一个数组中
            排序数组，相当于按优先级排序优先级队列
            遍历排好序的数组，重新构造一个新的有序链表即为所求
        var mergeKLists = function(lists) {
        if(!lists || lists.length == 0) return null;
        let arr = [];
        let res = new ListNode(0);
        lists.forEach(list => {
            let cur = list;
            while(cur){
                arr.push(cur.val);
                cur = cur.next;
            }
        })
        let cur = res;
        arr.sort((a,b) => a-b).forEach(val => {
            let node = new ListNode(val);
            cur.next = node;
            cur = cur.next;
        })
        return res.next;
        };
        ```

    *   奇偶链表★给定一个单链表，把所有的奇数节点和偶数节点分别排在一起。请注意，这里的奇数节点和偶数节点指的是节点编号的奇偶性，而不是节点的值的奇偶性。示例 1:输入: 1->2->3->4->5->NULL 输出: 1->3->5->2->4->NULL 解题思路有链表 1-2-3-4-5 对于奇数，我们其实只要改变奇数的下一个指向，指到下下个，如 1 改为指向 3 即可，也就是 node.next = node.next.next;对于偶数，也是一样的 node.next = node.next.next 遍历完成后，我们就得到两条链表，一条是全奇数节点，一条是全偶数节点。然后链接两段链表，奇数链.next = 偶数链头即可最后返回奇数链头

        ```cpp
        var oddEvenList = function(head) {
            if(!head){return head;}

            //储存偶数头
            let doubleHead = head.next;

            //偶数链
            let double = head.next;
            //奇数链
            let single = head;
            while(single.next&&single.next.next){
                single.next = single.next.next;
                double.next = double.next.next;
                //赋值需要放在后面一起赋值，不然 double.next.next 会被更改
                single = single.next;
                double = double.next;
            }
            //链接两条链表
            single.next = doubleHead;
            return head;
        };
        ```

    *   复制带随机指针的单向链表★

        ```cpp
        方法 1：
        用一个哈希表表示映射关系：键是原节点，值是复制的节点。

        整体算法流程是：
            第一次遍历，复制每个节点和 next 指针，并且保存“原节点-复制节点”的映射关系
            第二次遍历，通过哈希表获得节点对应的复制节点，更新 random 指针

        代码实现
        使用 ES6 的 Map，可以直接将对象作为键值。
        var copyRandomList = function(head) {
            if (!head) {
                return null;
            }
            const map = new Map();
            let node = head; // 当前节点
            const newHead = new Node(node.val);
            let newNode = newHead; // 当前节点的 copy
            map.set(node, newNode);

            while (node.next) {
                newNode.next = new Node(node.next.val);
                node = node.next;
                newNode = newNode.next;
                map.set(node, newNode);
            }

            newNode = newHead;
            node = head;
            while (newNode) {
                newNode.random = map.get(node.random);
                newNode = newNode.next;
                node = node.next;
            }

            return newHead;
        };

        方法二：
        解题思路
        原地复制，再拆分链表。
        var copyRandomList = function(head) {
        if (head == null) {
                return head;
            }
            //将拷贝节点放到原节点后面，例如 1->2->3 这样的链表就变成了这样 1->1'->2'->3->3'
            for (let node = head, copy = null; node != null; node = node.next.next) {
                copy = new Node(node.val);
                copy.next = node.next;
                node.next = copy;
            }
            //把拷贝节点的 random 指针安排上
            for (let node = head; node != null; node = node.next.next) {
                if (node.random != null) {
                    node.next.random = node.random.next;
                }
            }
            //分离拷贝节点和原节点，变成 1->2->3 和 1'->2'->3'两个链表，后者就是答案
            let newHead = head.next;
            for (let node = head, temp = null; node != null && node.next != null;) {
                temp = node.next;
                node.next = temp.next;
                node = temp;
            }

            return newHead;
        };
        ```

    *   有序链表转换二叉搜索树（BST）★

        ```cpp
        //自上而下构建树，先找链表的中点
        //使用数组，注意转换时的细节
        var sortedListToBST = function(head) {
            if(head === null) {
                return null
            }
            const nodes = []
            while(head.next) {
                nodes.push(head.val)
                head=head.next
            }
            nodes.push(head.val)

            const buildBST = (start, end)=>{
                if(start > end) {
                    return null
                }
                const mid = Math.ceil(start + (end - start) / 2) 
                //向上取整
                const root = new TreeNode(nodes[mid])
                root.left = buildBST(start, mid-1)
                root.right = buildBST(mid+1, end)
                return root
            }

            return buildBST(0, nodes.length-1)
        };
        ```

    *   二叉树展开为链表★

        ```cpp
        思路：先序遍历递归，再转换
        例如，给定二叉树
            1
           / \
          2   5
         / \   \
        3   4   6

        将其展开为：
        1
         \
          2
           \
            3
             \
              4
               \
                5
                 \
                  6
        var flatten = function (root) {
            const helper = (root) => {//先序遍历
                if (!root) {
                    return
                }
                res.push(root)
                helper(root.left)
                helper(root.right)
            }
            let res = []
            helper(root)
            for (let i = 0; i < res.length - 1; i++) {
                res[i].left = null//很关键，左子树均为空，类似链表
                res[i].right = res[i + 1]
            }
            return res;
        };
        ```

    *   K 个一组翻转链表★

        ```cpp
        给你一个链表，每 k 个节点一组进行翻转，请你返回翻转后的链表。
        k 是一个正整数，它的值小于或等于链表的长度。
        如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。
        示例：
        给你这个链表：1->2->3->4->5
        当 k = 2 时，应当返回: 2->1->4->3->5
        当 k = 3 时，应当返回: 3->2->1->4->5
        var reverseKGroup = function(head, k) {
          // 标兵
          let dummy = new ListNode()
          dummy.next = head
          let [start, end] = [dummy, dummy.next]
          let count = 0
          while(end) {
            count++
            if (count % k === 0) {
              start = reverseList(start, end.next)
              end = start.next
            } else {
              end = end.next
            }
          }
          return dummy.next

          // 翻转 stat -> end 的链表
          function reverseList(start, end) {
            let [pre, cur] = [start, start.next]
            const first = cur
            while(cur !== end) {
              let next = cur.next
              cur.next = pre
              pre = cur
              cur = next
            }
            start.next = pre
            first.next = cur
            return first
          }
        };
        ```

8.  二叉树各种遍历（前中后序遍历，递归非递归，DFS,BFS）★★★

    ```cpp
    function inorder(root) {//中序非递归   BST 第 K 小的数   第 K 大见下面
       if (!root) return null;
        var stack = [];
        var p = root;
        //var pre=-Infinity;
        while (stack.length > 0 || p) {
            if (p) { //当前非空，当前入栈，左移
                stack.push(p);
                p = p.left;
            } else { //栈弹出，并右移
                p = stack.pop();
                console.log(p.value);//在此和前一个数比较 判断是否为二叉搜索树 
                p = p.right;
            }
        }
    }
    function BFS(root) { //广度优先遍历(层序)
        var queue = [];
        queue.push(root); // 先进先出
        while (queue) {
            var temp = queue.shift();
            console.log(temp.value);
            if (temp.left != null)
                queue.push(temp.left);
            if (temp.right != null)
                queue.push(temp.right);
        }
    }
    function pre(root) {//先序非递归 中左右
        var stack = [];
        stack.push(root);
        while (stack) {
            // 移除最后一个
            var temp = stack.pop();
            console.log(temp.value);
            // 后进先出
            if (temp.right != null)
                stack.push(temp.right);
            if (temp.left != null)
                stack.push(temp.left);
        }
    }
    function pos(root) {//后序非递归 中右左=>左右中  
        var stack1 = [];
        var stack2 = [];
        stack1.push(root);
        while (stack1) {
            var temp = stack1.pop();
            stack2.push(temp);
            if (temp.left != null)
                stack1.push(temp.left);
            if (temp.right != null)
                stack1.push(temp.right);
        }
        while (stack2) {
            console.log(stack2.pop().value);
        }
    }
    var preOrder = function (node) { //先序遍历  递归版(中序和后序修改打印位置即可)
        if (node) {
            console.log(node.value); //arr.push(node.val)
            preOrder(node.left);
            preOrder(node.right);
        }
    }
    ```

9.  二叉树遍历涉及到的一些算法题（好多题其实就是二叉树深度或者广度非递归遍历稍微改一下即可）

    *   前序和中序重建二叉树★★

        ```cpp
        function reConstructBinaryTree(pre, vin)
        {
        if (pre.length == 0 || vin.length == 0) {
            return null;
        };
        var index = vin.indexOf(pre[0]);
        var left = vin.slice(0, index);
        var right = vin.slice(index + 1);
        var node = new TreeNode(vin[index]);
        node.left = reConstructBinaryTree(pre.slice(1, index + 1), left);
        node.right = reConstructBinaryTree(pre.slice(index + 1), right);
        return node;
        }
        ```

    *   BST 第 K 大的数和第 K 小的数★

        ```cpp
        function kmax(root,k) {//逆中序 右中左 第 k 大的数
        //也可以用递归中序放进一个数组
            if (!root) return null;
            var stack = [];
            var count=0;
            var p = root;
            while (stack.length > 0 || p) {
                if (p) { //当前非空，当前入栈，右移
                    stack.push(p);
                    p = p.right;
                } else { //栈弹出，并左移
                    p = stack.pop();
                    if (++count == k) {
                        return p.value;
                    }
                    p = p.left;
                }
            }
        }
        ```

    *   二叉树按层求和（层序遍历改进）★

        ```cpp
        function sum(pNode) {
        let output = [];
        if (pNode.left === null && pNode.right === null) {
            output.push(pNode.value);
            return output;
        }
        let storeArr = [];
        storeArr.push(pNode);
        while (storeArr.length > 0) {
            let tempSum = 0;
            let len = storeArr.length;
            for (let j = 0; j < len; j++) {//1,2,4 个
                let tempNode = storeArr.shift();
                tempSum += parseInt(tempNode.value);
                if (tempNode.left !== null) {
                    storeArr.push(tempNode.left);
                }
                if (tempNode.right !== null) {
                    storeArr.push(tempNode.right);
                }
            }
            output.push(tempSum);
        }
        return output;
        }
        ```

    *   Z 字型（之字形）遍历二叉树★

        ```cpp
        function Print(pRoot) {
        var lists = [];
        if (pRoot === null) {
            return lists;
        }
        var stack1 = [];
        var stack2 = [];
        stack2.push(pRoot);
        var i = 1;
        while (stack1.length !== 0 || stack2.length !== 0) {
            var list = [];
            // 为奇数层 从左至右
            if ((i & 1) === 1) {
                while (stack2.length !== 0) {
                    var tmp = stack2.pop();
                    list.push(tmp.val);
                    if (tmp.left !== null) stack1.push(tmp.left);
                    if (tmp.right !== null) stack1.push(tmp.right);
                }
            }
            // 为偶数层 从右至左
            else {
                while (stack1.length !== 0) {
                    var tmp = stack1.pop();
                    list.push(tmp.val);
                    if (tmp.right !== null) stack2.push(tmp.right);
                    if (tmp.left !== null) stack2.push(tmp.left);
                }
            }
            i++;
            lists.push(list);//可以用 for 循环将 list 逐个 push 进 lists
        }
        return lists;
        }
        ```

10.  二叉树深度相关

    *   二叉树深度★★★

        ```cpp
        function TreeDepth(pRoot) {
          //树的深度=左子树的深度和右子树深度中最大者+1
          if (pRoot === null) return 0;
          var leftDep = TreeDepth(pRoot.left);
          var rightDep = TreeDepth(pRoot.right);
          return Math.max(leftDep, rightDep) + 1;
        }
        ```

    *   二叉树最小深度★★

        ```cpp
        最小深度是从根节点到最近叶子节点的最短路径上的节点数量。
        var minDepth = function (root) {
          if (root == null) {
            return 0;
          }
          if (root.left == null && root.right == null) {
            return 1;
          }
          let ans = Infinity;
          if (root.left != null) {
            ans = Math.min(minDepth(root.left), ans);
          }
          if (root.right != null) {
            ans = Math.min(minDepth(root.right), ans);
          }
          return ans + 1;
        };
        ```

    *   树找两(叶子)节点最长距离（相隔的最长路径）★

        ```cpp
        我们要求的二叉树的最大距离其实就是求：肯定是某个节点左子树的高度加上右子树的高度加 2，所以求出每个节点左子树和右子树的高度，取左右子树高度之和加 2 的最大值即可，假设空节点的高度为-1
        function TreeDepth(pNode,nMaxDistance) {
            if (pNode == null)
                return -1; //空节点的高度为-1
            //递归
            var leftDep  = TreeDepth(pNode.left, nMaxDistance) + 1; //左子树的的高度加 1
            var rightDep  = TreeDepth(pNode.left, nMaxDistance) + 1; //右子树的高度加 1
            var nDistance = leftDep  + rightDep ; //距离等于左子树的高度加上右子树的高度+2
            nMaxDistance = nMaxDistance > nDistance ? nMaxDistance : nDistance; //得到距离的最大值
            return leftDep  > rightDep  ? leftDep  : rightDep ;
        } //nMaxDistance 为所求
        ```

    *   判断二叉树是否为平衡二叉树★★

        ```cpp
        function IsBalanced(pRoot) { //方法 1：节点重复遍历了，影响效率了
            if (pRoot == null) return true;
            let leftLen = TreeDepth(pRoot.left);
            let rightLen = TreeDepth(pRoot.right);
            return Math.abs(rightLen - leftLen) <= 1 && IsBalanced(pRoot.left) && IsBalanced(pRoot.right);
            //左右子树均平衡，且左右子树高度差不超过 1
        }

        function TreeDepth(pRoot) {
            if (pRoot == null) return 0;
            let leftLen = TreeDepth(pRoot.left);
            let rightLen = TreeDepth(pRoot.right);
            return Math.max(leftLen, rightLen) + 1;
        }

        方法 2：
        //改进办法就是在求高度的同时判断是否平衡，如果不平衡就返回-1，否则返回树的高度。
        //并且当左子树高度为-1 时，就没必要去求右子树的高度了，可以直接一路返回到最上层了
         function IsBalanced(pRoot) {
            return TreeDepth(pRoot) !== -1;
        }

        function TreeDepth(pRoot) {
            if (pRoot === null) return 0;
            const leftLen = TreeDepth(pRoot.left);
            if (leftLen === -1) return -1;//当左子树高度为-1 时，就没必要去求 rightLen，可以直接一路返回到最上层了
            const rightLen = TreeDepth(pRoot.right);
            if (rightLen === -1) return -1;
            return Math.abs(leftLen - rightLen) > 1 ? -1 : Math.max(leftLen, rightLen) + 1;
        } 
        ```

    *   二叉树右视图（左）★

        ```cpp
        方法一：DFS
        var rightSideView = function(root) {
          if(!root) return []
          let arr = []
          dfs(root, 0, arr)
          return arr
        };
        function dfs (root, step, res) {
          if(root){
            if(res.length === step){
              res.push(root.val)           
        // 当数组长度等于当前 深度 时, 把当前的值加入数组
            }
            dfs(root.right, step + 1, res) 
        // 先从右边开始, 当右边没了, 再轮到左边
            dfs(root.left, step + 1, res)
          }
        }
        ```

        ```cpp
        方法二：BFS
        var rightSideView = function(root) {
          if(!root) return []
          let queue = [root]                        
        // 队列 把树顶加入队列
          let arr = []                              
        // 用来存储每层最后个元素值
          while(queue.length > 0){
            let len = queue.length
            while (len) {
              let node = queue.shift()               // 取出队列第一个元素
              if(len === 1) arr.push(node.val)       
        // 当 node 是当前一层的最后一个元素时，把值加入 arr
              if(node.left) queue.push(node.left)    // 继续往队列添加元素
              if(node.right) queue.push(node.right)
              len--
            }
          }
          return arr
        };
        ```

11.  二叉树路径相关

    *   路径总和 1★★★

        ```cpp
        给定一个二叉树和一个目标和，判断该树中是否存在根节点到叶子节点的路径，这条路径上所有节点值相加等于目标和。存在返回 true 否则 false
        var hasPathSum = function (root, sum) {
          // 根节点为空
          if (root === null) return false;
          // 叶节点 同时 sum 参数等于叶节点值
          if (root.left === null && root.right === null) return root.val === sum;
          // 总和减去当前值，并递归
          sum = sum - root.val
          return hasPathSum(root.left, sum) || hasPathSum(root.right, sum);
        };
        ```

    *   路径总和 2（回溯法）★

        ```cpp
        按字典序打印出二叉树中结点值的和为输入整数的所有路径。 回溯法（DFS）
        function TreeNode(val) {
            this.val = val;
            this.left = this.right = null;
        }

         function FindPath(root, expectNumber) {
           //深度遍历整个树  但是做不到 在返回值的 list 中，数组长度大的数组靠前啊啊啊啊啊！！！！！！
           const path = [],
             result = [];
           return findpath(root, expectNumber, path, result);
         }

         function findpath(root, expectNumber, path, result) {
           if (root === null) {
             return result;
           }
           path.push(root.val);
           var x = expectNumber - root.val;
           if (root.left === null && root.right === null && x === 0) {
             result.push(path.slice(0)); //result.push(Array.of(...path)) 拷贝 path
           }
           findpath(root.left, x, path, result);
           findpath(root.right, x, path, result);
           path.pop(); //在遍历完之后需要把最后一颗节点弹出来 来返回上一层
           return result;
         }
        ```

12.  DP

    *   斐波那契数列★★★

        ```cpp
        function Fibonacci(n) {
            //DP 问题
            var a = 0,
                b = 1,
                c;
            if (n <= 1) {
                return n;
            }
            for (var i = 2; i <= n; i++) {
                c = a + b;
                a = b;
                b = c;
            }
            return c;
        }
        // 递归法
         function Fibonacci(n) {
            if(n<2){
                return n;
            }else{
                return Fibonacci(n - 1) + Fibonacci(n-2);
            }
        } 

         function Fibonacci(n, a, b) {//尾递归 不爆栈
          if (n <= 1) {
            return a;
          }
          return Fibonacci(n - 1, b, a + b)
        }
        ```

    *   最长公共子序列 LCS ★★

        ```cpp
        var longestCommonSubsequence = function (text1, text2) {
            let n = text1.length;
            let m = text2.length;
            let dp = Array(n + 1).fill(0).map(() => new Array(m + 1).fill(0)); //多一个第 0 行 第 0 行和 0 列均为 0 n*m
            for (let i = 1; i <= n; i++) {
                for (let j = 1; j <= m; j++) {
                    if (text1[i - 1] == text2[j - 1]) {
                        dp[i][j] = dp[i - 1][j - 1] + 1;
                    } else {
                        dp[i][j] = Math.max(dp[i][j - 1], dp[i - 1][j]);
                    }
                }
            }
            return dp[n][m];
        };
        ```

    *   最长上升子序列 ★★

        ```cpp
        function LISbyDP(arr) {
            if (arr.length <= 1) {
                return arr.length;
            }
            let maxLen = 0;
            let fs = [];
            fs[0] = 1;
            for (let i = 1; i < arr.length; i++) {
                fs[i] = 1; //初始化为 1  很关键 子序列最小也要包括自己
                for (let j = 0; j < i; j++) {
                    if (arr[j] < arr[i]) {
                        fs[i] = Math.max(fs[j] + 1, fs[i]); //fs[i]表示到以 arr[i]结尾的最长递增子序列的长度
                    }
                }
            }
            maxLen = Math.max.apply(null, fs); //计算出以每一个数结尾的最长递增子串数 求数组最大值
            return maxLen;
        }
        let arr = [1, 5, 3, 4, 6, 9, 7, 8];
        console.log(LISbyDP(arr));//6
        ```

    *   连续子数组（子串）的最大和★★

        ```cpp
        输入: nums = [-2,1,-3,4,-1,2,1,-5,4]
        输出: 6
        解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
        var maxSubArray = function(nums) {
        for(var i = 1; i < nums.length; i++){
            if(nums[i - 1] > 0){
                nums[i] += nums[i - 1];
            }
        }
        return Math.max(...nums)
        };
        //DP 法
        var maxSubArray = function (nums) {
        if (!nums.length) return null
        let max = nums[0], record = nums[0];
        for (let i = 1; i < nums.length; i++) {
            record = Math.max(record + nums[i], nums[i]);
            if (record > max) max = record;
        }
        return max
        };
        ```

    *   硬币找零（最少硬币个数）★★

        ```cpp
        var coinChange = function (coins, amount) {
            let dp = new Array(amount + 1).fill(Infinity);
            dp[0] = 0;

            for (let i = 1; i <= amount; i++) {
                for (let coin of coins) {
                    if (i - coin >= 0) {
                        dp[i] = Math.min(dp[i], dp[i - coin] + 1);
                    }
                }
            }

            return dp[amount] === Infinity ? -1 : dp[amount];
        }
        ```

    *   0-1 背包问题★

        ```cpp
         * @param {*} capacity 允许负重大小
         * @param {*} wt 重量数组
         * @param {*} val 价值数组
         * @param {*} n 几件物品 val 数组的长度
        function knapSack (capacity, wt, val, n) {
            var i, w, a, b, dp = [];
            for (let i = 0; i <= n; i++) {
                dp[i] = [];    
            }

            for (let j = 0; j <= n; j++) { //dp[i][w]表示：对于前 i 个物品，当前背包容量为 w 时，这种情况下可以装下的最大价值
                for (let w = 0; w <= capacity; w++) { //从 0 开始计数
                    if (j == 0 || w == 0) {
                        dp[j][w] = 0;
                    } else if (wt[j-1] <= w) {
                        a = val[j - 1] + dp[j - 1][w - wt[j - 1]]; // 第 j 个放 wt[j - 1]表示第 j 个物品的重量
                        b = dp[j - 1][w]; //  第 j 个不放
                        dp[j][w] = (a > b) ? a : b; // max(a,b)
                    } else {
                        dp[j][w] = dp[j-1][w];
                    }
                }
            }
            return dp[n][capacity];
        }
        ```

    *   完全背包问题（了解即可）★

        ```cpp
        var val = [3, 4, 4, 6, 5];
        var wt = [3, 5, 3, 4, 3];
        var capacity = 13;

        function all(a, b, n, m) { //重量 价值 物品种类数 总容量
            var f = [];
            for (let i = 0; i <= n; i++) {
                f[i] = [];
            }
            for (var i = 0; i <= n; i++) {
                for (var j = 0; j <= m; j++) {
                    if (i == 0 || j == 0) {
                        f[i][j] = 0;
                    } else {
                        f[i][j] = f[i - 1][j]; //不取第 i 件物品
                        for (var k = 1; k * a[i - 1] <= j; k++) { //取第 i 件物品，且第 i 件物品可以取 k 个  第 i 件物品数组下标为 i-1
                            f[i][j] = Math.max(f[i][j], f[i - 1][j - k * a[i - 1]] + k * b[i - 1]);
                        }
                    }

                }
            }
            return f[n][m];
        }
        console.log(all(wt, val, val.length, capacity));
        ```

13.  全排列（回溯法） ★

## CSS![](img/22da74088eeef1147a38349a5debf43f.png)

1.  CSS 画各种图形（等腰三角形、等腰梯形、扇形、圆、半圆）    ★

    ```cpp
    <div class="a"></div>
    //等腰三角形
    .a{
    width: 0px;
    height: 0px;
    border-bottom: 50px solid red;
    border-left: 50px solid transparent;
    border-right: 50px solid transparent;
    }

    //等腰梯形
    .a{
    width: 50px;
    height: 0px;
    border-bottom: 50px solid red;
    border-left: 50px solid transparent;
    border-right: 50px solid transparent;
    }

    //扇形
    .a{
    width: 0px;
    height: 0px;
    border-bottom: 50px solid red;
    border-left: 50px solid transparent;
    border-right: 50px solid transparent;
    border-radius: 50%;
    }

    //圆
    .a{
    width: 50px;
    height: 50px;
    background-color: blue;
    border-radius: 25px;//50% 边框半径为宽高的 50%
    }
    半圆
    .a{
    width: 100px;
    height: 50px;
    background-color: blue;
    border-top-left-radius: 50px;
    border-top-right-radius: 50px;
    /* border-bottom-left-radius: 50px; */
    }
    ```

2.  三列布局（至少掌握三种方法）    ★★★
3.  垂直水平居中（至少掌握三种方法）    ★★★