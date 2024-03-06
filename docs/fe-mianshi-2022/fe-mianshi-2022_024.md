# 第五章 第 5 节 前端基础-js4

> 原文：[`www.nowcoder.com/tutorial/10072/d29d56daf9da4853b4326ba417c6e45d`](https://www.nowcoder.com/tutorial/10072/d29d56daf9da4853b4326ba417c6e45d)

#### 2.23 手写数组转树

**参考答案**：

**问题：**

```cpp
// 例如将 input 转成 output 的形式
let input = [
    {
        id: 1, val: '学校', parentId: null
    }, {
        id: 2, val: '班级 1', parentId: 1
    }, {
        id: 3, val: '班级 2', parentId: 1
    }, {
        id: 4, val: '学生 1', parentId: 2
    }, {
        id: 5, val: '学生 2', parentId: 3
    }, {
        id: 6, val: '学生 3', parentId: 3
    },
]

let output = {
    id: 1,
    val: '学校',
    children: [{
        id: 2,
        val: '班级 1',
        children: [
            {
                id: 4,
                val: '学生 1',
                children: []
            },
            {
                id: 5,
                val: '学生 2',
                children: []
            }
        ]
    }, {
        id: 3,
        val: '班级 2',
        children: [{
            id: 6,
            val: '学生 3',
            children: []
        }]
    }]
}
```

**答案**：

```cpp
// 代码实现
function arrayToTree(array) {
    let root = array[0]
    array.shift()
    let tree = {
        id: root.id,
        val: root.val,
        children: array.length > 0 ? toTree(root.id, array) : []
    }
    return tree;
}

function toTree(parenId, array) {
    let children = []
    let len = array.length
    for (let i = 0; i < len; i++) {
        let node = array[i]
        if (node.parentId === parenId) {
            children.push({
                id: node.id,
                val: node.val,
                children: toTree(node.id, array)
            })
        }
    }
    return children
}

console.log(arrayToTree(input))
```

#### 2.24 介绍下 Set、Map、WeakSet 和 WeakMap 的区别？

**参考答案**：

**Set**

1.  成员不能重复；
2.  只有键值，没有键名，有点类似数组；
3.  可以遍历，方法有 `add、delete、has`

**WeakSet**

1.  成员都是对象（引用）；
2.  成员都是弱引用，随时可以消失（不计入垃圾回收机制）。可以用来保存 DOM 节点，不容易造成内存泄露；
3.  不能遍历，方法有 `add、delete、has`；

**Map**

1.  本质上是键值对的集合，类似集合；
2.  可以遍历，方法很多，可以跟各种数据格式转换；

**WeakMap**

1.  只接收对象为键名（null 除外），不接受其他类型的值作为键名；
2.  键名指向的对象，不计入垃圾回收机制；
3.  不能遍历，方法同 `get、set、has、delete`；

#### 2.25 简单说说 js 中有哪几种内存泄露的情况

**参考答案**：

1.  意外的全局变量；
2.  闭包；
3.  未被清空的定时器；
4.  未被销毁的事件监听；
5.  DOM 引用；

#### 2.26 异步笔试题

请写出下面代码的运行结果：

```cpp
// 今日头条面试题

async function async1() {

  console.log('async1 start')

  await async2()

  console.log('async1 end')

}

async function async2() {

  console.log('async2')

}

console.log('script start')

setTimeout(function () {

  console.log('settimeout')

})

async1()

new Promise(function (resolve) {

  console.log('promise1')

  resolve()

}).then(function () {

  console.log('promise2')

})

console.log('script end')
```

题目的本质，就是考察 setTimeout、promise、async await 的实现及执行顺序，以及 JS 的事件循环的相关问题。

答案：

```cpp
script start
async1 start
async2
promise1
script end
async1 end
promise2
settimeout
```

#### 2.27 json 和 xml 数据的区别

**参考答案**：

1.  数据体积方面：xml 是重量级的，json 是轻量级的，传递的速度更快些。
2.  数据传输方面：xml 在传输过程中比较占带宽，json 占带宽少，易于压缩。
3.  数据交互方面：json 与 javascript 的交互更加方便，更容易解析处理，更好的进行数据交互
4.  数据描述方面：json 对数据的描述性比 xml 较差
5.  xml 和 json 都用在项目交互下，xml 多用于做配置文件，json 用于数据交互。

#### 2.28 JavaScript 有几种方法判断变量的类型?

**参考答案**：

1.  使用 typeof 检测当需要判断变量是否是 number, string, boolean, function, undefined 等类型时，可以使用 typeof 进行判断。

2.  使用 instanceof 检测 instanceof 运算符与 typeof 运算符相似，用于识别正在处理的对象的类型。与 typeof 方法不同的是，instanceof 方法要求开发者明确地确认对象为某特定类型。

3.  使用 constructor 检测 constructor 本来是原型对象上的属性，指向构造函数。但是根据实例对象寻找属性的顺序，若实例对象上没有实例属性或方法时，就去原型链上寻找，因此，实例对象也是能使用 constructor 属性的。

#### 2.29 代码解释题

**参考答案**：

题目：

```cpp
var min = Math.min();
max = Math.max();
console.log(min < max);
// 写出执行结果，并解释原因
```

**答案**
false

**解析**

*   按常规的思路，这段代码应该输出 true，毕竟最小值小于最大值。但是却输出 false
*   MDN 相关文档是这样解释的
    *   Math.min 的参数是 0 个或者多个，如果多个参数很容易理解，返回参数中最小的。如果没有参数，则返回 Infinity，无穷大。
    *   而 Math.max 没有传递参数时返回的是-Infinity.所以输出 false

#### 2.30 代码解析题

**参考答案**：

**题目**

```cpp
var company = {
    address: 'beijing'
}
var yideng = Object.create(company);
delete yideng.address
console.log(yideng.address);
// 写出执行结果，并解释原因
```

**答案**
beijing

**解析**
这里的 yideng 通过 prototype 继承了 company 的 address。yideng 自己并没有 address 属性。所以 delete 操作符的作用是无效的。

**扩展**
1.delete 使用原则：delete 操作符用来删除一个对象的属性。
2.delete 在删除一个不可配置的属性时在严格模式和非严格模式下的区别:
（1）在严格模式中，如果属性是一个不可配置（non-configurable）属性，删除时会抛出异常;
（2）非严格模式下返回 false。
3.delete 能删除隐式声明的全局变量：这个全局变量其实是 global 对象(window)的属性
4.delete 能删除的：
（1）可配置对象的属性（2）隐式声明的全局变量 （3）用户定义的属性 （4）在 ECMAScript 6 中，通过 const 或 let 声明指定的 "temporal dead zone" (TDZ) 对 delete 操作符也会起作用
delete 不能删除的：
（2）显式声明的全局变量 （2）内置对象的内置属性 （3）一个对象从原型继承而来的属性
5.delete 删除数组元素：
（1）当你删除一个数组元素时，数组的 length 属性并不会变小，数组元素变成 undefined
（2）当用 delete 操作符删除一个数组元素时，被删除的元素已经完全不属于该数组。
（3）如果你想让一个数组元素的值变为 undefined 而不是删除它，可以使用 undefined 给其赋值而不是使用 delete 操作符。此时数组元素是在数组中的
6.delete 操作符与直接释放内存（只能通过解除引用来间接释放）没有关系。