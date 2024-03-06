# 第四章 第 1 节 前端框架 vue-1

> 原文：[`www.nowcoder.com/tutorial/10072/674cdbc02da04771b0c8566a5a1699f6`](https://www.nowcoder.com/tutorial/10072/674cdbc02da04771b0c8566a5a1699f6)

#### 1.1 v-model 作用？

**参考答案：**

`v-model` 本质上不过是语法糖，可以用 v-model 指令在**表单**及**元素**上创建双向数据绑定。

1.  它会根据控件类型自动选取正确的方法来更新元素
2.  它负责监听用户的输入事件以更新数据，并对一些极端场景进行一些特殊处理
3.  `v-model` 会忽略所有表单元素的 `value`、`checked`、`selected` 特性的初始值,而总是将 Vue 实例的数据作为数据来源，因此我们应该通过 JavaScript 在组件的 `data` 选项中声明初始值

**扩展：**

`v-model` 在内部为不同的输入元素使用不同的属性并抛出不同的事件：

1.  text 和 textarea 元素使用 `value` 属性和 `input` 事件；

2.  checkbox 和 radio 使用 `checked` 属性和 `change` 事件；

3.  select 字段将 `value` 作为 prop 并将 `change` 作为事件。

#### 1.2 v-model 实现原理？

**参考答案：**

v-model 只不过是一个语法糖而已,真正的实现靠的还是

1.  v-bind:绑定响应式数据
2.  触发 oninput 事件并传递数据

```cpp
<input v-model="sth" />
<!-- 等同于-->
<input :value="sth" @input="sth = $event.target.value" />
<!--自 html5 开始,input 每次输入都会触发 oninput 事件，所以输入时 input 的内容会绑定到 sth 中，于是 sth 的值就被改变-->
<!--$event 指代当前触发的事件对象;-->
<!--$event.target 指代当前触发的事件对象的 dom;-->
<!--$event.target.value 就是当前 dom 的 value 值;-->
<!--在@input 方法中，value => sth;-->
<!--在:value 中,sth => value;-->
```

#### 1.3 Vue2.0 双向绑定的缺陷？

**参考答案：**

​ Vue2.0 的数据响应是采用数据劫持结合发布者-订阅者模式的方式，通过 Object.defineProperty () 来劫持各个属性的 setter、getter，但是它并不算是实现数据的响应式的完美方案，某些情况下需要对其进行修补或者 hack 这也是它的缺陷，主要表现在两个方面：

1.  vue 实例创建后，无法检测到对象属性的新增或删除，只能追踪到数据是否被修改

    2.  不能监听数组的变化

**解析：**

1.  vue 实例创建后，无法检测到对象属性的新增或删除，只能追踪到数据是否被修改(Object.defineProperty 只能劫持对象的属性)。

    当创建一个 Vue 实例时，将遍历所有 DOM 对象，并为每个数据属性添加了 get 和 set。get 和 set 允许 Vue 观察数据的更改并触发更新。但是，如果你在 Vue 实例化后添加（或删除）一个属性，这个属性不会被 vue 处理，改变 get 和 set。

    解决方案：

    ```cpp
    Vue.set(obj, propertName/index, value)
    // 响应式对象的子对象新增属性，可以给子响应式对象重新赋值
    data.location = {
        x: 100,
        y: 100
    }
    data.location = {...data, z: 100}
    ```

2.  不能监听数组的变化

    vue 在实现数组的响应式时，它使用了一些 hack，把无法监听数组的情况通过重写数组的部分方法来实现响应式，这也只限制在数组的 push/pop/shift/unshift/splice/sort/reverse 七个方法，其他数组方法及数组的使用则无法检测到，例如如下两种使用方式

    ```cpp
    vm.items[index] = newValue;
    vm.items.length
    ```

    vue 实现数组响应式的方法

    通过重写数组的 Array.prototype 对应的方法，具体来说就是重新指定要操作数组的 prototype，并重新该 prototype 中对应上面的 7 个数组方法，通过下面代码简单了解下实现原理：

    ```cpp
    const methods = ['pop','shift','unshift','sort','reverse','splice', 'push'];
    // 复制 Array.prototype，并将其 prototype 指向 Array.prototype
    let proto = Object.create(Array.prototype);
    methods.forEach(method => {
        proto[method] = function () { // 重写 proto 中的数组方法
            Array.prototype[method].call(this, ...arguments);
            viewRender() // 视图更新
            function observe(obj) {
                if (Array.isArray(obj)) { // 数组实现响应式
                    obj.__proto__ = proto; // 改变传入数组的 prototype
                    return;
                }
                if (typeof obj === 'object') {
                    ... // 对象的响应式实现
                }
            }
        }
    })
    ```

#### 1.4 Vue3.0 实现数据双向绑定的方法

**参考答案:**

vue3.0 实现数据双向绑定是通过**Proxy**

**Proxy**是 ES6 中新增的一个特性，翻译过来意思是"代理"，用在这里表示由它来“代理”某些操作。 Proxy 让我们能够以简洁易懂的方式控制外部对对象的访问。其功能非常类似于设计模式中的代理模式。

Proxy 可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。

使用 Proxy 的核心优点是可以交由它来处理一些非核心逻辑（如：读取或设置对象的某些属性前记录日志；设置对象的某些属性值前，需要验证；某些属性的访问控制等）。 从而可以让对象只需关注于核心逻辑，达到关注点分离，降低对象复杂度等目的。

**扩展：**

使用 proxy 实现，双向数据绑定，相比 2.0 的 Object.defineProperty ()优势：

1.  可以劫持整个对象，并返回一个新对象
2.  有 13 种劫持操作

#### 1.5 Vuex 是什么，每个属性是干嘛的，如何使用

**参考答案：**

Vuex 是什么？

Vuex 是专门为 Vuejs 应用程序设计的**状态管理工具**。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化

具体工作：vuex 是一种状态管理机制，将全局组件的共享状态抽取出来为一个 store，以一个单例模式存在，应用任何一个组件中都可以使用，vuex 更改 state 的唯一途径是通过 mutation，mutation 需要 commit 触发, action 实际触发是 mutation，其中 mutation 处理同步任务，action 处理异步任务。

Vuex 每个属性是干嘛的？

![](img/e2e6076cfae0617b1650869ab9ada93a.png)

Vuex 的属性包含以下 6 个：

1）state

state 是存储的单一状态，是存储的基本数据。

2）Getters

getters 是 store 的计算属性，对 state 的加工，是派生出来的数据。就像 computed 计算属性一样，getter 返回的值会根据它的依赖被缓存起来，且只有当它的依赖值发生改变才会被重新计算。

3）Mutations

mutations 提交更改数据，使用 store.commit 方法更改 state 存储的状态。（mutations 同步函数）

4）Actions

actions 像一个装饰器，提交 mutation，而不是直接变更状态。（actions 可以包含任何异步操作）

5）Module

Module 是 store 分割的模块，每个模块拥有自己的 state、getters、mutations、actions。

```cpp
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```

6）辅助函数

Vuex 提供了 mapState、MapGetters、MapActions、mapMutations 等辅助函数给开发在 vm 中处理 store。

Vuex 的使用方法？

![](img/2c28fa0fb51a3961d0b95d968e5e2ea7.png)

```cpp
import Vuex from 'vuex';
Vue.use(Vuex); // 1\. vue 的插件机制，安装 vuex
let store = new Vuex.Store({ // 2.实例化 store，调用 install 方法
    state,
    getters,
    modules,
    mutations,
    actions,
    plugins
});
new Vue({ // 3.注入 store, 挂载 vue 实例
    store,
    render: h=>h(app)
}).$mount('#app');
```

#### 1.6 Vuex 实现原理

**参考答案：**

通过以下三个方面来阐述 vuex 的实现原理：

*   store 是怎么注册的?

*   mutation，commit 是怎么实现的?

*   辅助函数是怎么实现的?

1.  store 是怎么注册的?

    我们看到 Vuex 在 vue 的生命周期中的初始化钩子前插入一段 Vuex 初始化代码。给 Vue 的实例注入一个

    `$store` 的属性，这也就是为什么我们在 Vue 的组件中可以通过 `this.$store.xxx`, 访问到 Vuex 的各种数据和状态

    ```cpp
    export default function (Vue) {
      // 获取当前 Vue 的版本
      const version = Number(Vue.version.split('.')[0])

      if (version >= 2) {
        // 2.x 通过 hook 的方式注入
        Vue.mixin({ beforeCreate: vuexInit })
      } else {
        // 兼容 1.x
        // 使用自定义的 _init 方法并替换 Vue 对象原型的 _init 方法，实现注入
        const _init = Vue.prototype._init
        Vue.prototype._init = function (options = {}) {
          options.init = options.init
            ? [vuexInit].concat(options.init)
            : vuexInit
          _init.call(this, options)
        }
      }

      /**
       * Vuex init hook, injected into each instances init hooks list.
       */

      function vuexInit () {
        const options = this.$options
        // store 注入
        if (options.store) {
          this.$store = typeof options.store === 'function'
            ? options.store()
            : options.store
        } else if (options.parent && options.parent.$store) {
          // 子组件从其父组件引用 $store 属性
          this.$store = options.parent.$store
        }
      }
    }
    ```

2.  mutations，commit 是怎么实现的

    ```cpp
    function registerMutation (store, type, handler, local) {
      // 获取 type(module.mutations 的 key) 对应的 mutations, 没有就创建一个空数组
      const entry = store._mutations[type] || (store._mutations[type] = [])
      // push 处理过的 mutation handler
      entry.push(function wrappedMutationHandler (payload) {
        // 调用用户定义的 hanler, 并传入 state 和 payload 参数
        handler.call(store, local.state, payload)
      })
    }
    ```

    registerMutation 是对 store 的 mutation 的初始化，它接受 4 个参数，store 为当前 Store 实例，type 为 mutation 的 key，handler 为 mutation 执行的回调函数，path 为当前模块的路径。

    mutation 的作用就是同步修改当前模块的 state ，函数首先通过 type 拿到对应的 mutation 对象数组， 然后把一个 mutation 的包装函数 push 到这个数组中，这个函数接收一个参数 payload，这个就是我们在定义 mutation 的时候接收的额外参数。这个函数执行的时候会调用 mutation 的回调函数，并通过 getNestedState(store.state, path) 方法得到当前模块的 state，和 playload 一起作为回调函数的参数。

    我们知道 mutation 是通过 commit 来触发的，这里我们也来看一下 commit 的定义

    ```cpp
    commit (_type, _payload, _options) {
        // 解析参数
        const {
          type,
          payload,
          options
        } = unifyObjectStyle(_type, _payload, _options)

        // 根据 type 获取所有对应的处理过的 mutation 函数集合
        const mutation = { type, payload }
        const entry = this._mutations[type]
        if (!entry) {
          if (process.env.NODE_ENV !== 'production') {
            console.error(`[vuex] unknown mutation type: ${type}`)
          }
          return
        }
        // 执行 mutation 函数
        this._withCommit(() => {
          entry.forEach(function commitIterator (handler) {
            handler(payload)
          })
        })

        // 执行所有的订阅者函数
        this._subscribers.forEach(sub => sub(mutation, this.state))

        if (
          process.env.NODE_ENV !== 'production' &&
          options && options.silent
        ) {
          console.warn(
         `[vuex] mutation type: ${type}. Silent option has been removed. ` +
            'Use the filter functionality in the vue-devtools'
       )
        }
    }
    ```

    commit 支持 3 个参数，type 表示 mutation 的类型，payload 表示额外的参数,根据 type 去查找对应的 mutation，如果找不到，则输出一条错误信息，否则遍历这个 type 对应的 mutation 对象数组，执行 handler(payload) 方法，这个方法就是之前定义的 wrappedMutationHandler(handler)，执行它就相当于执行了 registerMutation 注册的回调函数。

3.  辅助函数

    辅助函数的实现都差不太多，在这里了解一下 mapState

    ```cpp
    export const mapGetters = normalizeNamespace((namespace, getters) => {
      // 返回结果
      const res = {}

      // 遍历规范化参数后的对象
      // getters 就是传递给 mapGetters 的 map 对象或者数组
      normalizeMap(getters).forEach(({ key, val }) => {
        val = namespace + val
        res[key] = function mappedGetter () {
          // 一般不会传入 namespace 参数
          if (namespace && !getModuleByNamespace(this.$store, 'mapGetters', namespace)) {
            return
          }
          // 如果 getter 不存在则报错
          if (process.env.NODE_ENV !== 'production' && !(val in this.$store.getters)) {
            console.error(`[vuex] unknown getter: ${val}`)
            return
          }
          // 返回 getter 值, store.getters 可见上文 resetStoreVM 的分析
          return this.$store.getters[val]
        }
        // mark vuex getter for devtools
        res[key].vuex = true
      })
      return res
    })
    ```

    mapState 在调用了 normalizeMap 函数后，把传入的 states 转换成由 {key, val} 对象构成的数组，接着调用 forEach 方法遍历这个数组，构造一个新的对象，这个新对象每个元素都返回一个新的函数 mappedState，函数对 val 的类型判断，如果 val 是一个函数，则直接调用这个 val 函数，把当前 store 上的 state 和 getters 作为参数，返回值作为 mappedState 的返回值；否则直接把 this.$store.state[val]作为 mappedState 的返回值。为了更直观的理解，我们看下最终 mapState 的效果

    ```cpp
    computed: mapState({
        name: state => state.name,
    })
    // 等同于
    computed: {
        name: this.$store.state.name
    }
    ```