# 第五章 第 14 节 前端基础-js 工作原理

> 原文：[`www.nowcoder.com/tutorial/10072/615aaab13fde47af98ea4a68bdf8d1fe`](https://www.nowcoder.com/tutorial/10072/615aaab13fde47af98ea4a68bdf8d1fe)

#### 7.1 为什么 js 是单线程

**参考答案：**

这主要和 js 的用途有关，js 是作为浏览器的脚本语言，主要是实现用户与浏览器的交互，以及操作 dom；这决定了它只能是单线程，否则会带来很复杂的同步问题。 举个例子：如果 js 被设计了多线程，如果有一个线程要修改一个 dom 元素，另一个线程要删除这个 dom 元素，此时浏览器就会一脸茫然，不知所措。所以，为了避免复杂性，从一诞生，JavaScript 就是单线程，这已经成了这门语言的核心特征，将来也不会改变

**扩展：**

什么是进程？

进程：是 cpu 分配资源的最小单位；（是能拥有资源和独立运行的最小单位）

什么是线程？

线程：是 cpu 调度的最小单位；（线程是建立在进程的基础上的一次程序运行单位，一个进程中可以有多个线程）

浏览器是多进程的？

放在浏览器中，每打开一个 tab 页面，其实就是新开了一个进程，在这个进程中，还有 ui 渲染线程，js 引擎线程，http 请求线程等。 所以，浏览器是一个多进程的。

为了利用多核 CPU 的计算能力，HTML5 提出 Web Worker 标准，允许 JavaScript 脚本创建多个线程，但是子线程完全受主线程控制，且不得操作 DOM。所以，这个新标准并没有改变 JavaScript 单线程的本质。

#### 7.2 宏微队列及执行顺序

**参考答案：**

JS 中用来存储待执行回调函数的队列包含 2 个不同特定的列队

*   **宏列队**：用来保存待执行的宏任务（回调），比如：定时器回调、DOM 事件回调、ajax 回调
*   **微列队**：用来保存待执行的微任务（回调），比如：promise 的回调、MutationObserver 的回调

JS 执行时会区别这 2 个队列

*   JS 引擎首先必须先执行所有的初始化同步任务代码
*   每次准备取出第一个宏任务执行前, 都要将所有的微任务一个一个取出来执行，也就是优先级比宏任务高，且与微任务所处的代码位置无关

下面这个例子可以看出 `Promise` 要先于 `setTimeout` 执行：

```cpp
　　setTimeout(() => { //立即放入宏队列
      console.log('timeout callback1（）')
      Promise.resolve(3).then(
        value => { //立即放入微队列
          console.log('Promise onResolved3()', value)
        }
      )
    }, 0)

    setTimeout(() => { //立即放入宏队列
      console.log('timeout callback2（）')
    }, 0)

    Promise.resolve(1).then(
      value => { //立即放入微队列
        console.log('Promise onResolved1()', value)
        setTimeout(() => {
          console.log('timeout callback3（）', value)
        }, 0)
      }
    )

    Promise.resolve(2).then(
      value => { //立即放入微队列
        console.log('Promise onResolved2()', value)
      }
    )

    // Promise onResolved1() 1
    // Promise onResolved2() 2
    // timeout callback1（）
    // Promise onResolved3() 3
    // timeout callback2（）
    // timeout callback3（） 1
```

#### 7.3 死锁

**参考答案：**

死锁是指两个或两个以上的进程在执行过程中，由于竞争资源而造成阻塞的现象，若无外力作用，它们都将无法继续执行

产生原因

*   竞争资源引起进程死锁
*   可剥夺和非剥夺资源
*   竞争非剥夺资源
*   竞争临时性资源
*   进程推进顺序不当

产生条件

1.  互斥条件：涉及的资源是非共享的
    *   涉及的资源是非共享的,一段时间内某资源只由一个进程占用,如果此时还有其它进程请求资源，则请求者只能等待，直至占有资源的进程用毕释放
2.  不剥夺条件：不能强行剥夺进程拥有的资源
    *   进程已获得的资源，在未使用完之前，不能被剥夺，只能在使用完时由自己释放
3.  请求和保持条件：进程在等待一新资源时继续占有已分配的资源
    *   指进程已经保持至少一个资源，但又提出了新的资源请求，而该资源已被其它进程占有，此时请求进程阻塞，但又对自己已获得的其它资源保持不放 环路等待条件：存在一种进程的循环链，链中的每一个进程已获得的资源同时被链中的下一个进程所请求 在发生死锁时，必然存在一个进程——资源的环形链

解决办法

只要打破四个必要条件之一就能有效预防死锁的发生

#### 7.4 暂时性死区

**参考答案：**

暂时性死区的本质就是，只要一进入当前作用域，所要使用的变量就已经存在了，但是不可获取，只有等到声明变量的那一行代码出现，才可以获取和使用该变量。

**扩展：**

let 、const 与暂时性死区

`let` 或 `const` 声明的变量拥有暂时性死区（TDZ）：当进入它的作用域，它不能被访问（获取或设置）直到执行到达声明。

首先看看不具有暂时性死区的 `var`：

*   当进入 `var` 变量的作用域（包围它的函数），立即为它创建（绑定）存储空间。变量会立即被初始化并赋值为 `undefined`。
*   当执行到变量声明的时候，如果变量定义了值则会被赋值。

通过 `let` 声明的变量拥有暂时性死区，生命周期如下：

*   当进入 `let` 变量的作用域（包围它的语法块），立即为它创建（绑定）存储空间。此时变量仍是未初始化的。
*   获取或设置未初始化的变量将抛出异常 `ReferenceError`。
*   当执行到变量声明的时候，如果变量定义了值则会被赋值。如果没有定义值，则赋值为 `undefined`。

`const` 工作方式与 `let` 类似，但是定义的时候必须赋值并且不能改变。

在 TDZ 内部，如果获取或设置变量将抛出异常：

```cpp
if (true) { // enter new scope, TDZ starts
    // Uninitialized binding for `tmp` is created

    tmp = 'abc'; // ReferenceError
    console.log(tmp); // ReferenceError

    let tmp; // TDZ ends, `tmp` is initialized with `undefined`
    console.log(tmp); // undefined

    tmp = 123;
    console.log(tmp); // 123
}
```

下面的示例将演示死区（dead zone）是真正短暂的（基于时间）和不受空间条件限制（基于位置）

```cpp
if (true) { // enter new scope, TDZ starts
    const func = function () {
        console.log(myVar); // OK!
    };

    // Here we are within the TDZ and
    // accessing `myVar` would cause a `ReferenceError`

    let myVar = 3; // TDZ ends
    func(); // called outside TDZ
}
```

`typeof` 与暂时性死区

变量在暂时性死区无法被访问，所以无法对它使用 `typeof`：

```cpp
if (true) {
    console.log(typeof tmp); // ReferenceError
    let tmp;
}
```

#### 7.5 面向对象的三个特征，分别说一下什么意思

**参考答案：**

概念：

**封装：**将对象运行所需的资源封装在程序对象中——基本上，是方法和数据。对象是“公布其接口”。其他附加到这些接口上的对象不需要关心对象实现的方法即可使用这个对象。这个概念就是“不要告诉我你是怎么做的，只要做就可以了。”对象可以看作是一个自我包含的原子。对象接口包括了公共的方法和初始化数据。

**继承：** 继承可以解决代码复用，让编程更加靠近人类思维。当多个类存在相同的属性(变量)和方法时，可以从这些类中抽象出父类，在父类中定义这些相同的属性和方法，所有的子类不需要重新定义这些属性和方法，只需要通过继承父类中的属性和方法。

**多态：** 多态是指一个引用(类型)在不同情况下的多种状态。也可以理解成：多态是指通过指向父类的引用，来调用在不同子类中实现的方法。

特点：

**封装**可以隐藏实现细节，使得代码模块化；

**继承**可以扩展已存在的代码模块（类），它们的目的都是为了——代码重用。

**多态**就是相同的事物，调用其相同的方法，参数也相同时，但表现的行为却不同。多态分为两种，一种是行为多态与对象的多态