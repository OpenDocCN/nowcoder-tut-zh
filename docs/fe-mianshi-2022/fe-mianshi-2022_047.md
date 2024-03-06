# 第五章 第 28 节 前端基础-webapi

> 原文：[`www.nowcoder.com/tutorial/10072/4b83ef30291849ac9dfa908588e1a41b`](https://www.nowcoder.com/tutorial/10072/4b83ef30291849ac9dfa908588e1a41b)

#### 13.1 window.open

**参考答案：**

这个方法是用来打开新窗口的

1.  最基本的弹出窗口

    ```cpp
    window.open('page.html');
    ```

2.  经过设置后的弹出窗口

    ```cpp
     window.open('page.html', 'newwindow', 'height=100, width=400, top=0, left=0, toolbar=no, menubar=no, scrollbars=no, resizable=no, location=no, status=no')   
       //该句写成一行代码
       //参数解释：
       // window.open 弹出新窗口的命令； 
    　　//'page.html' 弹出窗口的文件名； 
    　　//'newwindow' 弹出窗口的名字（不是文件名），非必须，可用空''代替； 
    　　//height=100 窗口高度； 
    　　//width=400 窗口宽度； 
    　　//top=0 窗口距离屏幕上方的象素值； 
    　　//left=0 窗口距离屏幕左侧的象素值； 
    　　//toolbar=no 是否显示工具栏，yes 为显示； 
    　　//menubar，scrollbars 表示菜单栏和滚动栏。 
    　　//resizable=no 是否允许改变窗口大小，yes 为允许； 
    　　//location=no 是否显示地址栏，yes 为允许； 
    　　//status=no 是否显示状态栏内的信息（通常是文件已经打开），yes 为允许
    ```

3.  用函数控制弹出窗口

    ```cpp
     <html> 
    　　<head> 
    　　<script LANGUAGE="JavaScript"> 
    　　<!-- 
    　　function openwin() { 
    　　window.open ("page.html", "newwindow", "height=100, width=400, toolbar =no, menubar=no, scrollbars=no, resizable=no, location=no, status=no") //写成一行
    　　} 
    　　//--> 
    　　</script> 
    　　</head> 
    　　<body οnlοad="openwin()"> 
    　　任意的页面内容... 
    　　</body> 
    　　</html> 
    ```

    解释：这里定义了一个函数 openwin(), 函数内容就是打开一个窗口。在调用它之前没有任何用途。怎么调用呢？ 方法一：<body οnlοad="openwin()"> 浏览器读页面时弹出窗口； 方法二：<body οnunlοad="openwin()"> 浏览器离开页面时弹出窗口； 方法三：用一个连接调用： <a href="#" οnclick="openwin()"> 打开一个窗口 注意：使用的"#"是虚连接。 方法四：用一个按扭调用： <input type="button" οnclick="openwin()" value="打开窗口" />

4.  弹出两个窗口

    ```cpp
    <script LANGUAGE="JavaScript"> 
    　　<!-- 
    　　function openwin() { 
    　　window.open ("page.html", "newwindow", "height=100, width=100, top=0, left=0,toolbar=no, menubar=no, scrollbars=no, resizable=no, location=n o, status=no")//写成一行 
    　　window.open ("page2.html", "newwindow2", "height=100, width=100, top=100, left=100,toolbar=no, menubar=no, scrollbars=no, resizable=no, loca tion=no, status=no")//写成一行 
    　　} 
    　　//--> 
    </script>
    ```

    为避免弹出的 2 个窗口覆盖，用 top 和 left 控制一下弹出的位置不要相互覆盖即可。最后用上面的说过的四种方法调用即可。 注意：2 个窗口的 name(newwindow 与 newwindow2)不要相同，或者干脆全部为空。

5.  主窗口打开文件 1.htm，同时弹出小窗口 page.html

    ```cpp
     function openwin(){
         window.open("page.html","","width=200,height=200") 
     }
    ```

    ```cpp
       //加入 body 区：
     <a href="1.htm" οnclick="openwin()">open</a>即可。
    ```

6.  弹出的窗口之定时关闭控制

    将一小段代码加入弹出的页面（注意是加入 page.html 的 HTML 中，可不是主页面中，否则......），让它在 10 秒后自动关闭

    ```cpp
     function closeit(){
        setTimeout("selft.close()", 10000)   //毫秒
     }
    ```

    ```cpp
    <body οnlοad="closeit()">
    ```

7.  在弹出窗口中加上一个关闭按扭

    ```cpp
    <input type="button" value="关闭" οnclick="window.close()">
    ```

8.  内包含的弹出窗口---一个页面两个窗口

    上面的例子都包含两个窗口，一个是主窗口，另一个是弹出的小窗口。通过下面的例子，你可以在一个页面内完成上面的效果

    ```cpp
    <html> 
    　　<head> 
    　　<SCRIPT LANGUAGE="JavaScript"> 
    　　function openwin() 
    　　{ 
    　　OpenWindow=window.open("", "newwin", "height=250, width=250,toolbar=no ,scrollbars="+scroll+",menubar=no"); 
    　　//写成一行 
    　　OpenWindow.document.write("<TITLE>例子</TITLE>") 
    　　OpenWindow.document.write("<BODY BGCOLOR=#ffffff>") 
    　　OpenWindow.document.write("<h1>Hello!</h1>") 
    　　OpenWindow.document.write("New window opened!") 
    　　OpenWindow.document.write("</BODY>") 
    　　OpenWindow.document.write("</HTML>") 
    　　OpenWindow.document.close() 
    　　} 
    　　</SCRIPT> 
    　　</head> 
    　　<body> 
    　　<a href="#" οnclick="openwin()">打开一个窗口</a> 
    　　<input type="button" οnclick="openwin()" value="打开窗口"> 
    　　</body> 
    　　</html> 
    ```

9.  终极应用---弹出的窗口这 Cookie 控制

    ```cpp
     function openwin(){ 
    　　window.open("page.html","","width=200,height=200") 
     } 
     function get_cookie(Name){
        var search=Name+"=";
        var returnvalue="";
        if(document.cookie.length>0){
    　　    if (offset != -1) { 
    　　      offset += search.length 
    　　      end = document.cookie.indexOf(";", offset); 
    　　      if (end == -1) 
    　　       end = document.cookie.length; 
    　　        returnvalue=unescape(document.cookie.substring(offset, end));
              }
         }
         return returnvalue;
     }
     function ladpopup(){
        if(get_cookie('popped=yes')){
          openwin()
          document.cookie="popped=yes";
        }
    }
    ```

    ```cpp
    <body οnlοad="loadpopup()">
    ```

#### 13.2 实现 Eventemitter 类，有 on、emit、off 方法

**参考答案：**

1.  on(event,fn)：监听 event 事件，事件触发时调用 fn 函数；
2.  once(event,fn)：为指定事件注册一个单次监听器，单次监听器最多只触发一次，触发后立即解除监听器；
3.  emit(event,arg1,arg2,arg3...)：触发 event 事件，并把参数 arg1,arg2,arg3....传给事件处理函数；
4.  off(event,fn)：停止监听某个事件

```cpp
class EventEmitter{
        constructor(){
            this._envents = {}
        }

        on(event,callback){  //监听 event 事件，触发时调用 callback 函数
            let callbacks = this._events[event] || []
            callbacks.push(callback)
            this._events[event] = callbacks
            return this
        }
        off(event,callback){  //停止监听 event 事件
            let callbacks = this._events[event]
            this._events[event] = callbacks && callbacks.filter(fn => fn !== callback)
            return this
        }
        emit(...args){ //触发事件，并把参数传给事件的处理函数
            const event = args[0]
            const params = [].slice.call(args,1)
            const callbacks = this._events[event]
            callbacks.forEach(fn => fn.apply(this.params))
            return this
        }
        once(event,callback){ //为事件注册单次监听器
            let wrapFanc = (...args) => {
                callback.apply(this.args)
                this.off(event,wrapFanc)
            }
            this.on(event,wrapFanc)
            return this
        }

    }
```

#### 13.3 查找给定的两个节点的第一个公共父节点

**参考答案**：

**解题思路**

递归循环树的节点，因二叉树不能重复的特性,当前节点为 p or q ，返回当前节点 父节点循环中，如果找到一个，则查找其他子树 其他子树没有找到另外一个，就证明当前节点为找到的子树是最近公共祖先 两个都找到了，对应当前节点是两个节点的父节点这种情况，则返回当前节点。 代码

```cpp
var lowestCommonAncestor = function(root, p, q) {
if (!root || root === p || root === q) return root
    let left = lowestCommonAncestor(root.left, p, q)
    let right = lowestCommonAncestor(root.right, p, q)
    if (!left) return right
    if (!right) return left
    return root
};
```