# 第七章 第 5 节 前端-业务场景 5

> 原文：[`www.nowcoder.com/tutorial/10072/f6cfd48ceacb42159702abfbf343dd7d`](https://www.nowcoder.com/tutorial/10072/f6cfd48ceacb42159702abfbf343dd7d)

#### 6\. 页面跳转的方式

**参考答案**：

**1.window.location.href 方式**

```cpp
<script language="JavaScript" type="text/javascript">
  window.location.href="http://www.dayanmei.com/";
</script>
```

**2.window.navigate 方式跳转**

```cpp
<script language="javascript">
  window.navigate("top.jsp");
</script>
```

**3.window.loction.replace 方式实现页面跳转，注意跟第一种方式的区别**

```cpp
<script language="javascript">
  window.location.replace("http://www.dayanmei.com");
</script>
```

有 3 个 jsp 页面（1.jsp, 2.jsp, 3.jsp），进系统默认的是 1.jsp ，当我进入 2.jsp 的时候， 2.jsp 里面用 window.location.replace("3.jsp");与用 window.location.href ("3.jsp");从用户界面来看是没有什么区别的，但是当 3.jsp 页面有一个"返回"按钮，调用 window.history.Go(-1); wondow.history.back();方法的时候，一点这个返回按钮就要返回 2.jsp 页面的话，区别就出来了，当用 window.location.replace("3.jsp");连到 3.jsp 页面的话，3.jsp 页面中的调用 window.history.go(-1);wondow.history.back();方法是不好用的，会返回到 1.jsp 。

**4.self.location 方式实现页面跳转，和下面的 top.location 有小小区别**

```cpp
<script language="JavaScript">
      self.location='top.htm';
</script>
```

**5.top.location**

```cpp
<script language="javascript">
      top.location='xx.jsp';
</script>
```

**6.不推荐这种方式跳转**

```cpp
<script language="javascript">
  alert("返回");
  window.history.back(-1);
</script>
```

**在 php 程序中,这种方式跳转前面不能有任何输出**

```cpp
<?php
    header("url.php");
?>
```

#### 7\. 怎么监听内容的改变：监听`oninput`

**参考答案**：

**原生方法**

onchange 事件

```cpp
<input type="text" onchange="onc(this)">
function onc(data){
    console.log(data.value);
}
```

onchange 事件在内容改变且失去焦点的时候触发。即，失去焦点了内容未变不触发，内容变了未失去焦点也不实时触发。
**js 直接更改 value 值时不触发**

**oninput 事件**

```cpp
<input id="inp" type="text" oninput="inp(this)">
function inp(data) {
    console.log(data.value)
}
```

oninput 事件在输入内容改变的时候实时触发。oninput 事件是 IE 之外的大多数浏览器支持的事件，在 value 改变时实时触发。
**js 直接更改 value 值时不触发。**

**onpropertychange 事件**

onpropertychange 事件是实时触发，每增加或删除一个字符就会触发，通过 js 改变也会触发该事件，但是该事件是 IE 专有。
当 input 设置为 disable=true 后，不会触发。

**oninput 事件与 onpropertychange 事件的区别：**

onpropertychange 事件是任何属性改变都会触发，而 oninput 却只在 value 改变时触发，oninput 要通过 addEventListener()来注册，onpropertychange 注册方法与一般事件相同。

**oninput 与 onpropertychange 联合使用**

　　oninput 是 HTML5 的标准事件，对于检测 textarea, input:text, input:password 和 input:search 这几个元素通过用户界面发生的内容变化非常有用，在内容修改后立即被触发，不像 onchange 事件需要失去焦点才触发。oninput 事件在 IE9 以下版本不支持，需要使用 IE 特有的 onpropertychange 事件替代，这个事件在用户界面改变或者使用脚本直接修改内容两种情况下都会触发，有以下几种情况：

修改了 input:checkbox 或者 input:radio 元素的选择中状态， checked 属性发生变化。
修改了 input:text 或者 textarea 元素的值，value 属性发生变化。
修改了 select 元素的选中项，selectedIndex 属性发生变化。
　　在监听到 onpropertychange 事件后，可以使用 event 的 propertyName 属性来获取发生变化的属性名称。

　　集合 oninput & onpropertychange 监听输入框内容变化的示例代码如下：

// Firefox, Google Chrome, Opera, Safari, Internet Explorer from version 9

```cpp
function OnInput (event) {
    alert ("The new content: " + event.target.value);
}
```

// Internet Explorer

```cpp
function OnPropChanged (event) {
    if (event.propertyName.toLowerCase () == "value") {
        alert ("The new content: " + event.srcElement.value);
    }
}

<input type="text" oninput="OnInput (event)" onpropertychange="OnPropChanged (event)" value="Te
```