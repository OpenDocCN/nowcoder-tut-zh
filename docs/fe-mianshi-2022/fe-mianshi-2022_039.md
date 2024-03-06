# 第五章 第 20 节 前端基础-模块化

> 原文：[`www.nowcoder.com/tutorial/10072/a3999b5049314e22871a73aab08c05ab`](https://www.nowcoder.com/tutorial/10072/a3999b5049314e22871a73aab08c05ab)

#### 9.1 CommonJS 规范

**参考答案：**

CommonJS 规范加载模块是同步的，只有加载完成，才能执行后面的操作。

`CommonJS`规范中的`module`、`exports`和`require`

*   每个文件就是一个模块，有自己的作用域。每个模块内部，`module`变量代表当前模块，是一个对象，它的`exports`属性（即`module.exports`）是对外的接口。
*   `module.exports`属性表示当前模块对外输出的接口，其他文件加载该模块，实际上就是读取`module.exports`变量。
*   为了方便，`Node`为每个模块提供一个`exports`变量，指向`module.exports`。

```cpp
let exports = module.exports;
```

*   `require`命令用于加载模块文件。

使用示例：

```cpp
  //name.js
  exports.name = function(){return '李婷婷'}; //导出
  //getName.js
  let getName = require('name'); //引入
```

注：不能直接将`exports`变量指向一个值，因为这样等于切断了`exports`与`module.exports`的联系：如下

```cpp
exports = function(x){console.log(x)}
```

如果一个模块的对外接口，就是一个单一的值，不能使用`exports`输出，只能使用`module.exports`输出。

CommonJS 模块导入用`require`，导出用`module.exports`。导出的对象需注意，如果是静态值，而且非常量，后期可能会有所改动的，请使用函数动态获取，否则无法获取修改值。导入的参数，是可以随意改动的，所以使用时要注意

#### 9.2 ES6 module 和 CommonJS module 的区别

**参考答案**：

*   为**CommonJS**的`require`语法是同步的，所以就导致了**CommonJS**模块规范只适合用在服务端，而 ES6 模块无论是在浏览器端还是服务端都是可以使用的，但是在服务端中，还需要遵循一些特殊的规则才能使用 ；

*   **CommonJS** 模块输出的是一个值的拷贝，而 ES6 模块输出的是值的引用；

*   **CommonJS** 模块是运行时加载，而 ES6 模块是编译时输出接口，使得对 JS 的模块进行静态分析成为了可能

*   因为两个模块加载机制的不同，所以在对待循环加载的时候，它们会有不同的表现。**CommonJS**遇到循环依赖的时候，只会输出已经执行的部分，后续的输出或者变化，是不会影响已经输出的变量。而 ES6 模块相反，使用`import`加载一个变量，变量不会被缓存，真正取值的时候就能取到最终的值；

*   关于模块顶层的`this`指向问题，在**CommonJS**顶层，`this`指向当前模块；而在 ES6 模块中，`this`指向`undefined`；

*   关于两个模块互相引用的问题，在 ES6 模块当中，是支持加载**CommonJS**模块的。但是反过来，**CommonJS**并不能`require`ES6 模块，在 NodeJS 中，两种模块方案是分开处理的。

#### 9.3 ES6 module、CommonJS module 循环引用的问题

**参考答案：**

​ 循环加载指的是 a 脚本的执行依赖 b 脚本，b 脚本的执行依赖 a 脚本

1.  CommonJS 模块是加载时执行。一旦出现某个模块被“循环加载”，就只输出已经执行的部分，没有执行的部分不会输出。

2.  ES6 模块对导出模块，变量，对象是动态引用，遇到模块加载命令 import 时不会去执行模块，只是生成一个指向被加载模块的引用。

    CommonJS 模块规范主要适用于后端 Node.js，后端 Node.js 是同步模块加载，所以在模块循环引入时模块已经执行完毕。推荐前端工程中使用 ES6 的模块规范，通过安装 Babel 转码插件支持 ES6 模块引入的语法。

**解析：**

1.  CommonJS 模块的加载原理

CommonJS 模块就是一个脚本文件，require 命令第一次加载该脚本时就会执行整个脚本，然后在内存中生成该模块的一个说明对象。

```cpp
{
    id: '',  //模块名，唯一
    exports: {  //模块输出的各个接口
        ...
    },
    loaded: true,  //模块的脚本是否执行完毕
    ...
}
```

以后用到这个模块时，就会到对象的 exports 属性中取值。即使再次执行 require 命令，也不会再次执行该模块，而是到缓存中取值。

CommonJS 模块是加载时执行，即脚本代码在 require 时就全部执行。一旦出现某个模块被“循环加载”，就只输出已经执行的部分，没有执行的部分不会输出。

案例说明：

案例来源于 Node 官方说明：[nodejs.org/api/modules…](https://nodejs.org/api/modules.html#modules_cycles)

```cpp
//a.js
exports.done = false;

var b = require('./b.js');
console.log('在 a.js 中，b.done = %j', b.done);

exports.done = true;
console.log('a.js 执行完毕！')

//b.js
exports.done = false;

var a = require('./a.js');
console.log('在 b.js 中，a.done = %j', a.done);

exports.done = true;
console.log('b.js 执行完毕！')

//main.js
var a = require('./a.js');
var b = require('./b.js');

console.log('在 main.js 中，a.done = %j, b.done = %j', a.done, b.done);

```

输出结果如下：

```cpp
//node 环境下运行 main.js
node main.js

在 b.js 中，a.done = false
b.js 执行完毕！
在 a.js 中，b.done = true
a.js 执行完毕！
在 main.js 中，a.done = true, b.done = true
```

JS 代码执行顺序如下：

1）main.js 中先加载 a.js，a 脚本先输出 done 变量，值为 false，然后加载 b 脚本，a 的代码停止执行，等待 b 脚本执行完成后，才会继续往下执行。

2）b.js 执行到第二行会去加载 a.js，这时发生循环加载，系统会去 a.js 模块对应对象的 exports 属性取值，因为 a.js 没执行完，从 exports 属性只能取回已经执行的部分，未执行的部分不返回，所以取回的值并不是最后的值。

3）a.js 已执行的代码只有一行，exports.done = false;所以对于 b.js 来说，require a.js 只输出了一个变量 done，值为 false。往下执行 console.log('在 b.js 中，a.done = %j', a.done);控制台打印出：

```cpp
在 b.js 中，a.done = false
```

4）b.js 继续往下执行，done 变量设置为 true，console.log('b.js 执行完毕！')，等到全部执行完毕，将执行权交还给 a.js。此时控制台输出：

```cpp
b.js 执行完毕！
```

5）执行权交给 a.js 后，a.js 接着往下执行，执行 console.log('在 a.js 中，b.done = %j', b.done);控制台打印出：

```cpp
在 a.js 中，b.done = true
```

6）a.js 继续执行，变量 done 设置为 true，直到 a.js 执行完毕。

```cpp
a.js 执行完毕！
```

7）main.js 中第二行不会再次执行 b.js，直接输出缓存结果。最后控制台输出：

```cpp
在 main.js 中，a.done = true, b.done = true
```

总结：

1）在 b.js 中，a.js 没有执行完毕，只执行了第一行，所以循环加载中，只输出已执行的部分。

2）main.js 第二行不会再次执行，而是输出缓存 b.js 的执行结果。exports.done = true;

2.  ES6 模块的循环加载

ES6 模块与 CommonJS 有本质区别，ES6 模块对导出变量，方法，对象是动态引用，遇到模块加载命令 import 时不会去执行模块，只是生成一个指向被加载模块的引用，需要开发者保证真正取值时能够取到值，只要引用是存在的，代码就能执行。

案例说明：

```cpp
//even.js
import {odd} from './odd';

var counter = 0;
export function even(n){
    counter ++;
    console.log(counter);

    return n == 0 || odd(n-1);
}
复制代码
//odd.js
import {even} from './even.js';

export function odd(n){
    return n != 0 && even(n-1);
}
复制代码
//index.js
import * as m from './even.js';

var x = m.even(5);
console.log(x);

var y = m.even(4);
console.log(y);
```

**执行 index.js，输出结果如下：**

```cpp
babel-node index.js

1
2
3
false
4
5
6
true
```

可以看出 counter 的值是累加的，ES6 是动态引用。如果上面的引用改为 CommonJS 代码，会报错，因为在 odd.js 里，even.js 代码并没有执行。改成 CommonJS 规范加载的代码为：

```cpp
//even.js
var odd = require('./odd.js');

var counter = 0;
module.exports = function even(n){
    counter ++;
    console.log(counter);

    return n == 0 || odd(n-1);
}
//odd.js
var even = require('./even.js');

module.exports = function odd(n){
    return n != 0 && even(n-1);
}
//index.js
var even = require('./even.js');

var x = even(5);
console.log(x);

var y = even(5);
console.log(y);
```

**执行 index.js，输出结果如下：**

```cpp
$ babel-node index.js
1
/Users/name/Projects/node/ES6/odd.1.js:6
    return n != 0 && even(n - 1);
                     ^

TypeError: even is not a function
    at odd (/Users/name/Projects/node/ES6/odd.1.js:4:22)
```