# 第五章 第 6 节 前端基础-异步 1

> 原文：[`www.nowcoder.com/tutorial/10072/50d3c6af7f704836b6f5dae6d01881ff`](https://www.nowcoder.com/tutorial/10072/50d3c6af7f704836b6f5dae6d01881ff)

#### 3.1 promise 和 async await 区别

**参考答案：**

*   **概念**
    **Promise** 是异步编程的一种解决方案，比传统的解决方案——回调函数和事件——更合理和更强大，简单地说，Promise 好比容器，里面存放着一些未来才会执行完毕（异步）的事件的结果，而这些结果一旦生成是无法改变的

    **async await**也是异步编程的一种解决方案，他遵循的是 Generator 函数的语法糖，他拥有内置执行器，不需要额外的调用直接会自动执行并输出结果，它返回的是一个 Promise 对象。

*   ##### 两者的区别

    1.  Promise 的出现解决了传统 callback 函数导致的“地域回调”问题，但它的语法导致了它向纵向发展行成了一个回调链，遇到复杂的业务场景，这样的语法显然也是不美观的。而 async await 代码看起来会简洁些，使得异步代码看起来像同步代码，await 的本质是可以提供等同于”同步效果“的等待异步返回能力的语法糖，只有这一句代码执行完，才会执行下一句。
    2.  async await 与 Promise 一样，是非阻塞的。
    3.  async await 是基于 Promise 实现的，可以说是改良版的 Promise，它不能用于普通的回调函数。

#### 3.2 defer 和 async 区别

**参考答案：**

区别主要在于一个执行时间,defer 会在文档解析完之后执行,并且多个 defer 会按照顺序执行,而 async 则是在 js 加载好之后就会执行,并且多个 async,哪个加载好就执行哪个

**解析：**

在没有 defer 或者 async 的情况下：会立即执行脚本,所以通常建议把 script 放在 body 最后

```cpp
<script src="script.js"></script>
```

async：有 async 的话,加载和渲染后续文档元素的过程将和 script.js 的加载与执行并行进行（异步）。
但是多个 js 文件的加载顺序不会按照书写顺序进行

```cpp
<script async src="script.js"></script>
```

derer：有 derer 的话,加载后续文档元素的过程将和 script.js 的加载并行进行（异步），但是 script.js 的执行要在所有元素解析完成之后，DOMContentLoaded 事件触发之前完成,并且多个 defer 会按照顺序进行加载。

```cpp
<script defer src="script.js"></script>
```

#### 3.3\. 同步和异步

**参考答案：**

同步

*   指在 **主线程**上排队执行的任务，只有前一个任务执行完毕，才能继续执行下一个任务。
*   也就是调用一旦开始，必须这个调用 **返回结果**(划重点——）才能继续往后执行。程序的执行顺序和任务排列顺序是一致的。

异步

*   异步任务是指不进入主线程，而进入 **任务队列**的任务，只有任务队列通知主线程，某个异步任务可以执行了，该任务才会进入主线程。
*   每一个任务有一个或多个 **回调函数**。前一个任务结束后，不是执行后一个任务,而是执行回调函数，后一个任务则是不等前一个任务结束就执行。
*   程序的执行顺序和任务的排列顺序是**不一致**的，异步的。
*   我们常用的 setTimeout 和 setInterval 函数，Ajax 都是异步操作。

#### 3.4 实现异步的方法

**参考答案：**

回调函数（Callback）、事件监听、发布订阅、Promise/A+、生成器 Generators/ yield、async/await

1.  JS 异步编程进化史：callback -> promise -> generator -> async + await

2.  async/await 函数的实现，就是将 Generator 函数和自动执行器，包装在一个函数里。

3.  async/await 可以说是异步终极解决方案了。

    (1) async/await 函数相对于 Promise，优势体现在：

    *   处理 then 的调用链，能够更清晰准确的写出代码
    *   并且也能优雅地解决回调地狱问题。

    当然 async/await 函数也存在一些缺点，因为 await 将异步代码改造成了同步代码，如果多个异步代码没有依赖性却使用了 await 会导致性能上的降低，代码没有依赖性的话，完全可以使用 Promise.all 的方式。

    (2) async/await 函数对 Generator 函数的改进，体现在以下三点：

    *   内置执行器。 Generator 函数的执行必须靠执行器，所以才有了 co 函数库，而 async 函数自带执行器。也就是说，**async 函数的执行，与普通函数一模一样，只要一行**。
    *   更广的适用性。 co 函数库约定，yield 命令后面只能是 Thunk 函数或 Promise 对象，而 **async 函数的 await 命令后面，可以跟 Promise 对象和原始类型的值（数值、字符串和布尔值，但这时等同于同步操作）**。
    *   更好的语义。 async 和 await，比起星号和 yield，语义更清楚了。async 表示函数里有异步操作，await 表示紧跟在后面的表达式需要等待结果。

**解析：**

1.  回调函数（Callback）

    回调函数是异步操作最基本的方法。以下代码就是一个回调函数的例子：

    ```cpp
    ajax(url, () => {
        // 处理逻辑
    })
    ```

    但是回调函数有一个致命的弱点，就是容易写出**回调地狱（Callback hell）**。假设多个请求存在依赖性，你可能就会写出如下代码：

    ```cpp
    ajax(url, () => {
        // 处理逻辑
        ajax(url1, () => {
            // 处理逻辑
            ajax(url2, () => {
                // 处理逻辑
            })
        })
    })
    ```

    回调函数的优点是简单、容易理解和实现，缺点是不利于代码的阅读和维护，各个部分之间高度耦合，使得程序结构混乱、流程难以追踪（尤其是多个回调函数嵌套的情况），而且每个任务只能指定一个回调函数。此外它不能使用 try catch 捕获错误，不能直接 return。

2.  事件监听

    这种方式下，**异步任务的执行不取决于代码的顺序，而取决于某个事件是否发生**。

    下面是两个函数 f1 和 f2，编程的意图是 f2 必须等到 f1 执行完成，才能执行。首先，为 f1 绑定一个事件（这里采用的 jQuery 的写法）

    ```cpp
    f1.on('done', f2);
    ```

    上面这行代码的意思是，当 f1 发生 done 事件，就执行 f2。然后，对 f1 进行改写：

    ```cpp
    function f1() {
      setTimeout(function () {
        // ...
        f1.trigger('done');
      }, 1000);
    }
    ```

    上面代码中，f1.trigger('done')表示，执行完成后，立即触发 done 事件，从而开始执行 f2。

    这种方法的优点是比较容易理解，可以绑定多个事件，每个事件可以指定多个回调函数，而且可以"去耦合"，有利于实现模块化。缺点是整个程序都要变成事件驱动型，运行流程会变得很不清晰。阅读代码的时候，很难看出主流程。

3.  发布订阅

    我们假定，存在一个"信号中心"，某个任务执行完成，就向信号中心"发布"（publish）一个信号，其他任务可以向信号中心"订阅"（subscribe）这个信号，从而知道什么时候自己可以开始执行。这就叫做"发布/订阅模式"（publish-subscribe pattern），又称"观察者模式"（observer pattern）。

    首先，f2 向信号中心 jQuery 订阅 done 信号。

    ```cpp
    jQuery.subscribe('done', f2);
    ```

    然后，f1 进行如下改写：

    ```cpp
    function f1() {
      setTimeout(function () {
        // ...
        jQuery.publish('done');
      }, 1000);
    }
    ```

    上面代码中，jQuery.publish('done')的意思是，f1 执行完成后，向信号中心 jQuery 发布 done 信号，从而引发 f2 的执行。 f2 完成执行后，可以取消订阅（unsubscribe）

    ```cpp
    jQuery.unsubscribe('done', f2);
    ```

    这种方法的性质与“事件监听”类似，但是明显优于后者。因为可以通过查看“消息中心”，了解存在多少信号、每个信号有多少订阅者，从而监控程序的运行。

4.  Promise/A+

    Promise 本意是承诺，在程序中的意思就是承诺我过一段时间后会给你一个结果。 什么时候会用到过一段时间？答案是异步操作，异步是指可能比较长时间才有结果的才做，例如网络请求、读取本地文件等

    4.1 Promise 的三种状态

    *   Pending----Promise 对象实例创建时候的初始状态
    *   Fulfilled----可以理解为成功的状态
    *   Rejected----可以理解为失败的状态

    ![](img/7a641ef9b66314e5f83924a0a20c843e.png)

    **这个承诺一旦从等待状态变成为其他状态就永远不能更改状态了**，比如说一旦状态变为 resolved 后，就不能 再次改变为 Fulfilled

    ```cpp
    let p = new Promise((resolve, reject) => {
      reject('reject')
      resolve('success')//无效代码不会执行
    })
    p.then(
      value => {
        console.log(value)
      },
      reason => {
        console.log(reason)//reject
      }
    )
    ```

    当我们在构造 Promise 的时候，构造函数内部的代码是立即执行的

    ```cpp
    new Promise((resolve, reject) => {
      console.log('new Promise')
      resolve('success')
    })
    console.log('end')
    // new Promise => end
    ```

4.2 promise 的链式调用

*   每次调用返回的都是一个新的 Promise 实例(这就是 then 可用链式调用的原因)

*   如果 then 中返回的是一个结果的话会把这个结果传递下一次 then 中的成功回调

*   如果 then 中出现异常,会走下一个 then 的失败回调

*   在 then 中使用了 return，那么 return 的值会被 Promise.resolve() 包装(见例 1，2)

*   then 中可以不传递参数，如果不传递会透到下一个 then 中(见例 3)

*   catch 会捕获到没有捕获的异常

    接下来我们看几个例子：

    ```cpp
    // 例 1
    Promise.resolve(1)
    .then(res => {
      console.log(res)
      return 2 //包装成 Promise.resolve(2)
    })
    .catch(err => 3)
    .then(res => console.log(res))
    复制代码
    // 例 2
    Promise.resolve(1)
    .then(x => x + 1)
    .then(x => {
      throw new Error('My Error')
    })
    .catch(() => 1)
    .then(x => x + 1)
    .then(x => console.log(x)) //2
    .catch(console.error)
    复制代码
    // 例 3
    let fs = require('fs')
    function read(url) {
    return new Promise((resolve, reject) => {
      fs.readFile(url, 'utf8', (err, data) => {
        if (err) reject(err)
        resolve(data)
      })
    })
    }
    read('./name.txt')
    .then(function(data) {
      throw new Error() //then 中出现异常,会走下一个 then 的失败回调
    }) //由于下一个 then 没有失败回调，就会继续往下找，如果都没有，就会被 catch 捕获到
    .then(function(data) {
      console.log('data')
    })
    .then()
    .then(null, function(err) {
      console.log('then', err)// then error
    })
    .catch(function(err) {
      console.log('error')
    })
    ```

    Promise 不仅能够捕获错误，而且也很好地解决了回调地狱的问题，可以把之前的回调地狱例子改写为如下代码：

    ```cpp
    ajax(url)
    .then(res => {
        console.log(res)
        return ajax(url1)
    }).then(res => {
        console.log(res)
        return ajax(url2)
    }).then(res => console.log(res))
    ```

    它也是存在一些缺点的，比如无法取消 Promise，错误需要通过回调函数捕获。

4.  生成器 Generators/ yield

    Generator 函数是 ES6 提供的一种异步编程解决方案，语法行为与传统函数完全不同，Generator 最大的特点就是可以控制函数的执行。

    *   语法上，首先可以把它理解成，Generator 函数是一个状态机，封装了多个内部状态。

    *   **Generator 函数除了状态机，还是一个遍历器对象生成函数**。

    *   **可暂停函数, yield 可暂停，next 方法可启动，每次返回的是 yield 后的表达式结果**。

    *   yield 表达式本身没有返回值，或者说总是返回 undefined。**next 方法可以带一个参数，该参数就会被当作上一个 yield 表达式的返回值**。

        我们先来看个例子：

    ```cpp
    function *foo(x) {
      let y = 2 * (yield (x + 1))
      let z = yield (y / 3)
      return (x + y + z)
    }
    let it = foo(5)
    console.log(it.next())   // => {value: 6, done: false}
    console.log(it.next(12)) // => {value: 8, done: false}
    console.log(it.next(13)) // => {value: 42, done: true}
    ```

    可能结果跟你想象不一致，接下来我们逐行代码分析：

    *   首先 Generator 函数调用和普通函数不同，它会返回一个迭代器

    *   当执行第一次 next 时，传参会被忽略，并且函数暂停在 yield (x + 1) 处，所以返回 5 + 1 = 6

    *   当执行第二次 next 时，传入的参数 12 就会被当作上一个 yield 表达式的返回值，如果你不传参，yield 永远返回 undefined。此时 let y = 2 * 12，所以第二个 yield 等于 2 * 12 / 3 = 8

    *   当执行第三次 next 时，传入的参数 13 就会被当作上一个 yield 表达式的返回值，所以 z = 13, x = 5, y = 24，相加等于 42

        我们再来看个例子：有三个本地文件，分别 1.txt,2.txt 和 3.txt，内容都只有一句话，下一个请求依赖上一个请求的结果，想通过 Generator 函数依次调用三个文件

        ```cpp
        1.txt
        //1.txt 文件
        ```

        ```cpp
        2.txt
        //2.txt 文件
        ```

        ```cpp
        3.txt
        //3.txt 文件
        ```

        ```cpp
        let fs = require('fs')
        function read(file) {
          return new Promise(function(resolve, reject) {
            fs.readFile(file, 'utf8', function(err, data) {
              if (err) reject(err)
              resolve(data)
            })
          })
        }
        function* r() {
          let r1 = yield read('./1.txt')
          let r2 = yield read(r1)
          let r3 = yield read(r2)
          console.log(r1)
          console.log(r2)
          console.log(r3)
        }
        let it = r()
        let { value, done } = it.next()
        value.then(function(data) { // value 是个 promise
          console.log(data) //data=>2.txt
          let { value, done } = it.next(data)
          value.then(function(data) {
            console.log(data) //data=>3.txt
            let { value, done } = it.next(data)
            value.then(function(data) {
              console.log(data) //data=>结束
            })
          })
        })
        ```

    从上例中我们看出手动迭代`Generator` 函数很麻烦，实现逻辑有点绕，而实际开发一般会配合 `co` 库去使用。**`co`是一个为 Node.js 和浏览器打造的基于生成器的流程控制工具，借助于 Promise，你可以使用更加优雅的方式编写非阻塞代码**。

    安装`co`库只需：`npm install co`

    上面例子只需两句话就可以轻松实现

    ```cpp
    function* r() {
      let r1 = yield read('./1.txt')
      let r2 = yield read(r1)
      let r3 = yield read(r2)
      console.log(r1)
      console.log(r2)
      console.log(r3)
    }
    let co = require('co')
    co(r()).then(function(data) {
      console.log(data)
    })
    // 2.txt=>3.txt=>结束=>undefined
    ```

    我们可以通过 Generator 函数解决回调地狱的问题，可以把之前的回调地狱例子改写为如下代码：

    ```cpp
    function *fetch() {
        yield ajax(url, () => {})
        yield ajax(url1, () => {})
        yield ajax(url2, () => {})
    }
    let it = fetch()
    let result1 = it.next()
    let result2 = it.next()
    let result3 = it.next()
    ```

5.  async/await

    5.1 Async/Await 简介

    ​ 使用 async/await，你可以轻松地达成之前使用生成器和 co 函数所做到的工作,它有如下特点：

    ​ 1\. async/await 是基于 Promise 实现的，它不能用于普通的回调函数。

    ​ 2\. async/await 与 Promise 一样，是非阻塞的。

    ```cpp
      3\. async/await 使得异步代码看起来像同步代码，这正是它的魔力所在。
    ```

    **一个函数如果加上 async ，那么该函数就会返回一个 Promise**

    ```cpp
    async function async1() {
      return "1"
    }
    console.log(async1()) // -> Promise {<resolved>: "1"}
    ```

    Generator 函数依次调用三个文件那个例子用 async/await 写法，只需几句话便可实现

    ```cpp
    let fs = require('fs')
    function read(file) {
      return new Promise(function(resolve, reject) {
        fs.readFile(file, 'utf8', function(err, data) {
          if (err) reject(err)
          resolve(data)
        })
      })
    }
    async function readResult(params) {
      try {
        let p1 = await read(params, 'utf8')//await 后面跟的是一个 Promise 实例
        let p2 = await read(p1, 'utf8')
        let p3 = await read(p2, 'utf8')
        console.log('p1', p1)
        console.log('p2', p2)
        console.log('p3', p3)
        return p3
      } catch (error) {
        console.log(error)
      }
    }
    readResult('1.txt').then( // async 函数返回的也是个 promise
      data => {
        console.log(data)
      },
      err => console.log(err)
    )
    // p1 2.txt
    // p2 3.txt
    // p3 结束
    // 结束
    ```

5.2 Async/Await 并发请求

如果请求两个文件，毫无关系，可以通过并发请求

```cpp
   let fs = require('fs')
   function read(file) {
     return new Promise(function(resolve, reject) {
       fs.readFile(file, 'utf8', function(err, data) {
         if (err) reject(err)
         resolve(data)
       })
     })
   }
   function readAll() {
     read1()
     read2()//这个函数同步执行
   }
   async function read1() {
     let r = await read('1.txt','utf8')
     console.log(r)
   }
   async function read2() {
     let r = await read('2.txt','utf8')
     console.log(r)
   }
   readAll() // 2.txt 3.txt
```