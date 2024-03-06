# 第五章 第 9 节 前端基础-this1

> 原文：[`www.nowcoder.com/tutorial/10072/9876194af18b47518546fc09832e4711`](https://www.nowcoder.com/tutorial/10072/9876194af18b47518546fc09832e4711)

#### 4.1 call appy bind 的作用和区别

**参考答案：**

作用：

​ 都可以改变函数内部的 this 指向。

区别点：

1.  call 和 apply 会调用函数，并且改变函数内部 this 指向。
2.  call 和 apply 传递的参数不一样，call 传递参数 arg1,arg2...形式 apply 必须数组形式[arg]
3.  bind 不会调用函数，可以改变函数内部 this 指向。

**解析：**

call 方法

改变函数内部 this 指向

call()方法调用一个对象。简单理解为调用函数的方式，但是它可以改变函数的 this 指向。

写法：fun.call(thisArg, arg1, arg3, ...) // thisArg 为想要指向的对象，arg1,arg3 为参数

call 的主要作用也可以实现继承

```cpp
function Person(uname, age) {
    this.uname = uname;
    this.age = age;
  }
  function Son(uname, age) {
    Person.call(this, uname, age);
  }
  var son = new Son("zhang", 12);
  console.log(son);
```

apply 方法

apply()方法调用一个函数。简单理解为调用函数的方式，但是它可以改变函数的 this 指向。

写法：fun.apply(thisArg, [argsArray])

*   thisArg:在 fun 函数运行时指定的 this 值
*   argsArray:传递的值，必须包含在数组里面
*   返回值就是函数的返回值，因为他就是调用函数

apply 的主要应用，比如可以利用 apply 可以求得数组中最大值

```cpp
const arr = [1, 22, 3, 44, 5, 66, 7, 88, 9];
const max = Math.max.apply(Math, arr);
console.log(max);
```

bind 方法

bind()方法不会调用函数，但是能改变函数内部 this 指向

写法：fun.bind(thisArg, arg1, arg2, ...)

*   thisArg:在 fun 函数运行时指定的 this 值
*   arg1,arg2:传递的其他参数
*   返回由指定的 this 值和初始化参数改造的原函数拷贝

```cpp
var o = {
    name: "lisa"
};
function fn() {
    console.log(this);
}
var f = fn.bind(o);
f();
```

bind 应用

如果有的函数我们不需要立即调用，但是又需要改变这个函数的 this 指向，此时用 bind 再合适不过了

```cpp
const btns = document.querySelectorAll("button");
for (let i = 0; i < btns.length; i++) {
    btns[i].onclick = function() {
      this.disabled = true;
      setTimeout(
        function() {
          this.disabled = false;
        }.bind(this),
        2000
      );
    };
}
```

**扩展:**

主要应用场景：

1.  call 经常做继承。
2.  apply 经常跟数组有关系，比如借助于数学对象实现数组最大值最小值。
3.  bind 不调用函数，但是还想改变 this 指向，比如改变定时器内部的 this 指向。

#### 4.2 this 指向（普通函数、箭头函数）

**参考答案：**

普通函数中的 this

1.  谁调用了函数或者方法，那么这个函数或者对象中的 this 就指向谁

```cpp
        let getThis = function () {
            console.log(this);
        }

        let obj={
            name:"Jack",
            getThis:function(){
                console.log(this);
            }
        }
        //getThis()方法是由 window 在全局作用域中调用的，所以 this 指向调用该方法的对象，即 window
        getThis();//window
        //此处的 getThis()方法是 obj 这个对象调用的，所以 this 指向 obj
        obj.getThis();//obj
```

2.  匿名函数中的 this：匿名函数的执行具有全局性，则匿名函数中的 this 指向是 window，而不是调用该匿名函数的对象；

```cpp
        let obj = {
            getThis: function () {
                return function () {
                    console.log(this);
                }
            }
        }
        obj.getThis()(); //window
```

上面代码中，getThi()方法是由 obj 调用，但是 obj.getThis()返回的是一个匿名函数，而匿名函数中的 this 指向 window，所以打印出 window。 如果想在上述代码中使 this 指向调用该方法的对象，可以提前把 this 传值给另外一个变量(_this 或者 that)：

```cpp
       let obj = {
            getThis: function () {
            //提前保存 this 指向
                let _this=this
                return function () {
                    console.log(_this);
                }
            }
        }
        obj.getThis()(); //obj
```

3.  箭头函数中的 this
    1.  箭头函数中的 this 是在函数定义的时候就确定下来的，而不是在函数调用的时候确定的；
    2.  箭头函数中的 this 指向父级作用域的执行上下文；（技巧：**因为 javascript 中除了全局作用域，其他作用域都是由函数创建出来的，所以如果想确定 this 的指向，则找到离箭头函数最近的 function，与该 function 平级的执行上下文中的 this 即是箭头函数中的 this**）
    3.  箭头函数无法使用 apply、call 和 bind 方法改变 this 指向，因为其 this 值在函数定义的时候就被确定下来。

例 1：首先，距离箭头函数最近的是 getThis(){}，与该函数平级的执行上下文是 obj 中的执行上下文，箭头函数中的 this 就是下注释代码处的 this，即 obj。

```cpp
        let obj = {
            //此处的 this 即是箭头函数中的 this
            getThis: function () {
                return  ()=> {
                    console.log(this);
                }
            }
        }
        obj.getThis()(); //obj
```

例 2：该段代码中存在两个箭头函数，this 找不到对应的 function(){}，所以一直往上找直到指向 window。

```cpp
        //代码中有两个箭头函数，由于找不到对应的 function，所以 this 会指向 window 对象。
       let obj = {
            getThis: ()=> {
                return  ()=> {
                    console.log(this);
                }
            }
        }
        obj.getThis()(); //window
```

#### 4.3 手写 bind

**参考答案：**

1.  Function.prototype.bind,这样就可以让所有函数的隐式原型上都会有一个 bind 了。

```cpp
Function.prototype.bind = function() {
    // TODO
}
```

2.  bind 的第一个形参是要绑定给函数的上下文，所以再完善一下上面的代码

```cpp
Function.prototype.bind = function(context) {
    var fn = this;
    return function() {
        return fn.apply(context);
    }
}
```

3.  真正的 bind 函数是可以传递多个参数的，第一个参数是要绑定给调用它的函数的上下文，其他的参数将会作为预设参数传递给这个函数，如下所示

```cpp
let foo = function(){
    console.log(arguments);
}
foo.bind(null,"a","b")("c","d","e"); // {"1":"a","2":"b","3":"c","4":"d","5":"e"}
```

4.  为了实现上面的效果，我们发现只要在返回的值上将函数合并上去就行了

```cpp
Function.prototype.bind = function(context, ...args) {
    var fn = this;
    return function(...rest) {
        return fn.apply(context,[...args, ...rest]);
    }
}
```

5.  为了兼容性，替换成 ES5 的写法

```cpp
Function.prototype.bind = function() {
    var args = Array.prototype.slice.call(arguments);
    var context = args.splice(0,1)[0];
    var fn = this;
    return function() {
        let rest = Array.prototype.slice.call(arguments);
        return fn.apply(context, args.concat(rest));
    }
}
```

6.  把函数的原型保留下来。

```cpp
Function.prototype.bind = function() {
    var args = Array.prototype.slice.call(arguments);
    var context = args.splice(0,1)[0];
    var fn = this;
    var res =  function() {
        let rest = Array.prototype.slice.call(arguments);
        return fn.apply(context, args.concat(rest));
    }
    if(this.prototype) {
        res.prototype = this.prototype;
    }
    return res;
}
```

7.  最后还需要再找到一种方法来判断是否对 bind 之后的结果使用了 new 操作符。

```cpp
Function.prototype.bind = function() {
    var args = Array.prototype.slice.call(arguments);
    var context = args.splice(0,1)[0];
    var fn = this;
    var noop = function() {}
    var res =  function() {
        let rest = Array.prototype.slice.call(arguments);
        // this 只和运行的时候有关系，所以这里的 this 和上面的 fn 不是一码事，new res()和 res()在调用的时         候，res 中的 this 是不同的东西
        return fn.apply(this instanceof noop ? this : context, args.concat(rest));
    }
    if(this.prototype) {
        noop.prototype = this.prototype;
    }
    res.prototype = new noop();
    return res;
}
```