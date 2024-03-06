# 第四章 第 7 节 前端框架-react1

> 原文：[`www.nowcoder.com/tutorial/10072/77a39dddfc2f4354b7f5f8c68743d098`](https://www.nowcoder.com/tutorial/10072/77a39dddfc2f4354b7f5f8c68743d098)

## 3\. react

#### 3.1 react 虚拟 dom

**参考答案：**

虚拟 dom 是什么？

​ 虚拟 dom 相当于在 js 和真实 dom 中间加了一个缓存，利用 dom diff 算法避免了没有必要的 dom 操作，从而 提高性能。

实现过程

1.  用 JavaScript 对象结构表示 DOM 树的结构
2.  用这个树构建一个真正的 DOM 树，插到文档当中当状态变更的时候，重新构造一棵新的对象树。
3.  用新的树和旧的树进行比较，记录两棵树差异把 2 所记录的差异应用到步骤 1 所构建的真正的 DOM 树上，视图就更新了。

#### 3.2 虚拟 dom 和 real dom 区别 性能差异

**参考答案：**

减少 DOM 的操作：虚拟 dom 可以将多次操作合并为一次操作，减少 DOM 操作的次数

| **Real DOM** | **Virtual DOM** |
| --- | --- |
| 1\. 更新缓慢。 | 1\. 更新更快。 |
| 2\. 可以直接更新 HTML。 | 2\. 无法直接更新 HTML。 |
| 3\. 如果元素更新，则创建新 DOM。 | 3\. 如果元素更新，则更新 JSX 。 |
| 4\. DOM 操作代价很高。 | 4\. DOM 操作非常简单。 |
| 5\. 消耗的内存较多。 | 5\. 很少的内存消耗。 |

#### 3.3 react 组件间通信

**参考答案：**

以下 6 种方法是 react 组件间通信的方式：

*   父组件向子组件通讯: 父组件可以向子组件通过传 props 的方式，向子组件进行通讯
*   子组件向父组件通讯: props+回调的方式,父组件向子组件传递 props 进行通讯，此 props 为作用域为父组件自身的函数，子组件调用该函数，将子组件想要传递的信息，作为参数，传递到父组件的作用域中
*   兄弟组件通信: 找到这两个兄弟节点共同的父节点,结合上面两种方式由父节点转发信息进行通信
*   跨层级通信: `Context`设计目的是为了共享那些对于一个组件树而言是“全局”的数据，例如当前认证的用户、主题或首选语言,对于跨越多层的全局数据通过`Context`通信再适合不过
*   发布订阅模式: 发布者发布事件，订阅者监听事件并做出反应,我们可以通过引入 event 模块进行通信
*   全局状态管理工具: 借助 Redux 或者 Mobx 等全局状态管理工具进行通信,这种工具会维护一个全局状态中心 Store,并根据不同的事件产生新的状态

#### 3.4 radux 的原理

**参考答案：**

**Redux：**Redux 是当今最热门的前端开发库之一。它是 JavaScript 程序的可预测状态容器，用于整个应用的状态管理。使用 Redux 开发的应用易于测试，可以在不同环境中运行，并显示一致的行为。

**数据如何通过 Redux 流动？**

1.  首先，用户（通过 View）发出 Action，发出方式就用到了 dispatch 方法。

2.  然后，Store 自动调用 Reducer，并且传入两个参数：当前 State 和收到的 Action，Reducer 会返回新的 State

3.  State 一旦有变化，Store 就会调用监听函数，来更新 View。

**Redux 遵循的三个原则是什么？**

1.  单一事实来源：整个应用的状态存储在单个 store 中的对象/状态树里。单一状态树可以更容易地跟踪随时间的变化，并调试或检查应用程序。
2.  状态是只读的：改变状态的唯一方法是去触发一个动作。动作是描述变化的普通 JS 对象。就像 state 是数据的最小表示一样，该操作是对数据更改的最小表示。
3.  使用纯函数进行更改：为了指定状态树如何通过操作进行转换，你需要纯函数。纯函数是那些返回值仅取决于其参数值的函数。

**你对“单一事实来源”有什么理解？**

Redux 使用 “Store” 将程序的整个状态存储在同一个地方。因此所有组件的状态都存储在 Store 中，并且它们从 Store 本身接收更新。单一状态树可以更容易地跟踪随时间的变化，并调试或检查程序。

**Redux 由以下组件组成：**

1.  **Action** – 这是一个用来描述发生了什么事情的对象。
2.  **Reducer** – 这是一个确定状态将如何变化的地方。
3.  **Store** – 整个程序的状态/对象树保存在 Store 中。
4.  **View** – 只显示 Store 提供的数据。

**如何在 Redux 中定义 Action？**

React 中的 Action 必须具有 type 属性，该属性指示正在执行的 ACTION 的类型。必须将它们定义为字符串常量，并且还可以向其添加更多的属性。在 Redux 中，action 被名为 Action Creators 的函数所创建。以下是 Action 和 Action Creator 的示例：

```cpp
function addTodo(text) {
       return {
                type: ADD_TODO,    
                 text
    }
}
```

**解释 Reducer 的作用**

Reducers 是纯函数，它规定应用程序的状态怎样因响应 ACTION 而改变。Reducers 通过接受先前的状态和 action 来工作，然后它返回一个新的状态。它根据操作的类型确定需要执行哪种更新，然后返回新的值。如果不需要完成任务，它会返回原来的状态。

**Store 在 Redux 中的意义是什么？**

Store 是一个 JavaScript 对象，它可以保存程序的状态，并提供一些方法来访问状态、调度操作和注册侦听器。应用程序的整个状态/对象树保存在单一存储中。因此，Redux 非常简单且是可预测的。我们可以将中间件传递到 store 来处理数据，并记录改变存储状态的各种操作。所有操作都通过 reducer 返回一个新状态。

**Redux 有哪些优点？**

*   结果的可预测性 - 由于总是存在一个真实来源，即 store ，因此不存在如何将当前状态与动作和应用的其他部分同步的问题。
*   可维护性 - 代码变得更容易维护，具有可预测的结果和严格的结构。
*   服务器端渲染 - 你只需将服务器上创建的 store 传到客户端即可。这对初始渲染非常有用，并且可以优化应用性能，从而提供更好的用户体验。
*   开发人员工具 - 从操作到状态更改，开发人员可以实时跟踪应用中发生的所有事情。
*   社区和生态系统 - Redux 背后有一个巨大的社区，这使得它更加迷人。一个由才华横溢的人组成的大型社区为库的改进做出了贡献，并开发了各种应用。
*   易于测试 - Redux 的代码主要是小巧、纯粹和独立的功能。这使代码可测试且独立。
*   组织 - Redux 准确地说明了代码的组织方式，这使得代码在团队使用时更加一致和简单。

#### **3.5 React 组件生命周期的阶段是什么？**

**参考答案：**

React 组件的生命周期有三个不同的阶段：

1.  初始渲染阶段：这是组件即将开始其生命之旅并进入 DOM 的阶段。
2.  更新阶段：一旦组件被添加到 DOM，它只有在 prop 或状态发生变化时才可能更新和重新渲染。这些只发生在这个阶段。
3.  卸载阶段：这是组件生命周期的最后阶段，组件被销毁并从 DOM 中删除。

#### 3.6 详细解释 React 组件的生命周期方法

**参考答案：**

目前 React 16.8 +的生命周期分为三个阶段,分别是挂载阶段、更新阶段、卸载阶段

挂载阶段:

*   constructor: 构造函数，最先被执行,我们通常在构造函数里初始化 state 对象或者给自定义方法绑定 this
*   getDerivedStateFromProps: `static getDerivedStateFromProps(nextProps, prevState)`,这是个静态方法,当我们接收到新的属性想去修改我们 state，可以使用 getDerivedStateFromProps
*   render: render 函数是纯函数，只返回需要渲染的东西，不应该包含其它的业务逻辑,可以返回原生的 DOM、React 组件、Fragment、Portals、字符串和数字、Boolean 和 null 等内容
*   componentDidMount: 组件装载之后调用，此时我们可以获取到 DOM 节点并操作，比如对 canvas，svg 的操作，服务器请求，订阅都可以写在这个里面，但是记得在 componentWillUnmount 中取消订阅

更新阶段:

*   getDerivedStateFromProps: 此方法在更新个挂载阶段都可能会调用
*   shouldComponentUpdate: `shouldComponentUpdate(nextProps, nextState)`,有两个参数 nextProps 和 nextState，表示新的属性和变化之后的 state，返回一个布尔值，true 表示会触发重新渲染，false 表示不会触发重新渲染，默认返回 true,我们通常利用此生命周期来优化 React 程序性能
*   render: 更新阶段也会触发此生命周期
*   getSnapshotBeforeUpdate: `getSnapshotBeforeUpdate(prevProps, prevState)`,这个方法在 render 之后，componentDidUpdate 之前调用，有两个参数 prevProps 和 prevState，表示之前的属性和之前的 state，这个函数有一个返回值，会作为第三个参数传给 componentDidUpdate，如果你不想要返回值，可以返回 null，此生命周期必须与 componentDidUpdate 搭配使用
*   componentDidUpdate: `componentDidUpdate(prevProps, prevState, snapshot)`,该方法在 getSnapshotBeforeUpdate 方法之后被调用，有三个参数 prevProps，prevState，snapshot，表示之前的 props，之前的 state，和 snapshot。第三个参数是 getSnapshotBeforeUpdate 返回的,如果触发某些回调函数时需要用到 DOM 元素的状态，则将对比或计算的过程迁移至 getSnapshotBeforeUpdate，然后在 componentDidUpdate 中统一触发回调或更新状态。

卸载阶段:

*   componentWillUnmount: 当我们的组件被卸载或者销毁了就会调用，我们可以在这个函数里去清除一些定时器，取消网络请求，清理无效的 DOM 元素等垃圾清理工作

**扩展：**

React 16 之后有三个生命周期被废弃(但并未删除)

*   componentWillMount
*   componentWillReceiveProps
*   componentWillUpdate

官方计划在 17 版本完全删除这三个函数，只保留 UNSAVE_ 前缀的三个函数，目的是为了向下兼容，但是对于开发者而言应该尽量避免使用他们，而是使用新增的生命周期函数替代它们

#### 3.7 react rounter

**参考答案：**

1.  什么是 React 路由？

    React 路由是一个构建在 React 之上的强大的路由库，它有助于向应用程序添加新的屏幕和流。这使 URL 与网页上显示的数据保持同步。它负责维护标准化的结构和行为，并用于开发单页 Web 应用。 React 路由有一个简单的 API。

    ```cpp
    <switch>
        <route exact="" path="’/’" component="{Home}/">
        <route path="’/posts/:id’" component="{Newpost}/">
        <route path="’/posts’" component="{Post}/">
    </route></route></route></switch>
    ```

2.  为什么需要 React 中的路由？

    Router 用于定义多个路由，当用户定义特定的 URL 时，如果此 URL 与 Router 内定义的任何 “路由” 的路径匹配，则用户将重定向到该特定路由。所以基本上我们需要在自己的应用中添加一个 Router 库，允许创建多个路由，每个路由都会向我们提供一个独特的视图

3.  为什么 React Router v4 中使用 switch 关键字 ？

    虽然`<div>`用于封装 Router 中的多个路由，当你想要仅显示要在多个定义的路线中呈现的单个路线时，可以使用 “switch” 关键字。使用时，`<switch>` 标记会按顺序将已定义的 URL 与已定义的路由进行匹配。找到第一个匹配项后，它将渲染指定的路径。从而绕过其它路线。

4.  列出 React Router 的优点

    4.1 就像 React 基于组件一样，在 React Router v4 中，API 是 'All About Components'。可以将 Router 可视化为单个根组件（），其中我们将特定的子路由（）包起来。

    4.2 无需手动设置历史值：在 React Router v4 中，我们要做的就是将路由包装在 组件中。

    4.3 包是分开的：共有三个包，分别用于 Web、Native 和 Core。这使我们应用更加紧凑。基于类似的编码风格很容易进行切换。

#### 3.8 hooks 的优缺点

**参考答案**：

**优点**

**更容易复用代码**

这点应该是 react hooks 最大的优点，它通过自定义 hooks 来复用状态，从而解决了类组件有些时候难以复用逻辑的问题。类组件的逻辑复用方式是高阶组件和 renderProps。hooks 是怎么解决这个复用的问题呢，具体如下：

1.  每调用 useHook 一次都会生成一份独立的状态，这个没有什么黑魔法，函数每次调用都会有一份独立的作用域。
2.  虽然状态(from useState)和副作用(useEffect)的存在依赖于组件，但它们可以在组件外部进行定义。这点是 class component 做不到的，你无法在外部声明 state 和副作用（如 componentDidMount）。

**清爽的代码风格**

函数式编程风格，函数式组件、状态保存在运行环境、每个功能都包裹在函数中，整体风格更清爽，更优雅

**代码量更少**

1.  向 props 或状态取值更加方便，函数组件的取值都从父级作用域直接取，而类组件需要先访问实例引用 this，再访问其属性 state 和 props，多了一步
2.  更改状态也变得更加简单, this.setState({ count:xxx })变成 setCount(xxx)。

**更容易发现无用的状态和函数**

对比类组件，函数组件里面的 unused 状态和函数更容易被发现

**更容易拆分组件**

写函数组件的时候，你会更愿意去拆分组件，因为函数组件写起小组件比类组件要省事。

**缺点**

**部分代码从主动式变成响应式**

写函数组件时，你不得不改变一些写法习惯。你必须把深入理解 useEffect 和 useCallback 这些 api 的第二个参数的作用。其次，还有下面几点：

1.  useEffect 的依赖参数并不好写，你需要花时间去判断该把什么变量加入到依赖数组，幸运的是`eslint-plugin-react-hooks`很多场景可以帮你解决这点，但有时得靠你自己加以判断
2.  useEffect 很容易出错，它是响应式的，当某个依赖项变化时它才会被调用。有时，useEffect 会发生比你预期更多的调用次数。你必须去理解它的调用时机、调用时的状态老旧问题，这不直观，也难以维护，这点在团队协作中很明显，你的队友有时会难以理解你 useEffect 的触发时机以及其作用。

**状态不同步**

不好用的 useEffect，

这绝对可以成为摒弃 react hooks 的理由。函数的运行是独立的，每个函数都有一份独立的作用域。当我们处理复杂逻辑的时候，经常会碰到“引用不是最新”的问题。

#### 3.9 **为什么浏览器无法读取 JSX？**

**参考答案**：

浏览器只能处理 JavaScript 对象，而不能读取常规 JavaScript 对象中的 JSX。所以为了使浏览器能够读取 JSX，首先，需要用像 Babel 这样的 JSX 转换器将 JSX 文件转换为 JavaScript 对象，然后再将其传给浏览器。

#### **3.10 你对 React 的 refs 有什么了解？**

**参考答案**：

Refs 是 React 中引用的简写。它是一个有助于存储对特定的 React 元素或组件的引用的属性，它将由组件渲染配置函数返回。用于对 render() 返回的特定元素或组件的引用。当需要进行 DOM 测量或向组件添加方法时，它们会派上用场。

```cpp
class ReferenceDemo extends React.Component{
     display() {
         const name = this.inputDemo.value;
         document.getElementById('disp').innerHTML = name;
     }
render() {
    return(        
          <div>
            Name: <input type="text" ref={input => this.inputDemo = input} />
            <button name="Click" onClick={this.display}>Click</button>            
            <h2>Hello <span id="disp"></span> !!!</h2>
          </div>
    );
   }
 }
```