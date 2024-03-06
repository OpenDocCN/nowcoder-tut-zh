# 第五章 第 18 节 前端基础-应用 4

> 原文：[`www.nowcoder.com/tutorial/10072/31171e9d3822431fa1b18809a6b6a1d2`](https://www.nowcoder.com/tutorial/10072/31171e9d3822431fa1b18809a6b6a1d2)

#### 8.13 实现一个 JS 的 sleep

**参考答案**：

普通版

```cpp
function sleep(sleepTime) {
  for(var start = new Date; new Date - start <= sleepTime;) {}
}
var t1 = +new Date()
sleep(3000)
var t2 = +new Date()
console.log(t2 - t1)
```

优点：简单粗暴，通俗易懂。

缺点：这是最简单粗暴的实现，确实 sleep 了，也确实卡死了，CPU 会飙升，无论你的服务器 CPU 有多么 Niubility。

Promise 版本

```cpp
function sleep(time) {
  return new Promise(resolve => setTimeout(resolve, time))
}

const t1 = +new Date()
sleep(3000).then(() => {
  const t2 = +new Date()
  console.log(t2 - t1)
})
```

优点：这种方式实际上是用了 setTimeout，没有形成进程阻塞，不会造成性能和负载问题。

缺点：虽然不像 callback 套那么多层，但仍不怎么美观，而且当我们需要在某过程中需要停止执行（或者在中途返回了错误的值），还必须得层层判断后跳出，非常麻烦，而且这种异步并不是那么彻底，还是看起来别扭

Async/Await 版本

```cpp
function sleep(delay) {
  return new Promise(reslove => {
    setTimeout(reslove, delay)
  })
}

!async function test() {
  const t1 = +new Date()
  await sleep(3000)
  const t2 = +new Date()
  console.log(t2 - t1)
}()
```

缺点： ES7 语法存在兼容性问题，有 babel 一切兼容性都不是问题

更优雅的写法

```cpp
function sleep (time) {
  return new Promise((resolve) => setTimeout(resolve, time));
}

// 用法
sleep(500).then(() => {
    // 这里写 sleep 之后需要去做的事情
})
```

不要忘了开源的力量

```cpp
const sleep = require("sleep")
const t1 = +new Date()
sleep.msleep(3000)
const t2 = +new Date()
console.log(t2 - t1)
```

优点：能够实现更加精细的时间精确度，而且看起来就是真的 sleep 函数，清晰直白。

#### 8.14 实现一个数组对象的去重，相同 value 的只保留最后一个，最好有多个思路

**参考答案**：

**1.遍历数组法**

它是最简单的数组去重方法（indexOf 方法）

实现思路：新建一个数组，遍历去要重的数组，当值不在新数组的时候（indexOf 为-1）就加入该新数组中；

```cpp
var arr=[2,8,5,0,5,2,6,7,2];
function unique1(arr){
  var hash=[];
  for (var i = 0; i < arr.length; i++) {
     if(hash.indexOf(arr[i])==-1){
      hash.push(arr[i]);
     }
  }
  return hash;
}
```

**2.数组下标判断法**

调用 indexOf 方法，性能和方法 1 差不多

实现思路：如果当前数组的第 i 项在当前数组中第一次出现的位置不是 i，那么表示第 i 项是重复的，忽略掉。否则存入结果数组。

```cpp
function unique2(arr){
  var hash=[];
  for (var i = 0; i < arr.length; i++) {
     if(arr.indexOf(arr[i])==i){
      hash.push(arr[i]);
     }
  }
  return hash;
}
```

**3.排序后相邻去除法**

实现思路：给传入的数组排序，排序后相同的值会相邻，然后遍历排序后数组时，新数组只加入不与前一值重复的值。

```cpp
function unique3(arr){
  arr.sort();
  var hash=[arr[0]];
  for (var i = 1; i < arr.length; i++) {
     if(arr[i]!=hash[hash.length-1]){
      hash.push(arr[i]);
     }
  }
  return hash;
}
```

**4.优化遍历数组法（推荐）**

实现思路：双层循环，外循环表示从 0 到 arr.length，内循环表示从 i+1 到 arr.length

将没重复的右边值放入新数组。（检测到有重复值时终止当前循环同时进入外层循环的下一轮判断）

```cpp
function unique4(arr){
  var hash=[];
  for (var i = 0; i < arr.length; i++) {
    for (var j = i+1; j < arr.length; j++) {
      if(arr[i]===arr[j]){
        ++i;
      }
    }
      hash.push(arr[i]);
  }
  return hash;
}
```

**5.ES6 实现**

基本思路：ES6 提供了新的数据结构 Set。它类似于数组，但是成员的值都是唯一的，没有重复的值。

Set 函数可以接受一个数组（或类似数组的对象）作为参数，用来初始化。

```cpp
function unique5(arr){
  var x = new Set(arr);
 return [...x];
}
```

扩展：如果重复，则去掉该元素

数组下标去重

```cpp
function unique22(arr){
  var hash=[];
  for (var i = 0; i < arr.length; i++) {
     if(arr.indexOf(arr[i])==arr.lastIndexOf(arr[i])){
      hash.push(arr[i]);
     }
  }
  return hash;
}
```

#### 8.15 function rand(min, max, N)：生成长度是 N，且在 min、max 内不重复的整数随机数组

**参考答案：**

把考点拆成了 4 个小项；需要用递归算法实现：
a) 生成一个长度为 n 的空数组 arr。
b) 生成一个（min－max）之间的随机整数 rand。
c) 把随机数 rand 插入到数组 arr 内，如果数组 arr 内已存在与 rand 相同的数字，则重新生成随机数 rand 并插入到 arr 内[需要使用递归实现，不能使用 for/while 等循环]
d) 最终输出一个长度为 n，且内容不重复的数组 arr。

