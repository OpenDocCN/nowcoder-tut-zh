# 第七章 第 10 节 前端-业务场景 10

> 原文：[`www.nowcoder.com/tutorial/10072/dabb56031d0e483fa2c9e5265f416282`](https://www.nowcoder.com/tutorial/10072/dabb56031d0e483fa2c9e5265f416282)

#### 19\. 我们现在需要写一个 foo 函数，这个函数返回首次调用时的 Date 对象，注意是首次。

**参考答案**：

**解决一：普通方法**

```cpp
var t;
function foo() {
    if (t) return t;
    t = new Date()
    return t;
}
```

问题有两个，一是污染了全局变量，二是每次调用 foo 的时候都需要进行一次判断。

**解决二：闭包**

我们很容易想到用闭包避免污染全局变量。

```cpp
var foo = (function() {
    var t;
    return function() {
        if (t) return t;
        t = new Date();
        return t;
    }
})();
```

然而还是没有解决调用时都必须进行一次判断的问题。

**解决三：函数对象**

函数也是一种对象，利用这个特性，我们也可以解决这个问题。

```cpp
function foo() {
    if (foo.t) return foo.t;
    foo.t = new Date();
    return foo.t;
}
```

依旧没有解决调用时都必须进行一次判断的问题。

**解决四：惰性函数**

不错，惰性函数就是解决每次都要进行判断的这个问题，解决原理很简单，重写函数。

```cpp
var foo = function() {
    var t = new Date();
    foo = function() {
        return t;
    };
    return foo();
};
```

#### 20\. 解析 URL Params 为对象

**参考答案**：

**题目**：

```cpp
let url = 'http://www.domain.com/?user=anonymous&id=123&id=456&city=%E5%8C%97%E4%BA%AC&enabled';
parseParam(url)
/* 结果
{ user: 'anonymous',
  id: [ 123, 456 ], // 重复出现的 key 要组装成数组，能被转成数字的就转成数字类型
  city: '北京', // 中文需解码
  enabled: true, // 未指定值得 key 约定为 true
}
*/
```

**答案**：

```cpp
function parseParam(url) {
  const paramsStr = /.+\?(.+)$/.exec(url)[1]; // 将 ? 后面的字符串取出来
  const paramsArr = paramsStr.split('&'); // 将字符串以 & 分割后存到数组中
  let paramsObj = {};
  // 将 params 存到对象中
  paramsArr.forEach(param => {
    if (/=/.test(param)) { // 处理有 value 的参数
      let [key, val] = param.split('='); // 分割 key 和 value
      val = decodeURIComponent(val); // 解码
      val = /^\d+$/.test(val) ? parseFloat(val) : val; // 判断是否转为数字

      if (paramsObj.hasOwnProperty(key)) { // 如果对象有 key，则添加一个值
        paramsObj[key] = [].concat(paramsObj[key], val);
      } else { // 如果对象没有这个 key，创建 key 并设置值
        paramsObj[key] = val;
      }
    } else { // 处理没有 value 的参数
      paramsObj[param] = true;
    }
  })

  return paramsObj;
}
```

#### 21\. 数据结构处理

**参考答案**：

**题目**：

有一个祖先树状 json 对象，当一个人有一个儿子的时候，其 child 为其儿子对象，如果有多个儿子，child 为儿子对象的数组。

请实现一个函数，找出这个家族中所有有多个儿子的人的名字（name），输出一个数组。

```cpp
// 样例数据
let data = {
  name: 'jack',
  child: [
    { name: 'jack1' },
    {
      name: 'jack2',
      child: [{
        name: 'jack2-1',
        child: { name: 'jack2-1-1' }
      }, {
        name: 'jack2-2'
      }]
    },
    {
      name: 'jack3',
      child: { name: 'jack3-1' }
    }
  ]
}
```

**答案**：

*   用递归

    ```cpp
    function findMultiChildPerson(data) {
      let nameList = [];

      function tmp(data) {
        if (data.hasOwnProperty('child')) {
          if (Array.isArray(data.child)) {
            nameList.push(data.name);
            data.child.forEach(child => tmp(child));
          } else {
            tmp(data.child);
          }
        }
      }
      tmp(data);
      return nameList;
    }
    复制代码
    ```

*   非递归

    ```cpp
    function findMultiChildPerson(data) {
      let list = [data];
      let nameList = [];

      while (list.length > 0) {
        const obj = list.shift();
        if (obj.hasOwnProperty('child')) {
          if (Array.isArray(obj.child)) {
            nameList.push(obj.name);
            list = list.concat(obj.child);
          } else {
            list.push(obj.child);
          }
        }
      }
      return nameList;
    }
    ```