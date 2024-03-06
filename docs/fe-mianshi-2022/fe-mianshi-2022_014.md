# 第四章 第 5 节 前端框架 vue-5

> 原文：[`www.nowcoder.com/tutorial/10072/38bef7e1fe294a0f80eec892dfa8333a`](https://www.nowcoder.com/tutorial/10072/38bef7e1fe294a0f80eec892dfa8333a)

#### 1.30 双向数据绑定原理

**参考答案：**

目前几种主流的 mvc(vm)框架都实现了单向数据绑定，而我所理解的双向数据绑定无非就是在单向绑定的基础上给可输入元素（input、textare 等）添加了 change(input)事件，来动态修改 model 和 view，并没有多高深。所以无需太过介怀是实现的单向或双向绑定。

实现数据绑定的做法有大致如下几种：

**发布者-订阅者模式:** 一般通过 sub, pub 的方式实现数据和视图的绑定监听，更新数据方式通常做法是 `vm.set('property', value)`，这里有篇文章讲的比较详细，有兴趣可点[这里](http://www.html-js.com/article/Study-of-twoway-data-binding-JavaScript-talk-about-JavaScript-every-day)

这种方式现在毕竟太 low 了，我们更希望通过 `vm.property = value`这种方式更新数据，同时自动更新视图，于是有了下面两种方式

**脏值检查:** angular.js 是通过脏值检测的方式比对数据是否有变更，来决定是否更新视图，最简单的方式就是通过 `setInterval()` 定时轮询检测数据变动，当然 Google 不会这么 low，angular 只有在指定的事件触发时进入脏值检测，大致如下：

*   DOM 事件，譬如用户输入文本，点击按钮等。( ng-click )
*   XHR 响应事件 ( $http )
*   浏览器 Location 变更事件 ( $location )
*   Timer 事件( ![timeout ,](img/55ff4c7a71a84b2fb20b497e43861d35.png))interval )
*   执行 ![digest() 或](img/55ff4c7a71a84b2fb20b497e43861d35.png))%20%E6%88%96)apply()

**数据劫持:** vue.js 则是采用数据劫持结合发布者-订阅者模式的方式，通过`Object.defineProperty()`来劫持各个属性的`setter`，`getter`，在数据变动时发布消息给订阅者，触发相应的监听回调。

#### 1.31 既然 Vue 通过数据劫持可以精准探测数据在具体 dom 上的变化,为什么还需要虚拟 DOM diff 呢?

**参考答案**：

**前置知识:** 依赖收集、虚拟 DOM、响应式系统

现代前端框架有两种方式侦测变化，一种是 **pull** ，一种是 **push**

**pull:** 其代表为 React，我们可以回忆一下 React 是如何侦测到变化的,我们通常会用 setStateAPI 显式更新，然后 React 会进行一层层的 Virtual Dom Diff 操作找出差异，然后 Patch 到 DOM 上，React 从一开始就不知道到底是哪发生了变化，只是知道「有变化了」，然后再进行比较暴力的 Diff 操作查找「哪发生变化了」，另外一个代表就是 Angular 的脏检查操作。

**push:** Vue 的响应式系统则是 push 的代表，当 Vue 程序初始化的时候就会对数据 data 进行依赖的收集，一但数据发生变化,响应式系统就会立刻得知。因此 Vue 是一开始就知道是「在哪发生变化了」，但是这又会产生一个问题，如果你熟悉 Vue 的响应式系统就知道，通常一个绑定一个数据就需要一个 Watcher（具体如何创建的 Watcher 可以先了解下 Vue 双向数据绑定的原理如下图）

