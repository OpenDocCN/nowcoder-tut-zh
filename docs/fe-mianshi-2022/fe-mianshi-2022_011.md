# 第四章 第 2 节 前端框架 vue-2

> 原文：[`www.nowcoder.com/tutorial/10072/54d25175a82b4402be49c6ff78a5ab4b`](https://www.nowcoder.com/tutorial/10072/54d25175a82b4402be49c6ff78a5ab4b)

#### 1.7 mutation 和 action 有什么区别？

**参考答案：**

​ **mutation**：更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。Vuex 中的 mutation 非常类似于件： 每个 mutation 都有一个字符串的 事件类型 (type) 和 一个 回调函数 (handler)。这个回调函数就是我们实际进 行状态更改的地方，并且它会接受 state 作为第一个参数

```cpp
const store = new Vuex.Store({
  state: {
    count: 1
  },
  mutations: {
    increment (state) {
      // 变更状态
      state.count++
    }
  }
})
```

​ 不能直接调用一个 mutation handler。这个选项更像是事件注册：“当触发一个类型为 increment 的 mutation 时，调用此函数。”要唤醒一个 mutation handler，你需要以相应的 type 调用 store.commit 方法：

```cpp
store.commit('increment')
```

​ **Action:** Action 类似于 mutation，不同在于：

1.  Action 提交的是 mutation，而不是直接变更状态。

2.  Action 可以包含任意异步操作。

    让我们来注册一个简单的 action：

```cpp
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  },
  actions: {
    increment (context) {
      context.commit('increment')
    }
  }
})
```

**扩展：**事实上在 vuex 里面 actions 只是一个架构性的概念，并不是必须的，说到底只是一个函数，你在里面想干嘛都可以，只要最后触发 mutation 就行。异步竞态怎么处理那是用户自己的事情。

vuex 真正限制你的只有 mutation 必须是同步的这一点（在 redux 里面就好像 reducer 必须同步返回下一个状态一样）。同步的意义在于这样每一个 mutation 执行完成后都可以对应到一个新的状态（和 reducer 一样），这样 devtools 就可以打个 snapshot 存下来，然后就可以随便 time-travel 了。如果你开着 devtool 调用一个异步的 action，你可以清楚地看到它所调用的 mutation 是何时被记录下来的，并且可以立刻查看它们对应的状态。

#### 1.8 修改 ElementUI 样式的几种方式?

**参考答案：**

​ 修改 ElementUI 样式的方式有四种：

1.  新建全局样式表

    新建 global.css 文件，并在 main.js 中引入。 global.css 文件一般都放在 src->assets 静态资源文件夹下的 style 文件夹下，在 main.js 的引用写法如下：

    ```cpp
    import "./assets/style/global.css"
    ```

    在 global.css 文件中写的样式，无论在哪一个 vue 单页面都会覆盖 ElementUI 默认的样式。

2.  在当前-vue-单页面中添加一个新的 style 标签

    在当前的 vue 单页面的`style`标签后，添加一对新的`style`标签，新的`style`标签中不要添加`scoped`属性。在有写`scoped`的`style`标签中书写的样式不会覆盖 ElementUI 默认的样式。

3.  使用 `/deep/` 深度修改标签样式

    找到需要修改的 ElementUI 标签的类名，然后在类名前加上 `/deep/` ，可以强制修改默认样式。这种方式可以直接用到有 `scoped` 属性的 style 标签中。

    ```cpp
    // 修改级联选择框的默认宽度
    /deep/ .el-cascader {
      width: 100%;
    }
    ```

4.  通过内联样式 或者 绑定类样式覆盖默认样式

    通过内联样式 style ，绑定类样式的方式，可以在**某些标签**中可以直接覆盖默认样式，不是很通用。具体实例如下：

```cpp
   <el-button :style="selfstyle">默认按钮</el-button>
   <script>
       export default {
         data() {
           return {
               selfstyle: {
                   color: "white",
           marginTop: "10px",
           width: "100px",
           backgroundColor: "cadetblue"
               }
           };
         }
       }
   </script>
```

通过绑定修改样式方式修改：

```cpp
   <el-button :class="[selfbutton]">默认按钮</el-button>
   <script>
     export default {
       data() {
         return {
           selfbutton: "self-button"
         };
       } 
     }
   </script>
   <style lang="stylus" rel="stylesheet/stylus" scoped>
   .self-button {
       color: white;
       margin-top: 10px;
       width: 100px;
       background-Color: cadetblue;
   }
   </style>
```

**扩展：**

​ 第一种全局引入 css 文件的方式，适合于对 elementUI 整体的修改，比如整体配色的修改；
​ 第二种添加一个 style 标签的形式，也能够实现修改默认样式的效果，但实际上因为是修改了全局的样式，因此 在不同的 vue 组件中修改同一个样式有可能会有冲突。
​ 第三种方式通过 /deep/ 的方式可以很方便的在 vue 组件中修改默认样式，也不会于其他页面有冲突。
​ 第四种方式局限性比较大，可以使用，但不推荐使用。

#### 1.9 elementui 有什么用?

**参考答案：**

​ **Element-UI**：是一套采用 Vue 2.0 作为基础框架实现的组件库，一套为开发者、设计师和产品经理准备的基于 Vue 2.0 的组件库，提供了配套设计资源，帮助网站快速成型

**扩展：**

Element-UI 特点：

一致性 Consistency

*   与现实生活一致：与现实生活的流程、逻辑保持一致，遵循用户习惯的语言和概念；
*   在界面中一致：所有的元素和结构需保持一致，比如：设计样式、图标和文本、元素的位置等。

反馈 Feedback

*   控制反馈：通过界面样式和交互动效让用户可以清晰的感知自己的操作；
*   页面反馈：操作后，通过页面元素的变化清晰地展现当前状态。

效率 Efficiency

*   简化流程：设计简洁直观的操作流程；
*   清晰明确：语言表达清晰且表意明确，让用户快速理解进而作出决策；
*   帮助用户识别：界面简单直白，让用户快速识别而非回忆，减少用户记忆负担。

可控 Controllability

*   用户决策：根据场景可给予用户操作建议或安全提示，但不能代替用户进行决策；
*   结果可控：用户可以自由的进行操作，包括撤销、回退和终止当前操作等。

#### 1.10 导航守卫

**参考答案：**

导航守卫主要用来**通过跳转或取消的方式守卫导航**。

简单的说，导航守卫就是路由跳转过程中的一些钩子函数。路由跳转是一个大的过程，这个大的过程分为跳转前中后等等细小的过程，在每一个过程中都有一函数，这个函数能让你操作一些其他的事儿的时机，这就是导航守卫。

**解析：**

路由守卫的具体方法：

1.  全局前置守卫

    你可以使用 router.beforeEach 注册一个全局前置守卫：

    ```cpp
    const router = new VueRouter({ ... })
    router.beforeEach((to, from, next) => {
      // ...
    })
    ```

    当一个导航开始时，全局前置守卫按照注册顺序调用。守卫是异步链式调用的，导航在最后的一层当中。

    ```cpp
    new Promise((resolve, reject) => {
        resolve('第一个全局前置守卫')
    }.then(() => {
        return '第二个全局前置守卫'
    }.then(() => {
        ...
    }.then(() => {
        console.log('导航终于开始了') // 导航在最后一层中
    })
    ```

​ 每个守卫方法接收三个参数（往后的守卫都大同小异）：

​ 1\. to: Route: 即将要进入的目标 路由对象

​ 2\. from: Route: 当前导航正要离开的路由

​ 3\. next: Function: 一定要调用该方法将控制权交给下一个守卫，执行效果依赖 next 方法的参数。

​ next(): 进入下一个守卫。如果全部守卫执行完了。则导航的状态就是 confirmed (确认的)。

​ next(false): 中断当前的导航（把小明腿打断了）。如果浏览器的 URL 改变了 (可能是用户手动或者浏览器 后退按钮)，那么 URL 地址会重置到 from 路由对应的地址。

​ next('/') 或者 next({ path: '/' }): 跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航（小明 被打断腿并且送回家了）。你可以向 next 传递任意位置对象，且允许设置诸如 replace: true、name: 'home' 之类的选项以及任何用在 router-link 的 to prop 或 router.push 中的选项。

​ next(error): (2.4.0+) 如果传入 next 的参数是一个 Error 实例，则导航会被终止且该错误会被传递给 router.

​ onError() 注册过的回调。

​ 注意：永远不要使用两次 next，这会产生一些误会。

2.  全局解析守卫

    这和 router.beforeEach 类似，但他总是被放在最后一个执行。

3.  全局后置钩子

    导航已经确认了的，小明已经到了外婆家了，你打断他的腿他也是在外婆家了。

    ```cpp
    router.afterEach((to, from) => {
        // 你并不能调用 next
      // ...
    })
    ```

4.  路由独享的守卫

    在路由内写的守卫

    ```cpp
    const router = new VueRouter({
      routes: [
        {
          path: '/foo',
          component: Foo,
          beforeEnter: (to, from, next) => {
            // ...
          }
        }
      ]
    })
    ```

5.  组件内的守卫

    5.1 beforeRouteEnter

    5.2 beforeRouteUpdate (2.2 新增)

    5.3 beforeRouteLeave

    ```cpp
    const Foo = {
      template: `...`,
      beforeRouteEnter (to, from, next) {
        // 路由被 confirm 前调用
        // 组件还未渲染出来，不能获取组件实例 `this`
      },
      beforeRouteUpdate (to, from, next) {
        // 在当前路由改变，但是该组件被复用时调用
        // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
        // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
        // 可以访问组件实例 `this`，一般用来数据获取。
      },
      beforeRouteLeave (to, from, next) {
        // 导航离开该组件的对应路由时调用
        // 可以访问组件实例 `this`
      }
    }
    ```

**扩展：**

导航全过程

*   导航被触发。
*   在准备离开的组件里调用 beforeRouteLeave 守卫。
*   调用全局的 beforeEach 守卫。
*   在重用的组件里调用 beforeRouteUpdate 守卫 (2.2+)。（如果你的组件是重用的）
*   在路由配置里调用 beforeEnter。
*   解析即将抵达的组件。
*   在即将抵达的组件里调用 beforeRouteEnter。
*   调用全局的 beforeResolve 守卫 (2.5+)。
*   导航被确认。
*   调用全局的 afterEach 钩子。
*   触发 DOM 更新。
*   用创建好的实例调用 beforeRouteEnter 守卫中传给 next 的回调函数。

#### 1.11 路由守卫进行判断登录

**参考答案：**

在 vue 项目中，切换路由时肯定会碰到需要登录的路由，其原理就是在切换路径之前进行判断，你不可能进入页面再去判断有无登录重新定向到 login，那样的话会导致页面已经渲染以及它的各种请求已经发出。

1.  如需要登录的路由可在 main.js 中统一处理（全局前置守卫）

    我们可以在入口文件 man.js 里面进行配置，使用 router.beforeEach 方法，不懂得可以打印 to，from 的参数就 ok，requireAuth 可以随意换名的，只要 man.js 里面跟配置路由的 routes 里面的字段保持一致：

    ```cpp
    import router from './router'
    router.beforeEach((to, from, next) => {
      if (to.matched.some(record => record.meta.requireAuth)){  // 判断该路由是否需要登录权限
        if(!sessionStorage.getItem('token') && !localStorage.getItem('token')){
          next({
            path: '/login',
            query: {redirect: to.fullPath}  // 将跳转的路由 path 作为参数，登录成功后跳转到该路由
          })
        }else{
             next();
        }
      }else {
        next();
      }
    });
    new Vue({
      el: '#app',
      router,
      render: h => h(App)
    })
    ```

    ```cpp
    export default new Router({
        routes: [
            {
                path: '/',
                name: 'home',
                redirect: '/home'
            },
            {
                path: '/home',
                component: Home,
                meta: {
                  title: '',
                  requireAuth: true,  // 添加该字段，表示进入这个路由是需要登录的
               }
            },
            {
                path:'/login',
                name:'login',
                component:Login
            },
            {
                path:'/register',
                name:'register',
                component:Register
            }
        ]
    })
    ```

2.  全局后置守卫

    ```cpp
    router.afterEach((to, from) => {
      // ...
    })
    ```

3.  单独路由独享守卫（与全局一致，可单独对某个路由进行配置）

    ```cpp
    const router = new VueRouter({
      routes: [
        {
          path: '/foo',
          component: Foo,
          beforeEnter: (to, from, next) => {
            // ...
          }
        }
      ]
    })
    ```

4.  组件内部路由守卫（可写在与生命周期同级位置）

    ```cpp
    beforeRouteEnter (to, from, next) {
        // 在渲染该组件的对应路由被 confirm 前调用
        // 不！能！获取组件实例 `this`
        // 因为当守卫执行前，组件实例还没被创建
    },
    beforeRouteUpdate (to, from, next) {
        // 在当前路由改变，但是该组件被复用时调用
        // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
        // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
        // 可以访问组件实例 `this`
    },
    beforeRouteLeave (to, from, next) {
        // 导航离开该组件的对应路由时调用
        // 可以访问组件实例 `this`
    }
    ```

#### 1.12 vue-router 实现懒加载

**参考答案：**

懒加载：当打包构建应用时，JavaScript 包会变得非常大，影响页面加载。如果我们能把不同路由对应的组件分割成不同的代码块，然后当路由被访问的时候才加载对应组件，这样就更加高效了。

实现：结合 Vue 的[异步组件](https://cn.vuejs.org/v2/guide/components-dynamic-async.html#%E5%BC%82%E6%AD%A5%E7%BB%84%E4%BB%B6)和 Webpack 的[代码分割功能](https://doc.webpack-china.org/guides/code-splitting-async/#require-ensure-/)，可以实现路由组件的懒加载

1.  首先，可以将异步组件定义为返回一个 Promise 的工厂函数 (该函数返回的 Promise 应该 resolve 组件本身)：

    ```cpp
    const Foo = () => Promise.resolve({ /* 组件定义对象 */ })
    ```

2.  在 Webpack 2 中，我们可以使用[动态 import](https://github.com/tc39/proposal-dynamic-import)语法来定义代码分块点 (split point)：

    ```cpp
    import('./Foo.vue') // 返回 Promise
    ```

    结合这两者，这就是如何定义一个能够被 Webpack 自动代码分割的异步组件。

    ```cpp
    const Foo = () => import('./Foo.vue')
    ```

    在路由配置中什么都不需要改变，只需要像往常一样使用 `Foo`：

    ```cpp
    const router = new VueRouter({
      routes: [
        { path: '/foo', component: Foo }
      ]
    })
    ```