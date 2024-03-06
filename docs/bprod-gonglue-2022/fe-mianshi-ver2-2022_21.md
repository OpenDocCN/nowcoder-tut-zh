# 第五章 第 2 节 新特性（中 ）

> 原文：[`www.nowcoder.com/tutorial/10091/5c9b7369b76549a39c8a7ecb3ea32ef6`](https://www.nowcoder.com/tutorial/10091/5c9b7369b76549a39c8a7ecb3ea32ef6)

### 2 新特性（中）

#### 2.1 请说说 ES6 新增了哪些对象方法？

【考点映射】

*   ES6 新增 Object 对象方法

【频率】★★★★

【难度】☆

【参考答案】

**（1）Object.is()**

ES5 中用 == 和 === 比较两个值是否相等，两者都有缺陷，== 会自动转换数据类型，=== NaN 不等于自身，以及 +0 等于 -0。ES6 提出“Same-value equality”（同值相等）算法，即在所有环境中，只要两个值是一样的，则相等，Object.is()就是实现这个算法的新方法，用来比较两个值是否严格相等（除了两个特例，基本与 === 一致）

```cpp
Object.is('foo','foo') // true 
Object.is({},{}) // false
//与===的不同之处
Object.is(+0,-0) // false 
Object.is(NaN,NaN) // true

```

**（2）Object.assign()**

用于对象的合并，将源对象（source）的所有可枚举属性，复制到目标对象（target）

常见用途：为对象添加属性、方法，克隆对象，合并多个对象，为属性指定默认值

```cpp
const target ={a:1};  
const source1 ={b:2}; 
const source2 ={c:3};  
Object.assign(target, source1, source2); 
console.log(target); // {a:1, b:2, c:3}

```

注意：

*   Object.assign()方法是浅拷贝，而不是深拷贝，若源对象某个属性的值是对象，那么目标对象拷贝得到的是这个对象的引用

*   Object.assign()拷贝的属性是有限制的，只拷贝源对象的自身属性，不拷贝继承属性，不拷贝不可枚举属性

*   一旦遇到同名属性时，会直接替换，后面的属性会覆盖前面的属性

*   若参数不是对象，则会先转成对象；由于 undefined 和 null 无法转成对象，若它们作为参数，就会报错    

**（3）__proto__ 属性：**本质上是一个内部属性，而不是一个正式的对外的 API，若一个对象本身部署了 __proto__ 属性，该属性的值就是对象的原型，实际上是调用了 Object.prototype.__proto__

**（4）Object.setPrototypeOf()：**功能与 __proto__ 相同，用于设置一个对象的原型对象，相较于 __proto__ 是 ES6 推荐的方法

**（5）Object.getPrototypeOf() ：**用于读取一个对象的原型对象

**（6）Object.keys()：**遍历对象自身（不含继承的）所有可遍历属性的键名，以数组的方式返回

**（7）Object.values()：**遍历对象自身（不含继承的）所有可遍历属性的值，以数组的方式返回

**（8）Object.entries() ：**遍历对象自身（不含继承的）所有可遍历属性的健值对，以数组的方式返回（只输出属性名非 Symbol 值的属性）

```cpp
var obj ={ foo:'bar', baz:42}
Object.keys(obj) // ["foo", "baz"]
Object.values(obj) // ["bar", 42]
Object.entries(obj) // [ ["foo", "bar"], ["baz", 42] ]

```

**（9）Object.fromEntries()：**将一个键值对数组转为对象，Object.entries()逆操作

```cpp
Object.fromEntries([['foo','bar'],['baz',42]]) // { foo: "bar", baz: 42 }
```

**（10）Object.getOwnPropertyDescriptors()：**用来获取一个对象的所有自身属性的描述符，返回一个对象，所有原来的对象的属性名都是该对象的属性名，对应的属性值就是该属性的描述对象

```cpp
const obj = {
  foo: 123,
  get bar() { return 'abc' }
};
Object.getOwnPropertyDescriptors(obj)
// { foo:
//    { value: 123,
//      writable: true,
//      enumerable: true,
//      configurable: true },
//   bar:
//    { get: [Function: bar],
//      set: undefined,
//      enumerable: true,
//      configurable: true } }

```

#### 2.2 请说说 ES6 新增了哪些数组方法？

【考点映射】

*   ES6 新增数组方法

【频率】★★★★★

【难度】☆

【参考答案】

**（1）扩展运算符**

扩展运算符：...，将一个数组转为用逗号分隔的参数序列，该运算符主要用于函数调用

```cpp
console.log(...[1,2,3]) // 1 2 3  
console.log(1,...[2,3,4],5) // 1 2 3 4 5 

```

**（2）Array.from（）**

用于将两类对象转为真正的数组：类似数组的对象（array-like object）和可遍历（iterable）的对象（包括 ES6 新增的数据结构 Set 和 Map）

```cpp
let arrayLike ={
    '0':'a',
    '1':'b',
    '2':'c',    
    length:3
}; 
// ES5 的写法 
var arr1 =[].slice.call(arrayLike); // ['a', 'b', 'c']  
// ES6 的写法 
let arr2 = Array.from(arrayLike); // ['a', 'b', 'c']

```

**（3）数组实例的 find（）和 findIndex（）**

Array.prototype.find（）：用于找出第一个符合条件的数组成员，它的参数是一个回调函数，所有数组成员依次执行该回调函数，直到找出第一个返回值为 true 的成员，返回该成员。如没有符合条件的成员，则返回 undefined

```cpp
[1,4,-5,10].find((n)=> n <0) // -5
```

Array.prototype.findIndex（）：与 find 方法非常类似，返回第一个符合条件的数组成员的位置，如所有成员都不符合条件，则返回-1

```cpp
[1,5,10,15].findIndex(function(value, index, arr){
    return value >9;
}) // 2

```

**（4）数组实例的 includes（）**

Array.prototype.includes（）：返回一个布尔值，表示某个数组是否包含给定的值，第二个参数表示搜索的起始位置，默认为 0，与字符串的 includes 方法类似

```cpp
[1,2,3].includes(2) // true 
[1,2,3].includes(3,3); // false

```

#### 2.3 请问你了解 Set、Map 数据结构吗？

【考点映射】

*   Set、Map 数据结构

【频率】★★★★★

【难度】☆

【参考答案】

*   **Set**

类似于数组，但**成员的值是唯一的**，没有重复值

最常见应用：去除数组重复值、去除字符串里面的重复字符

```cpp
// 去除数组的重复成员 
[...newSet(array)]
// 去除字符串里面的重复字符
[...newSet('ababbc')].join('') // "abc"

```

*   **WeakSet**

类似于 Set，也是不重复的值的集合，与 Set 区别：

（1）WeakSet 的成员只能是对象，不能是其他类型的值

（2）WeakSet 中的对象都是弱引用，即垃圾回收机制不考虑 WeakSet 对该对象的引用，如果其他对象都不再引用该对象，那垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存在于 WeakSet 之中

*   **Map**

js 对象本质上是键值对的集合（Hash 结构），但是只能用字符串当作键名，一些非字符串类型的值作为键名前，都会被自动转换为字符串

Map 正是为解决此问题，它类似于对象，也是键值对的集合，但是键名的范围不限于字符串，各种类型的值（包括对象）都可以当作键名

相比 Object 结构，Object 是“字符串——值”对应，Map 是“值——值”的对应

*   **WeakMap**

结构于 Map 类似，也是用于生成键值对的集合，与 Map 区别：

（1）只接受对象作为键名（null 除外），不接受其他类型的值作为键名

（2）WeakMap 的键名所指向的对象，不计入垃圾回收机制

#### 2.4 请你了解 ES6 中的迭代器与生成器吗？

【考点映射】

*   迭代器与生成器

【频率】★★★★★

【难度】☆

【参考答案】

Iterator 迭代器是一种接口，为各种不同的数据结构提供统一的访问机制，任何数据结构只要部署了 Iterator 接口，就可以完成迭代操作（即遍历，依次处理该数据结构的所有成员）

**Iterator 的主要作用：**

（1）为各种数据结构，提供一个统一的、简便的访问接口

（2）使得数据结构的成员能够按某种次序排列

（3）当使用 for...of 循环遍历某种数据结构时，该循环会自动去寻找 Iterator 接口

**常见默认调用 Iterator 场景：**

for...of 循环、解构赋值、扩展运算（...）、yield*

遍历数组也会调用遍历器接口，任何接受数组作为参数的地方，几乎都调用了遍历器接口，如 Array.from()、Map()、Set()等

**Generator 生成器**是一种异步编程解决方案，Generator 是一个状态机，封装了多个内部状态；它是一个迭代器对象生成函数，

执行 Generator 函数会返回一个迭代器对象，也就是说，Generator 函数除了状态机，还是一个遍历器对象生成函数。返回的迭代器对象，可以依次遍历 Generator 函数内部的每一个状态。

Generator 对比普通函数，外观上有两个主要特征：function 关键字与函数名之间有一个星号，函数体内部使用 yield 表达式，定义不同的内部状态

```cpp
function* helloWorldGenerator(){   
    yield 'hello';    //yield 表达式
    yield 'world';    //yield 表达式
    return'ending';
}var hw = helloWorldGenerator();

```

代码块定义了一个 Generator 函数：helloWorldGenerator，它内部有两个 yield 表达式，有三个状态：hello，world 和 return 语句（结束执行）

不同于普通函数，调用 **Generator 函数后不执行**，返回的不是函数运行结果，而返回一个指向内部状态的指针对象（迭代器对象）

且必须调用遍历器对象的 next 方法，使得指针移向下一个状态。可理解为：每次调用 next 方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个 yield 表达式（或 return 语句）为止。Generator 函数是分段执行的，yield 表达式是暂停执行的标记，而 next 方法可以恢复执行。

#### 2.5 请问箭头函数与普通函数有哪些区别？

【考点映射】

*   箭头函数

【频率】★★★★★

【难度】☆

【参考答案】

在外观上， 箭头函数省去了 function 关键字，使用“箭头”=>定义函数，使表达更加简洁，

```cpp
// 箭头函数写法 
var f = v => v; 
// 正常函数写法 
var f = function(v){
    return v;
};
// 箭头函数写法 
var f =() => 5; 
// 正常函数写法 
var f = function(){
    return 5
};
// 箭头函数写法 
var sum =(num1, num2)=> num1 + num2; 
// 正常函数写法 
var sum = function( num1, num2 ){
    return num1 + num2;
};

```

箭头函数很大程度简化了回调函数

```cpp
// 正常函数写法 
[1,2,3].map(function(x){
    return x * x;
}); 
// 箭头函数写法 
[1,2,3].map(x => x * x);

```

注意：

（1）箭头函数不会创建自己的 this，只会从自己的作用域链的上一层继承 this

（2）箭头函数的 this 是定义时所在的对象，不是使用时所在的对象

（3）箭头函数的 this 指向是固定的

（4）不能做构造函数，不可使用 new 命令，会报错

（5）箭头函数是没有原型，没有 prototype 属性，故不可使用 new

（6）没有自己的 arguments 对象，箭头函数中访问 arguments 实际上是外层局部（函数）执行环境中的值

（7）不可使用 yield 命令，不能用作 Generator 函数

一起看两道箭头函数的 this 指向的高频考题：（极有可能出代码题（面试官给代码，答输出））

箭头函数的 this 指向取决于外层作用域中的 this，外层作用域或函数的 this 指向谁，箭头函数中的 this 便指向谁；且一旦箭头函数的 this 绑定成功，也无法被再次修改，类似于硬绑定

```cpp
var a = 'window'
var obj1 = {
    a: 1,
    fn1: function () {
            return () => console.log(this.a)
    }
}
var obj2 = {
    a: 2
};

obj1.fn1()(); // 1
obj1.fn1().call(obj2); // 1 
obj1.fn1.call(obj2)(); // 2

```

obj1.fn1()()等价于：

```cpp
var fn = obj1.fn1(); //fn1 的 this 指向 obj1,箭头函数 this 参考 fn1 的 this
window.fn();//无法改变箭头函数 this

```

obj1.fn1().call(obj2)等价于：

```cpp
var fn = obj1.fn1(); 
fn.call(obj2);//显式绑定无法改变 this

```

obj1.fn1.call(obj2)()等价于：

```cpp
var fn = obj1.fn1.call(obj2);//fn1 的 this 此时指向 obj2,箭头函数的 this 跟着变化
window.fn();//隐式绑定无法改变箭头函数 this

```

**注意：虽无法直接改变箭头函数 this 指向，但可以修改外层作用域的 this 间接修改箭头函数 this**

```cpp
var name = 'window'  
function Person(a) {   
    this.a = a;   
    this.fn1 = () => console.log(this.a);   
    this.fn2 = function () {     
        return () => console.log(this.a);   
    }; 
};
var obj1 = new Person(1); 

var obj2 = new Person(2);  

obj1.fn1(); // 1
obj1.fn1.call(obj2); // 1

obj1.fn2()(); // 1
obj1.fn2().call(obj2); // 1
obj1.fn2.call(obj2)(); // 2

```

obj1.fn1()：构造函数在 new 的过程中 this 指向了 obj1，于是箭头函数的 this 也指向 obj1

obj1.fn1.call(obj2)等价于：

```cpp
var fn = obj1.fn1;//箭头函数 this 指向 obj1 
fn.call(obj2);//箭头函数 this 无法改变

```

obj1.fn2()()：fn2 指向 obj1，箭头函数 this 同样指向 obj1

obj1.fn2().call(obj2)：箭头函数无法被直接修改

obj1.fn2.call(obj2)()等价于：

```cpp
var fn = obj1.fn2.call(obj2);//fn2 的 this 此时指向 obj2,箭头函数的 this 跟着变化
window.fn();//隐式绑定无法改变箭头函数 this

```

#### 2.6 请说说你对 Promise 的理解？

【考点映射】

*   ES6 新特性 Promise

【频率】★★★★★

【难度】☆☆

【参考答案】

**Promise 三个优点：**

（1）可以很好地解决异步编程，将异步操作以同步操作的流程表达出来

（2）让回调函数变成更加规范的链式写法，避免了层层嵌套的回调“地域”

（3）可以处理并行任务，提供统一的对外接口

**Promise 三种状态：**

（1）pending：进行中

（2）fulfilled(resolved)：已成功

（3）rejected：已失败

**Promise 三个特点：**

（1）对象的状态不受外界影响，虽执行器函数的执行是同步的，但在 Promise 的状态未从 Pending 状态变更到其他状态前，then 和 catch 里面的回调函数会放在队列里面等待状态变更以后才执行（异步）

（2）一旦状态改变，就不会再变，任何时候都可以得到这个结果

（3）只有两种状态改变可能：pending→fulfilled、pending→rejected

**Promise 三个缺点：**

（1）一旦新建 Promise 就会立即执行，无法中途取消

（2）若不设置回调函数，Promise 内部抛出的错误，不会反应到外部

（3）当处于 pending 状态时，无法得知操作现阶段处于哪一个阶段