# 第五章 第 3 节 新特性（下）

> 原文：[`www.nowcoder.com/tutorial/10091/eb53e68803884de68bedd1e908a5d76f`](https://www.nowcoder.com/tutorial/10091/eb53e68803884de68bedd1e908a5d76f)

### 3 新特性（下）

#### 3.1 请问你能手写 Promise 吗？

【考点映射】

*   Promise 结构实现（面试撕代码可能性极大）

【频率】★★★★★

【难度】☆☆☆

【参考答案】

Promise 对象是一个构造函数，主要用来生成 Promise 实例

构造函数接受一个函数作为参数，该函数的两个参数分别是 resolve 函数 和 reject 函数

```cpp
//创建一个 Promise 实例
const promise =new Promise(function(resolve, reject){ 
    // ... some code 
    if(/* 异步操作成功 */){
        resolve(value);
    }else{
        reject(error);
    }
});

```

**resolve 函数：**将 Promise 对象的状态从 pending → fulfilled ，在异步操作成功时调用，并将异步操作的结果，作为参数传递给注册在 then 方法上的回调函数（then 方法的第一个参数）；

**reject 函数：**将 Promise 对象的状态从 pending → rejected ，在异步操作失败时调用，并将异步操作报出的错误，作为参数传递给注册在 then 方法上的回调函数（then 方法的第二个参数）

**then 方法：**定义在原型对象 Promise.prototype 上，作用是为 Promise 实例添加状态改变时的回调函数，返回一个新的 Promise 实例（不是原来那个）。第一个参数是 resolved 状态的回调函数，第二个参数是 rejected 状态的回调函数

一步步写出 Promise 的结构（注意：写法有很多，大家一定要注重思考，理解 Promise 的工作原理，不要死记硬背一种代码）

下面是 ES6 版本的整体代码，摘自网络，仅供参考：

```cpp
// 先定义三个常量表示状态
const PENDING = 'pending';
const FULFILLED = 'fulfilled';
const REJECTED = 'rejected';

// 新建 MyPromise 类
class MyPromise {
  constructor(executor){
    // executor 是一个执行器，进入会立即执行
    // 并传入 resolve 和 reject 方法
    try {
      executor(this.resolve, this.reject)
    } catch (error) {
      this.reject(error)
    }
  }

  // 储存状态的变量，初始值是 pending
  status = PENDING;
  // 成功之后的值
  value = null;
  // 失败之后的原因
  reason = null;

  // 存储成功回调函数
  onFulfilledCallbacks = [];
  // 存储失败回调函数
  onRejectedCallbacks = [];

  // 更改成功后的状态
  resolve = (value) => {
    // 只有状态是等待，才执行状态修改
    if (this.status === PENDING) {
      // 状态修改为成功
      this.status = FULFILLED;
      // 保存成功之后的值
      this.value = value;
      // resolve 里面将所有成功的回调拿出来执行
      while (this.onFulfilledCallbacks.length) {
        // Array.shift() 取出数组第一个元素，然后（）调用，shift 不是纯函数，取出后，数组将失去该元素，直到数组为空
        this.onFulfilledCallbacks.shift()(value)
      }
    }
  }

  // 更改失败后的状态
  reject = (reason) => {
    // 只有状态是等待，才执行状态修改
    if (this.status === PENDING) {
      // 状态成功为失败
      this.status = REJECTED;
      // 保存失败后的原因
      this.reason = reason;
      // resolve 里面将所有失败的回调拿出来执行
      while (this.onRejectedCallbacks.length) {
        this.onRejectedCallbacks.shift()(reason)
      }
    }
  }

  then(onFulfilled, onRejected) {
    const realOnFulfilled = typeof onFulfilled === 'function' ? onFulfilled : value => value;
    const realOnRejected = typeof onRejected === 'function' ? onRejected : reason => {throw reason};

    // 为了链式调用这里直接创建一个 MyPromise，并在后面 return 出去
    const promise2 = new MyPromise((resolve, reject) => {
      const fulfilledMicrotask = () =>  {
        // 创建一个微任务等待 promise2 完成初始化
        queueMicrotask(() => {
          try {
            // 获取成功回调函数的执行结果
            const x = realOnFulfilled(this.value);
            // 传入 resolvePromise 集中处理
            resolvePromise(promise2, x, resolve, reject);
          } catch (error) {
            reject(error)
          } 
        })  
      }

      const rejectedMicrotask = () => { 
        // 创建一个微任务等待 promise2 完成初始化
        queueMicrotask(() => {
          try {
            // 调用失败回调，并且把原因返回
            const x = realOnRejected(this.reason);
            // 传入 resolvePromise 集中处理
            resolvePromise(promise2, x, resolve, reject);
          } catch (error) {
            reject(error)
          } 
        }) 
      }
      // 判断状态
      if (this.status === FULFILLED) {
        fulfilledMicrotask() 
      } else if (this.status === REJECTED) { 
        rejectedMicrotask()
      } else if (this.status === PENDING) {
        // 等待
        // 因为不知道后面状态的变化情况，所以将成功回调和失败回调存储起来
        // 等到执行成功失败函数的时候再传递
        this.onFulfilledCallbacks.push(fulfilledMicrotask);
        this.onRejectedCallbacks.push(rejectedMicrotask);
      }
    }) 

    return promise2;
  }

  // resolve 静态方法
  static resolve (parameter) {
    // 如果传入 MyPromise 就直接返回
    if (parameter instanceof MyPromise) {
      return parameter;
    }

    // 转成常规方式
    return new MyPromise(resolve =>  {
      resolve(parameter);
    });
  }

  // reject 静态方法
  static reject (reason) {
    return new MyPromise((resolve, reject) => {
      reject(reason);
    });
  }
}

function resolvePromise(promise2, x, resolve, reject) {
  // 如果相等了，说明 return 的是自己，抛出类型错误并返回
  if (promise2 === x) {
    return reject(new TypeError('Chaining cycle detected for promise #<Promise>'))
  }
  // 判断 x 是不是 MyPromise 实例对象
  if(x instanceof MyPromise) {
    // 执行 x，调用 then 方法，目的是将其状态变为 fulfilled 或者 rejected
    // x.then(value => resolve(value), reason => reject(reason))
    // 简化之后
    x.then(resolve, reject)
  } else{
    // 普通值
    resolve(x)
  }
}

module.exports = MyPromise;

```

**Promise.all()：**

将多个 Promise，包装成一个新的 Promise，只有当所有 Promise 都成功，新的 Promise 才成功，一个失败就返回失败的 Promise

一些日常开发中十分实用的点：

（1）Promise.all()获得的成功结果的数组里面的数据顺序和 Promise.all()接收到的数组顺序是一致的，即便后面的结果返回得慢

（2）Promise.all()可以充分利用并行的优势，返回结果的时间是最长请求的时间，这样可以更快处理请求的结果

**Promise.race()：**

将多个 Promise，包装成一个新的 Promise，新 Promise 的结果由第一个完成的 Promise 决定， 第一个成功则返回成功的 Promise，第一个失败直接返回失败的 Promise

```cpp
/*Promise 函数对象 all 方法，返回一个 promise 只有当所有 promise 都成功，才成功，一个失败就失败*/
function all(promises){
    const values = new Array(promises.length) //用来保存所有成功 value 的数组,长度等于 promises 的长度
    //定义计数器，用来保存成功 promise 的数量
    let resolvedCount = 0

    //返回一个新的 promise
    return new Promise((resolve,reject) =>{
        //遍历 promises 获取每个 promise 的结果
        promises.forEach((p,index) => {
            Promise.resolve(p).then(
                value => {
                //成功一次 加一
                    resolvedCount++
                // p 成功，将成功的 value 保存 values,不能直接 push，得按照 promises 的顺序存
                    values[index] = value
                    //如果全部成功了，将 return 的 promises 改为成功
                    if (resolvedCount===promises.length) {
                        resolve(values)
                    }
                },
                reason => { //一个失败，整个失败
                    reject(reason)
                }
            )
        })
    })
}

/*Promise 函数对象 race 方法，返回一个 promise，其结果由第一个完成的 promise 决定，看传入的 promise 数组 里谁最先完成，看第一个完成的是成功/失败*/
function race(promises){
    return new Promise((resolve,reject) =>{
    //遍历 promises 获取每个 promise 的结果
        promises.forEach((p,index) => {
            Promise.resolve(p).then(
                value => {
               //一旦有成功的，return 的 promise 变为成功
                    resolve(value)
                },
                reason => { //一个失败，return 的 promise 变为失败
                    reject(reason)
                }
            )
        })
    })
}

```

#### 3.2 请问你了解 async/await 吗？

【考点映射】

*   async/await

【频率】★★★★★

【难度】☆☆☆

【参考答案】

async/await 本质上就是一个语法糖，底层实现是带有执行器的 Generator 函数，async 函数就是将 Generator 函数的星号（*）替换成 async，将 yield 替换成 await

可简单理解为 async 用于申明一个 函数 是异步的，而 await 可认为是 async wait 简写，用于等待一个异步方法执行完成

**async 函数：**返回的是一个 Promise 对象。如果在函数中 return 一个直接量，async 会把这个直接量通过 Promise.resolve()封装成 Promise 对象

```cpp
Promise.resolve(x)
//可看作为下面这句的简写
new Promise(resolve => resolve(x))

```

**Promise.resolve(x)：**可以用于快速封装字面量对象或其他对象，将其封装成 Promise 实例

若最外层没有 await 获取其返回值的情况下，应该用原来的方式：.then()链来处理返回的个 Promise 对象

**await ：**通常情况 async 函数返回一个 Promise 对象，故 await 可以用于等 Promise 对象，实际上它在等一个返回值。不仅仅用于等 Promise 对象，它可以等任意表达式的结果，所以，await 后面实际是可以接普通函数调用或者直接量

注意：await 是一个运算符，用于组成表达式，表达式的运算结果取决于等到的返回值：

（1）、返回值不是一个 Promise 对象，运算结果直接为该返回值

（2）、返回值是一个 Promise 对象，await 会阻塞后面的代码，等 Promise 对象完成 resolve，随后得到 resolve 的值，作为 await 表达式运算结果，通常 async 函数调用不会造成阻塞，因为它内部所有的阻塞都被封装在一个 Promise 对象中异步执行了

**async/await 的优势：**处理 then 链，相当于进一步优化回调问题

可利用如下代码进行对比，假设有一共任务，分多个步骤完成，每个步骤都是异步的，而且依赖于上一个步骤的结果。先用 setTimeout 模拟异步操作：

```cpp
/**
 * 传入参数 n，表示这个函数执行的时间（毫秒）
 * 执行的结果是 n + 200，这个值将用于下一步骤
 */
function takeLongTime(n) {
    return new Promise(resolve => {
        setTimeout(() => resolve(n + 200), n);
    });
}
function step1(n) {
    console.log(`step1 with ${n}`);
    return takeLongTime(n);
}
function step2(n) {
    console.log(`step2 with ${n}`);
    return takeLongTime(n);
}
function step3(n) {
    console.log(`step3 with ${n}`);
    return takeLongTime(n);
}

```

用 Promise 方式来实现这三个步骤的处理：

```cpp
function doIt() {
    console.time("doIt");
    const time1 = 300;
    step1(time1)
        .then(time2 => step2(time2))
        .then(time3 => step3(time3))
        .then(result => {
            console.log(`result is ${result}`);
            console.timeEnd("doIt");
        });
}

doIt();

```

用 async/await 实现：

```cpp
async function doIt() {
    console.time("doIt");
    const time1 = 300;
    const time2 = await step1(time1);
    const time3 = await step2(time2);
    const result = await step3(time3);
    console.log(`result is ${result}`);
    console.timeEnd("doIt");
}

doIt();

```

这个代码看起来会更清晰明了，几乎跟同步代码一样

#### 3.3 请问 async/await 中如何进行错误处理？

【考点映射】

*   async/await 错误处理

【频率】★★★★

【难度】☆☆

【参考答案】

await 后的异步操作出错，等同于 async 函数返回的 Promise 对象被 reject，所以最好把 await 命令放在 try...catch 代码块中

```cpp
//写法示例
async function myFunction(){
    try{     
        await somethingThatReturnsAPromise()
    ;}catch(err){     
        console.log(err);
    }
} 

```

此外，可在 await 后面的 Promise 对象再跟一个 catch 方法，处理前面可能出现的错误

对于多个 **await**命令后面的异步操作，如果不存在继发关系，最好让它们同时触发（可用 promise.all()）

```cpp
let foo = await getFoo();
let bar = await getBar();

// 改写成：
let [foo, bar] = await Promise.all([getFoo(), getBar()]);

```

注意：await **只能用在 async 函数之中**，如果用在普通函数，会报错

#### 3.4 请问什么是 Proxy？

【考点映射】

*   Proxy

【频率】★★★★

【难度】☆

【参考答案】

Proxy 是 ES6 为了操作对象引入的一个新 API，可以对目标对象的读取、函数调用等操作进行拦截，然后进行处理

Proxy 不直接操作对象，更像一种代理模式，通过对象的代理对象进行操作，在进行这些操作时，可以添加一些额外操作

基本用法：

一个 Proxy 对象由两个部分组成： target 、handler 。在通过 Proxy 构造函数生成实例对象时，需要提供这两个参数：target 为目标对象， handler 是一个对象，声明了代理 target 的指定行为

```cpp
//具体实例:
let target = {     
    name: 'Tom',     
    age: 24 
} 
let handler = {     
    get: function(target, key) {         
        console.log('getting '+key);         
        return target[key]; // 不是 target.key     
    },     
    set: function(target, key, value) {        
        console.log('setting '+key);         
        target[key] = value;     
    } 
} 
let proxy = new Proxy(target, handler) 
proxy.name     // 实际执行 handler.get 
proxy.age = 25 // 实际执行 handler.set 
// getting name 
// setting age
 // 25

```

Proxy 的常用实例方法：

（1）、**get(target, propKey, receiver)，**用于 target 对象上 propKey 的读取操作

```cpp
let exam ={     
    name: "Tom",     
    age: 24 
} 
let proxy = new Proxy(exam, {   
    get(target, propKey, receiver) {     
        console.log('Getting ' + propKey);     
        return target[propKey];   
    } 
}) 
proxy.name  
// Getting name 
// "Tom"

```

（2）、set(target, propKey, value, receiver)，用于拦截 target 对象上的 propKey 的赋值操作。如果目标对象自身的某个属性，不可写且不可配置，那 set 方法将不起作用

```cpp
let validator = {     
    set: function(obj, prop, value) {         
        if (prop === 'age') {             
            if (!Number.isInteger(value)) {                 
                throw new TypeError ('The age is not an integer');}                                          }            
             if (value > 200) {                 
                 throw new RangeError('The age seems invalid'); }         
             }         // 对于满足条件的 age 属性以及其他属性，直接保存                     
             obj[prop] = value;     
             } 
 };
 let proxy= new Proxy({}, validator) 
 proxy.age = 100; 
 proxy.age           // 100 
 proxy.age = 'oppps' // 报错 
 proxy.age = 300     // 报错

```

#### 3.5 请问什么是 Reflect ？

【考点映射】

*   Reflect

【频率】★★★★

【难度】☆

【参考答案】

Reflect 同样也是 ES6 为了操作对象引入的一个新 API，可以用于获取目标对象的行为，与 Object 类似，但是更易读，它的方法与 Proxy 是对应的

ES6 将 Object 的一些明显属于语言内部的方法移植到了 Reflect 对象上（现阶段某些方***同时存在于 Object 和 Reflect 对象上）

Reflect 对象对某些方法的返回结果进行了修改，使其更合理

Reflect 对象使用函数的方式实现了 Object 的命令式操作

（1）、**Reflect.get(target, name, receiver)，**查找并返回 target 对象的 name 属性

```cpp
let exam = {     
    name: "Tom",     
    age: 24,     
    get info(){         
        return this.name + this.age;     
    } 
} 
Reflect.get(exam, 'name'); // "Tom"  
// 当 target 对象中存在 name 属性的 getter 方法， getter 方法的 this 会绑定

// receiver 
let receiver = {     
    name: "Jerry",     
    age: 20 
} 
Reflect.get(exam, 'info', receiver); // Jerry20   
// 当 name 为不存在于 target 对象的属性时，返回 undefined 
Reflect.get(exam, 'birth'); // undefined   
// 当 target 不是对象时，会报错 
Reflect.get(1, 'name'); // TypeError

```

（2）、**Reflect.set(target, name, value, receiver)，**将 target 的 name 属性设置为 value，返回值为 boolean ，true 表示修改成功，false 表示失败（当 target 为不存在的对象时，会报错）

```cpp
let exam = {     
    name: "Tom",     
    age: 24,     
    set info(value){         
        return this.age = value;     
    } 
} 
exam.age; // 24 
Reflect.set(exam, 'age', 25); // true 
exam.age; // 25   

// value 为空时会将 name 属性清除 
Reflect.set(exam, 'age', ); // true 
exam.age; // undefined   

// 当 target 对象中存在 name 属性 setter 方法时，setter 方法中的 this 会绑定//receiver, 所以修改的实际上是 receiver 的属性, 
let receiver = {     
    age: 18 
} 
Reflect.set(exam, 'info', 1, receiver); // true 
receiver.age; // 1   
let receiver1 = {     
    name: 'oppps' 
} 
Reflect.set(exam, 'info', 1, receiver1); 
receiver1.age; // 1

```

（3）**、Reflect.ownKeys(target)，**用于返回 target 对象的所有属性，等同于 Object.getOwnPropertyNames +Object.getOwnPropertySymbols

```cpp
var exam = {   
    name: 1,   
    [Symbol.for('age')]: 4 
} 
Reflect.ownKeys(exam) // ["name", Symbol(age)]

```

### 3.6 总结

本章主要总结了 ES6 相关面试考点，根据考点出发详细阐述了 ES6 的一些常用常考新特性，针对每个考题给出了参考答案与代码示例，但仅是个人结合所学、各类参考书与网站所得，若存在欠缺与不妥的地方，欢迎大家及时在下方讨论区指出。

下章我们将一起学习前端技术的基石——浏览器相关知识，希望大家能通过本专刊的分析更好地掌握知识与考题!