![vue 双向数据绑定原理一但我们的绑定细粒度过高就会产生大量的 Watcher，这会带来内存以及依赖追踪的开销，而细粒度过低会无法精准侦测变化,因此 Vue 的设计是选择中等细粒度的方案,在组件级别进行 push 侦测的方式,也就是那套响应式系统,通常我们会第一时间侦测到发生变化的组件,然后在组件内部进行 Virtual Dom Diff 获取更加具体的差异，而 Virtual Dom Diff 则是 pull 操作，Vue 是 push+pull 结合的方式进行变化侦测的。#### 1.32 简单聊聊 new Vue 以后发生的事情**参考答案**：1.  `new Vue` 会调用 Vue 原型链上的 `_init` 方法对 Vue 实例进行初始化；2.  首先是 `initLifecycle` 初始化生命周期，对 Vue 实例内部的一些属性（如 children、parent、isMounted）进行初始化；3.  `initEvents`，初始化当前实例上的一些自定义事件（Vue.$on）；4.  `initRender`，解析 `slots` 绑定在 Vue 实例上，绑定 `createElement` 方法在实例上；5.  完成对生命周期、自定义事件等一系列属性的初始化后，触发生命周期钩子 `beforeCreate`；6.  `initInjections`，在初始化 `data` 和 `props` 之前完成依赖注入（类似于 React.Context）；7.  `initState`，完成对 `data` 和 `props` 的初始化，同时对属性完成数据劫持内部，启用监听者对数据进行监听（更改）；8.  `initProvide`，对依赖注入进行解析；9.  完成对数据（state 状态）的初始化后，触发生命周期钩子 `created`；10.  进入挂载阶段，将 vue 模板语法通过 `vue-loader` 解析成虚拟 DOM 树，虚拟 DOM 树与数据完成双向绑定，触发生命周期钩子 `beforeMount`；11.  将解析好的虚拟 DOM 树通过 vue 渲染成真实 DOM，触发生命周期钩子 `mounted`；#### 1.33 v-for 中的 key 的理解？**参考答案**：需要使用 key 来给每个节点做一个唯一标识，Diff 算法就可以正确的识别此节点。主要是为了高效的更新虚拟 DOM。#### 1.34 vue 首屏白屏如何解决？**参考答案**：1.  路由懒加载 2.  vue-cli 开启打包压缩 和后台配合 gzip 访问 3.  进行 cdn 加速 4.  开启 vue 服务渲染模式 5.  用 webpack 的 externals 属性把不需要打包的库文件分离出去，减少打包后文件的大小 6.  在生产环境中删除掉不必要的 console.log```cpp  plugins:     new webpack.optimize.UglifyJsPlugin({ //添加-删除 console.log      compress: {        warnings: false,        drop_debugger: true,        drop_console: true      },      sourceMap: true    }),```7.  开启 nginx 的 gzip ,在 nginx.conf 配置文件中配置```cpphttp {  //在 http 中配置如下代码，   gzip on;   gzip_disable "msie6";    gzip_vary on;    gzip_proxied any;   gzip_comp_level 8; #压缩级别   gzip_buffers 16 8k;   #gzip_http_version 1.1;   gzip_min_length 100; #不压缩临界值   gzip_types text/plain application/javascript application/x-javascript text/css    application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png; }```8.  添加 loading 效果，给用户一种进度感受#### 1.35 vue 单页面和传统的多页面区别？**参考答案**:单页面应用（SPA）通俗一点说就是指只有一个主页面的应用，浏览器一开始要加载所有必须的 html, js, css。所有的页面内容都包含在这个所谓的主页面中。但在写的时候，还是会分开写（页面片段），然后在交互的时候由路由程序动态载入，单页面的页面跳转，仅刷新局部资源。多应用于 pc 端。多页面（MPA）指一个应用中有多个页面，页面跳转时是整页刷新**单页面的优点：**用户体验好，快，内容的改变不需要重新加载整个页面，基于这一点 spa 对服务器压力较小；前后端分离；页面效果会比较炫酷（比如切换页面内容时的专场动画）。**单页面缺点：**不利于 seo；导航不可用，如果一定要导航需要自行实现前进、后退。（由于是单页面不能用浏览器的前进后退功能，所以需要自己建立堆栈管理）；初次加载时耗时多；页面复杂度提高很多。#### 1.36 ![refs、$parent 的使用？**参考答案**：$root 可以用来获取 vue 的根实例，比如在简单的项目中将公共数据放再 vue 根实例上(可以理解为一个全局 store ),因此可以代替 vuex 实现状态管理；$refs 在子组件上使用 ref 特性后，this.属性可以直接访问该子组件。可以代替事件 emit 和![](img/bfb1c42e751092578184c38fc247ac4b.png)refs.testId 获取指定元素。注意：![](img/8a987007c4d3bfc532957208f2165a52.png)refs。

$parent

$parent 属性可以用来从一个子组件访问父组件的实例，可以替代将数据以 prop 的方式传入子组件的方式；当变更父级组件的数据的时候，容易造成调试和理解难度增加；

#### 1.36 路由跳转和 location.href 的区别？

**参考答案**：

使用 location.href='/url'来跳转，简单方便，但是刷新了页面；
使用路由方式跳转，无刷新页面，静态跳转；

#### 1.37 scss 是什么？在 vue.cli 中的安装使用步骤是？有哪几大特性？

**参考答案**：

css 的预编译。

使用步骤：

1.  先装 css-loader、node-loader、sass-loader 等加载器模块
2.  在 build 目录找到 webpack.base.config.js，在那个 extends 属性中加一个拓展.scss
3.  在同一个文件，配置一个 module 属性
4.  然后在组件的 style 标签加上 lang 属性 ，例如：lang=”scss”

特性:

可以用变量，例如（$变量名称=值）；
可以用混合器，例如（）
可以嵌套

#### 1.38 delete 与 vue.delete 区别?

delte 会删除数组的值，但是它依然会在内存中占位置
而 vue.delete 会删除数组在内存中的占位

```cpp
let arr1 = [1,2,3]
let arr2 = [1,2,3]
delete arr1[1]
this.$delete(arr2,2)
console.log(arr1)    //【1, empty, 3】
console.log(arr2)    //【1,2】
```

#### 1.39 computed 和 watch 的区别

**参考答案**：

computed

计算结果并返回，只有当被计算的属性发生改变时才会触发（即：计算属性的结果会被缓存，除非依赖的响应属性变化才会重新及孙）

watch

监听某一个值，当被监听的值发生变化时，执行相关操作。

与 computed 的区别是，watch 更加适用于监听某一个值得变化，并做对应操作，比如请求后太接口等。而 computed 适用于计算已有的值并返回结果。 监听简单数据类型：

```cpp
data(){      
    return{        
        'first':2     
    }   
},   
 watch:{      
     first(){        
         console.log(this.first)    
    }   
 },
```

#### 1.40 Vue 为什么要用 vm.$set() 解决对象新增属性不能响应的问题 ？你能说说如下代码的实现原理么？

**参考答案**：

**1）Vue 为什么要用 vm.$set() 解决对象新增属性不能响应的问题**

1.  Vue 使用了 Object.defineProperty 实现双向数据绑定
2.  在初始化实例时对属性执行 getter/setter 转化
3.  属性必须在 data 对象上存在才能让 Vue 将它转换为响应式的（这也就造成了 Vue 无法检测到对象属性的添加或删除）

所以 Vue 提供了 Vue.set (object, propertyName, value) / vm.$set (object, propertyName, value)

**2）框架本身是如何实现的呢?**

> Vue 源码位置：vue/src/core/instance/index.js

```cpp
export function set (target: Array<any> | Object, key: any, val: any): any {
  // target 为数组  
  if (Array.isArray(target) && isValidArrayIndex(key)) {
    // 修改数组的长度, 避免索引>数组长度导致 splcie()执行有误
    target.length = Math.max(target.length, key)
    // 利用数组的 splice 变异方法触发响应式  
    target.splice(key, 1, val)
    return val
  }
  // key 已经存在，直接修改属性值  
  if (key in target && !(key in Object.prototype)) {
    target[key] = val
    return val
  }
  const ob = (target: any).__ob__
  // target 本身就不是响应式数据, 直接赋值
  if (!ob) {
    target[key] = val
    return val
  }
  // 对属性进行响应式处理
  defineReactive(ob.value, key, val)
  ob.dep.notify()
  return val
}
```

我们阅读以上源码可知，vm.$set 的实现原理是：

1.  如果目标是数组，直接使用数组的 splice 方法触发相应式；
2.  如果目标是对象，会先判读属性是否存在、对象是否是响应式，
3.  最终如果要对属性进行响应式处理，则是通过调用 defineReactive 方法进行响应式处理