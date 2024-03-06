# 第五章 第 16 节 前端基础-应用 2

> 原文：[`www.nowcoder.com/tutorial/10072/3a3ee0c5beb145eca08a5773d9ff13fb`](https://www.nowcoder.com/tutorial/10072/3a3ee0c5beb145eca08a5773d9ff13fb)

#### 8.3 防抖和节流的原理和使用场景

**参考答案：**

函数防抖和函数节流：优化高频率执行 js 代码的一种手段，js 中的一些事件如浏览器的 resize、scroll，鼠标的 mousemove、mouseover，input 输入框的 keypress 等事件在触发时，会不断地调用绑定在事件上的回调函数，极大地浪费资源，降低前端性能。为了优化体验，需要对这类事件进行调用次数的限制。

**防抖：**

在事件被触发 n 秒后再执行回调，如果在这 n 秒内又被触发，则重新计时。

根据函数防抖思路设计出第一版的最简单的防抖代码：

```cpp
var timer; // 维护同一个 timer
function debounce(fn, delay) {
    clearTimeout(timer);
    timer = setTimeout(function(){
        fn();
    }, delay);
}
```

上面例子中的 debounce 就是防抖函数，在 document 中鼠标移动的时候，会在 onmousemove 最后触发的 1s 后执行回调函数 testDebounce；如果我们一直在浏览器中移动鼠标（比如 10s），会发现会在 10 + 1s 后才会执行 testDebounce 函数（因为 clearTimeout(timer)），这个就是函数防抖。

在上面的代码中，会出现一个问题，var timer 只能在 setTimeout 的父级作用域中，这样才是同一个 timer，并且为了方便防抖函数的调用和回调函数 fn 的传参问题，我们应该用闭包来解决这些问题。

优化后的代码：

```cpp
function debounce(fn, delay) {
    var timer; // 维护一个 timer
    return function () {
        var _this = this; // 取 debounce 执行作用域的 this
        var args = arguments;
        if (timer) {
            clearTimeout(timer);
        }
        timer = setTimeout(function () {
            fn.apply(_this, args); // 用 apply 指向调用 debounce 的对象，相当于 _this.fn(args);
        }, delay);
    };
}
```

使用闭包后，解决传参和封装防抖函数的问题，这样就可以在其他地方随便将需要防抖的函数传入 debounce 了。

**节流：**

每隔一段时间，只执行一次函数。

*   定时器实现节流函数：

    ```cpp
    function throttle(fn, delay) {
        var timer;
        return function () {
            var _this = this;
            var args = arguments;
            if (timer) {
                return;
            }
            timer = setTimeout(function () {
                fn.apply(_this, args);
                timer = null; // 在 delay 后执行完 fn 之后清空 timer，此时 timer 为假，throttle 触发可以进入计时器
            }, delay)
        }
    }
    ```

*   时间戳实现节流函数：

    ```cpp
    function throttle(fn, delay) {
        var previous = 0;
        // 使用闭包返回一个函数并且用到闭包函数外面的变量 previous
        return function() {
            var _this = this;
            var args = arguments;
            var now = new Date();
            if(now - previous > delay) {
                fn.apply(_this, args);
                previous = now;
            }
        }
    }
    ```

**异同比较**

相同点：

*   都可以通过使用 setTimeout 实现。
*   目的都是，降低回调执行频率。节省计算资源。

不同点：

*   函数防抖，在一段连续操作结束后，处理回调，**利用 clearTimeout 和 setTimeout 实现**。函数节流，在一段连续操作中，**每一段时间只执行一次**，频率较高的事件中使用来提高性能。
*   函数防抖关注一定时间连续触发的事件只在最后执行一次，而函数节流侧重于一段时间内只执行一次。

常见应用场景

**函数防抖的应用场景:**

连续的事件，只需触发一次回调的场景有：

*   搜索框搜索输入。只需用户最后一次输入完，再发送请求
*   手机号、邮箱验证输入检测
*   窗口大小 Resize。只需窗口调整完成后，计算窗口大小。防止重复渲染。

函数节流的应用场景:

间隔一段时间执行一次回调的场景有：

*   滚动加载，加载更多或滚到底部监听
*   谷歌搜索框，搜索联想功能
*   高频点击提交，表单重复提交

#### 8.4 浅拷贝，深拷贝(实现方式)

**参考答案：**

浅拷贝和深拷贝都只针对于引用数据类型，浅拷贝只复制指向某个对象的指针，而不复制对象本身，新旧对象还是共享同一块内存；但深拷贝会另外创造一个一模一样的对象，新对象跟原对象不共享内存，修改新对象不会改到原对象；

区别：浅拷贝只复制对象的第一层属性、深拷贝可以对对象的属性进行递归复制；

实现浅拷贝方法

（1）Object.assign 方法

```cpp
var obj = {
    a: 1,
    b: 2
}
var obj1 = Object.assign({},obj);
boj1.a = 3;
console.log(obj.a) // 3
```

（2）for in 方法

```cpp
// 只复制第一层的浅拷贝
function simpleCopy(obj1) {
   var obj2 = Array.isArray(obj1) ? [] : {};
   for (let i in obj1) {
   obj2[i] = obj1[i];
  }
   return obj2;
}
var obj1 = {
   a: 1,
   b: 2,
   c: {
         d: 3
      }
}
var obj2 = simpleCopy(obj1);
obj2.a = 3;
obj2.c.d = 4;
alert(obj1.a); // 1
alert(obj2.a); // 3
alert(obj1.c.d); // 4
alert(obj2.c.d); // 4
```

实现深拷贝方法

（1）采用递归去拷贝所有层级属性

```cpp
function deepClone(obj){
    let objClone = Array.isArray(obj)?[]:{};
    if(obj && typeof obj==="object"){
        for(key in obj){
            if(obj.hasOwnProperty(key)){
                //判断 ojb 子元素是否为对象，如果是，递归复制
                if(obj[key]&&typeof obj[key] ==="object"){
                    objClone[key] = deepClone(obj[key]);
                }else{
                    //如果不是，简单复制
                    objClone[key] = obj[key];
                }
            }
        }
    }
    return objClone;
}    
let a=[1,2,3,4],
    b=deepClone(a);
a[0]=2;
console.log(a,b);
```

（2）使用 JSON.stringify 和 JSON.parse 实现深拷贝：JSON.stringify 把对象转成字符串，再用 JSON.parse 把字符串转成新的对象；

```cpp
function deepCopy(obj1){
    let _obj = JSON.stringify(obj1);
    let obj2 = JSON.parse(_obj);
    return obj2;
  }
    var a = [1, [1, 2], 3, 4];
    var b = deepCopy(a);
    b[1][0] = 2;
    alert(a); // 1,1,2,3,4
    alert(b); // 2,2,2,3,4
```

（3）热门的函数库 lodash，也有提供 _.cloneDeep 用来做深拷贝；

```cpp
var _ = require('lodash');
var obj1 = {
    a: 1,
    b: { f: { g: 1 } },
    c: [1, 2, 3]
};
var obj2 = _.cloneDeep(obj1);
console.log(obj1.b.f === obj2.b.f);
// false
```

#### 8.5 获取当前页面 url

**参考答案：**

1.  window.location.href (设置或获取整个 URL 为字符串)

```cpp
var test = window.location.href;
alert(test);
//  返回：http://i.cnblogs.com/EditPosts.aspx?opt=1
```

2.  window.location.protocol (设置或获取 URL 的协议部分)

```cpp
var test = window.location.protocol;
alert(test);
//返回：http:
```

3.  window.location.host (设置或获取 URL 的主机部分)

```cpp
var test = window.location.host;
alert(test);
//返回：i.cnblogs.com
```

4.  window.location.port (设置或获取与 URL 关联的端口号码)

```cpp
var test = window.location.port;
alert(test);
//返回：空字符(如果采用默认的 80 端口 (update:即使添加了:80)，那么返回值并不是默认的 80 而是空字符)
```

5.  window.location.pathname (设置或获取与 URL 的路径部分（就是文件地址）)

```cpp
var test = window.location.pathname;
alert(test);
//返回：/EditPosts.aspx
```

6.  window.location.search (设置或获取 href 属性中跟在问号后面的部分)

```cpp
var test = window.location.search;
alert(test);
//返回：?opt=1
（PS：获得查询（参数）部分，除了给动态语言赋值以外，我们同样可以给静态页面，并使用 javascript 来获得相信应的参数值。）
```

7.  window.location.hash (设置或获取 href 属性中在井号“#”后面的分段)

```cpp
var test = window.location.hash;
alert(test);
//返回：空字符(因为 url 中没有)
```

8.  js 获取 url 中的参数值*

    正则法

```cpp
 function getQueryString(name) {
          var reg = new RegExp('(^|&)' + name + '=([^&]*)(&|$)', 'i');
          var r = window.location.search.substr(1).match(reg);

          if (r != null) {
             return unescape(r[2]);
          }
          return null;
  }
// 这样调用：
alert(GetQueryString("参数名 1"));
alert(GetQueryString("参数名 2"));
alert(GetQueryString("参数名 3"));
```

​ split 拆分法

```cpp
function GetRequest() {
         var url = location.search; //获取 url 中"?"符后的字串
         var theRequest = new Object();

         if (url.indexOf("?") != -1) {
                 var str = url.substr(1);
                 strs = str.split("&");
              for(var i = 0; i < strs.length; i ++) {
                      theRequest[strs[i].split("=")[0]] = unescape(strs[i].split("=")[1]);
               }
         }
     return theRequest;
 }
var Request = new Object();
Request = GetRequest();<br>// var id=Request["id"]; 
// var 参数 1,参数 2,参数 3,参数 N;
// 参数 1 = Request['参数 1'];
// 参数 2 = Request['参数 2'];
// 参数 3 = Request['参数 3'];
// 参数 N = Request['参数 N'];
```

​ 指定取
比如说一个 url：[`i.cnblogs.com/?j=js`](https://link.jianshu.com?t=http://i.cnblogs.com/?j=js), 我们想得到参数 j 的值，可以通过以下函数调用。

```cpp
function GetQueryString(name) { 
         var reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)", "i"); 
         var r = window.location.search.substr(1).match(reg); //获取 url 中"?"符后的字符串并正则匹配
         var context = ""; 

     if (r != null) 
     context = r[2]; 
    reg = null; 
    r = null; 
    return context == null || context == "" || context == "undefined" ? "" : context; 
 }
alert(GetQueryString("j"));
```

​ 单个参数的获取方法

```cpp
function GetRequest() {
         var url = location.search; //获取 url 中"?"符后的字串
         if (url.indexOf("?") != -1) {? //判断是否有参数
                  var str = url.substr(1); //从第一个字符开始 因为第 0 个是?号 获取所有除问号的所有符串
                  strs = str.split("=");? //用等号进行分隔 （因为知道只有一个参数 
                                          //所以直接用等号进分隔 如果有多个参数 要用&号分隔 再用等号进行分隔）
                  alert(strs[1]);???? //直接弹出第一个参数 （如果有多个参数 还要进行循环的）
         }
  }
```