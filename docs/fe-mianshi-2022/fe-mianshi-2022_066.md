# 第六章 第 18 节 前端进阶-node1

> 原文：[`www.nowcoder.com/tutorial/10072/ec15cecfb2424f60ae7166ac2ff32f51`](https://www.nowcoder.com/tutorial/10072/ec15cecfb2424f60ae7166ac2ff32f51)

#### 6.1 node 中的模块化导入导出和 ES6 的区别

**参考答案：**

node commonjs 规范模块化

1.  module 对象为模块运行时生成的标识对象，提供模块信息；

2.  exports 为模块导出引用数据类型时，modulex.exports 与 exports 指向的是同一对象，require 导入的是 module.exports 导出的对象；

3.  同一模块导入后存在模块缓存，后续多次导入从缓存中加载；

4.  源模块的引用与导入该模块的引用是同一对象；

5.  最好不要同时使用 module.exports 与 exports，导出对象使用 module.exports，导出变量使用 exports。

es6 规范模块化

1.  es6 通过 export 和 import 实现导出导入功能；

2.  es6 export 支持导出多个变量，export default 是 export 形式的语法糖，表示导出 default 接口；

3.  `import * as xx from 'xx.js'`导入的是 Module 对象，包含 default 接口和其他变量接口；

4.  多个模块导入多次，只会执行一次；

5.  导出引用数据类型时，导出对象与导入对象指向同一个变量，修改导出变量对象，源对象也会发生改变。

6.  导出单个变量建议使用 export default，导出多个变量使用 export。

#### 6.2 Node 中间件

**参考答案**：

**中间件概念**

在 NodeJS 中，中间件主要是指封装所有 Http 请求细节处理的方法。一次 Http 请求通常包含很多工作，如记录日志、ip 过滤、查询字符串、请求体解析、Cookie 处理、权限验证、参数验证、异常处理等，但对于 Web 应用而言，并不希望接触到这么多细节性的处理，因此引入中间件来简化和隔离这些基础设施与业务逻辑之间的细节，让开发者能够关注在业务的开发上，以达到提升开发效率的目的。

中间件的行为比较类似 Java 中过滤器的工作原理，就是在进入具体的业务处理之前，先让过滤器处理。它的工作模型下图所示。

![](img/ee4b471000782b95be0941b3fe04cda0.png)

**中间件工作模型**

中间件机制核心实现

中间件是从 Http 请求发起到响应结束过程中的处理方法，通常需要对请求和响应进行处理，因此一个基本的中间件的形式如下：

```cpp
const middleware = (req, res, next) => {
  // TODO
  next()
}
```

以下通过两种方式的中间件机制的实现来理解中间件是如何工作的。

**方式一**

如下定义三个简单的中间件：

```cpp
const middleware1 = (req, res, next) => {
  console.log('middleware1 start')
  next()
}

const middleware2 = (req, res, next) => {
  console.log('middleware2 start')
  next()
}

const middleware3 = (req, res, next) => {
  console.log('middleware3 start')
  next()
}
```

通过递归的形式，将后续中间件的执行方法传递给当前中间件，在当前中间件执行结束，通过调用`next()`方法执行后续中间件的调用。

```cpp
// 中间件数组
const middlewares = [middleware1, middleware2, middleware3]
function run (req, res) {
  const next = () => {
    // 获取中间件数组中第一个中间件
    const middleware = middlewares.shift()
    if (middleware) {
      middleware(req, res, next)
    }
  }
  next()
}
run() // 模拟一次请求发起
```

执行以上代码，可以看到如下结果：

```cpp
middleware1 start
middleware2 start
middleware3 start
```

如果中间件中有异步操作，需要在异步操作的流程结束后再调用`next()`方法，否则中间件不能按顺序执行。改写 middleware2 中间件：

```cpp
const middleware2 = (req, res, next) => {
  console.log('middleware2 start')
  new Promise(resolve => {
    setTimeout(() => resolve(), 1000)
  }).then(() => {
    next()
  })
}
```

执行结果与之前一致，不过 middleware3 会在 middleware2 异步完成后执行。

![img](img/55ff4c7a71a84b2fb20b497e43861d35.png)

执行结果

**方式二**

有些中间件不止需要在业务处理前执行，还需要在业务处理后执行，比如统计时间的日志中间件。在方式一情况下，无法在`next()`为异步操作时再将当前中间件的其他代码作为回调执行。因此可以将`next()`方法的后续操作封装成一个`Promise`对象，中间件内部就可以使用`next.then()`形式完成业务处理结束后的回调。改写`run()`方法如下：

```cpp
function run (req, res) {
  const next = () => {
    const middleware = middlewares.shift()
    if (middleware) {
      // 将 middleware(req, res, next)包装为 Promise 对象
      return Promise.resolve(middleware(req, res, next))
    }
  }
  next()
}
```

中间件的调用方式需改写为：

```cpp
const middleware1 = (req, res, next) => {
  console.log('middleware1 start')
  // 所有的中间件都应返回一个 Promise 对象
  // Promise.resolve()方法接收中间件返回的 Promise 对象，供下层中间件异步控制
  return next().then(() => {
    console.log('middleware1 end')
  })
}
```

得益于`async`函数的自动异步流程控制，中间件也可以用如下方式来实现：

```cpp
// async 函数自动返回 Promise 对象
const middleware2 = async (req, res, next) => {
  console.log('middleware2 start')
  await new Promise(resolve => {
    setTimeout(() => resolve(), 1000)
  })
  await next()
  console.log('middleware2 end')
}

const middleware3 = async (req, res, next) => {
  console.log('middleware3 start')
  await next()
  console.log('middleware3 end')
}
```

执行结果如下：

![](img/1ed290f3910a05e332fbc4f9322067ff.png)

执行结果

以上描述了中间件机制中多个异步中间件的调用流程，实际中间件机制的实现还需要考虑异常处理、路由等。

在`express`框架中，中间件的实现方式为方式一，并且全局中间件和内置路由中间件中根据请求路径定义的中间件共同作用，不过无法在业务处理结束后再调用当前中间件中的代码。`koa2`框架中中间件的实现方式为方式二，将`next()`方法返回值封装成一个`Promise`，便于后续中间件的异步流程控制，实现了`koa2`框架提出的洋葱圈模型，即每一层中间件相当于一个球面，当贯穿整个模型时，实际上每一个球面会穿透两次。

koa2 中间件洋葱圈模型

`koa2`框架的中间件机制实现得非常简洁和优雅，这里学习一下框架中组合多个中间件的核心代码。

```cpp
function compose (middleware) {
  if (!Array.isArray(middleware)) throw new TypeError('Middleware stack must be an array!')
  for (const fn of middleware) {
    if (typeof fn !== 'function') throw new TypeError('Middleware must be composed of functions!')
  }
  return function (context, next) {
    let index = -1
    return dispatch(0)
    function dispatch (i) {
      // index 会在 next()方法调用后累加，防止 next()方法重复调用
      if (i <= index) return Promise.reject(new Error('next() called multiple times'))
      index = i
      let fn = middleware[i]
      if (i === middleware.length) fn = next
      if (!fn) return Promise.resolve()
      try {
        // 核心代码
        // 包装 next()方法返回值为 Promise 对象
        return Promise.resolve(fn(context, dispatch.bind(null, i + 1)));
      } catch (err) {
        // 遇到异常中断后续中间件的调用
        return Promise.reject(err)
      }
    }
  }
}
```