```cpp
function buildArray(arr, n, min, max) {
    var num = Math.floor(Math.random() * (max - min + 1)) + min;
    if (!arr.includes(num)) { arr.push(num); }
    return arr.length === n ? arr : buildArray(arr, n, min, max);
}
var result = buildArray([], 5, 2, 32);
console.table(result);
```

#### 8.16 闭包的理解

**参考答案：**

闭包：

​ 一个函数和对其周围状态（**lexical environment，词法环境**）的引用捆绑在一起（或者说函数被引用包围）， 这样的组合就是**闭包**（**closure**）。也就是说，闭包让你可以在一个内层函数中访问到其外层函数的作用域。在 JavaScript 中，每当创建一个函数，闭包就会在函数创建的同时被创建出来。

闭包的特点：

​ 让外部访问函数内部变量成为可能；
​ 可以避免使用全局变量，防止全局变量污染；
​ 可以让局部变量常驻在内存中；
​ 会造成内存泄漏（有一块内存空间被长期占用，而不被释放）

应用场景

1.  埋点（是网站分析的一种常用的数据采集方法）计数器

```cpp
function count() {
  var num = 0;
  return function () {
    return ++num
  }
}
var getNum = count();
var getNewNum = count();
document.querySelectorAll('button')[0].onclick = function(){
  console.log('点击加入购物车次数： '+getNum());
}
document.querySelectorAll('button')[1].onclick = function(){
  console.log('点击付款次数： '+getNewNum());
}    
```

2.  事件+循环

按照以下方式添加事件，打印出来的 i 不是按照序号的

形成原因就是操作的是同一个词法环境,因为 onclick 后面的函数都是一个闭包，但是操作的是同一个词法环境

```cpp
   var lis = document.querySelectorAll('li');
   for (var i = 0; i < lis.length; i++) {
            lis[i].onclick = function () {
                alert(i)
            }       
    }
```

解决办法：

使用匿名函数之后，就形成一个闭包， 操作的就是不同的词法环境

```cpp
var lis = document.querySelectorAll('li');  
for (var i = 0; i < lis.length; i++) {
     (function (j) {
                lis[j].onclick = function () {
                    alert(j)
                }
            })(i)
 }
```

#### 8.17 字符串中的单词逆序输出（手写）

**参考答案：**

方法一：

```cpp
function strReverse(str) {
   return str.split("").reverse().join("") 
}
```

方法二：

```cpp
function strReverse(str) {
    var i=str.length;
    var nstr = ""; 
    i=i-1; 
    for (var x = i; x >=0; x--) { 
        nstr+=str.charAt(x)
    }
    return nstr
}
```

方法三：

```cpp
function strReverse(str) {
      if(str.length == 0)return null; 
      var i = str.length; 
      var dstr = ""; 
      while(--i >= 0) 
      { 
          dstr += str.charAt(i);  
      } 
      return dstr; 
}
```

方法四：

```cpp
function strReverse(str) {
  return str.split('').reduce((prev, next) => next + prev);
}
```

方法五：

```cpp
function strReverse(str) {
    var newstr="";
           for(var i=0;i<str.length;i++){
               newstr=str.charAt(i)+newstr;
           }
           return newstr
}
```

方法六：

```cpp
function strReverse(str) {
            if(str.length===1){
                return str
            }
            return str.slice(-1)+strReverse(str.slice(0,-1));
}
```