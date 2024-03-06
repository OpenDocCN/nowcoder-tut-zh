# 第五章 第 23 节 前端基础-布局-1

> 原文：[`www.nowcoder.com/tutorial/10072/683490f48eaf4d9d8ca468ad40393118`](https://www.nowcoder.com/tutorial/10072/683490f48eaf4d9d8ca468ad40393118)

#### 11.1 未知高度元素垂直居中、垂直居中的实现方式有哪些？

**参考答案：**

**1、绝对定位+css3 transform:translate(-50%，-50%)**

```cpp
.wrap{
  position:relative;
}
.child{
  position: absolute;
  top:50%;
  left:50%;
  -webkit-transform:translate(-50%,-50%);
}
```

**2、css3 的 flex 布局**

```cpp
.wrap{
  display:flex;
  justify-content:center;
}
.child{
  align-self:center;
}
```

**3、table 布局**

```cpp
<div class="wrap">
   <div class="child">
          <div>sadgsdgasgd</div>
   </div>
</div>
.wrap{
  display:table;
  text-align:center;
}
.child{
  background:#ccc;
  display:table-cell;
  vertical-align:middle;
}
.child div{
    width:300px;
    height:150px;
    background:red;
    margin:0 auto;
}
```

#### 11.2 实现图片垂直居中

**参考答案：**

**1\. 使用 flex 实现图片垂直居中**

利用 display: flex;align-items: center 实现垂直居中。flex 可能不是实现垂直居中最好的选择，因为 IE8,9 并不支持它。

html 代码：

```cpp
<div class="flexbox">
    <img src="1.jpg" alt=""></div>
```

css 代码：

```cpp
body{ background:#999}
.flexbox{width: 300px;height: 250px;background:#fff;display: flex;align-items: center}
.flexbox img{width: 100px;height: 100px;align-items: center;}
```

**2\. 利用 Display: table;实现 img 图片垂直居中**

给最外层的 div 设置 display 属性为 table;img 的父元素 div 设置 display:table-cell,vertical-align: middle;如果你也想实现水平居中，你可以给最外层的 div 元素添加 text-align: center 属性

html 代码：

```cpp
<div class="tablebox">
    <div id="imgbox">
        <img src="1.jpg" alt="">
    </div></div>
```

css 代码：

```cpp
.tablebox{width: 300px;height: 250px;background: #fff;display: table}
#imgbox{display: table-cell;vertical-align: middle;}
#imgbox img{width: 100px}
```

**3\. 用绝对定位实现垂直居中（推荐-兼容性好）**

1.  给 img 的父元素添加相对定位属性（position: relative），同时，要给子元素也就是图片 img 元素添加绝对定位属性（position: absolute）。

2.  将图片元素的 top 属性设置为 50%。

3.  现在我们需要给 img 元素设置一个负的 margin-top 值，这个值为你想要实现垂直居中的元素高度的一半，*如果不确定元素的高度，可以不使用 margin-top，而是使用 transform:translateY(-50%);属性。

    记住：如果你想要同时实现水平居中，那么你可以用实现垂直居中的一样的技巧来实现。

    HTML 代码：

    ```cpp
    <div class="posdiv">
    <img src="1.jpg" alt=""></div>
    ```

    css 代码：

    ```cpp
    body{background: #ccc;}
    .posdiv{width: 300px;height: 250px;background: #fff;position: relative; margin:0 auto}
    .posdiv img{width: 100px;position: absolute;top: 50%;margin-top: -50px;}
    ```

#### 11.3 设置斑马线表格(纯 css)

**参考答案：**

```cpp
<!DOCTYPE html>
<html lang="en">
<head>
 <meta charset="UTF-8">
 <title>斑马线表格</title>
 <style type="text/css">
 *{
  margin: 0;
  padding: 0;
  /*清处浏览器默认设置*/
 }
 table{
  /*表格的外边距和大小*/
  margin: 10px 0 0 0;
  width: 100%;
  border-spacing: 0;
  border-collapse: collapse;
  /*collapse 表格单元格边框合并 
   border-spacing 表格单元格间距为零
  */
 }
 caption{
  font: 30px "楷体";
  padding: 5px;
  /*表格标题*/
 }
 td{
  width: 32%;
  height: 50px;
  /*单元格大小*/
 }
 tbody td{
   border: 1px solid;
   /*表格主体的边框*/
 }
 thead{
  background-color: #A2A5A7;
  /*表格头部*/
 }
 tr:hover{
  background-color: #66D9EF;
  cursor: pointer;
  /*鼠标悬停在表格上时，表格的背景和鼠标的形状*/
 }
 table tbody tr:nth-child(even){
  background-color: #8F908A;
  box-shadow: inset 0 5px rgba(255,255,255,0.5);
  /*even 为偶数行 odd 为奇数行
    设置表格的主体部分偶数行的样式
    shadow 阴影  inset 将外部阴影改为内部阴影
  */
 }
 thead tr th:first-child
 {
  /*表头部分 th 第一个 th 左上角设置圆角*/
  border-radius: 15px 0 0 0;
 }
 thead tr td:last-child{
  /*最后一个单元格右上角设置圆角*/
  border-radius: 0 15px 0 0;
 }
 </style>
</head>
<body>
 <table>
 <caption>斑马线表格</caption>
 <thead>
  <tr>
   <th></th>
   <td></td>
   <td></td>
  </tr>
 </thead>
 <tbody>
  <tr>
   <td></td>
   <td></td>
   <td></td>
  </tr>
  <tr>
   <td></td>
   <td></td>
   <td></td>
  </tr>
  <tr>
   <td></td>
   <td></td>
   <td></td>
  </tr>
  <tr>
   <td></td>
   <td></td>
   <td></td>
  </tr>
  <tr>
   <td></td>
   <td></td>
   <td></td>
  </tr>
  <tr>
   <td></td>
   <td></td>
   <td></td>
  </tr>
 </tbody>
  <tfoot>
   <tr>
    <td></td>
    <td></td>
    <td></td>
   </tr>
  </tfoot>
 </table>
</body>
</html>
```

#### 11.4 文本元素如何居中

**参考答案：**

1.  CSS 设置文字水平居中

    在 CSS 中可以使用 text-align 属性来设置文字水平居中。该属性规定元素中的文本的水平对齐方式，通过使用 center 值设置文本居中。

    text-align 是一个基本的属性，它会影响一个元素中的文本行互相间的对齐方式。值 left、right 和 center 会导致元素中的文本分别左对齐、右对齐和居中，想要使文本居中，直接使用 center 即可。

    该属性设置文本和 img 标签等一些内联对象（或与之类似的元素）的居中。

    该属性有如下几个特点：

    1）text-align 的 center 应用在一个容器上，它只针对容器里面的文字以及容器里面的 display 为 inline 或者 inline-block 的容器，如果里面的容器 display 为 block，则里面的容器的内容不会居中。

    2）text-align 具有向下传递性，会不断地向子元素传递。如果设置一个 div，则其子 div 中的内容也会居中。

    ```cpp
    <!DOCTYPE html>
    <html>
     <head>
       <meta charset="UTF-8">
       <title>css 水平居中</title>
       <style>
         .box {
           width: 400px;
           height: 100px;
           background: pink;
           text-align:center;
         }
       </style>
     </head>
     <body>
       <div class="box">css 水平居中了--文本文字</div>
     </body>

    </html>
    ```

2.  CSS 设置字体垂直居中

    2.1 单行文字垂直居中

    对于单行文本，我们只需要将文本行高(line-height 属性)和所在区域高度(height)设置一致就可以了

    ```cpp
    <!DOCTYPE html>
    <html>
     <head>
       <meta charset="UTF-8">
       <title>css 垂直居中</title>
       <style>
         .box {
           width: 300px;
           height: 300px;
           background: paleturquoise;
           line-height:300px;
         }
       </style>
     </head>
     <body>
       <div class="box">css 垂直居中了--文本文字</div>
     </body>
    </html>
    ```

    2.2 多行文本垂直居中

    说明：多行文本垂直居中分为两种情况，一个是父级元素高度不固定，随着内容变化；另一个是父级元素高度固定。

    1) 父级元素高度不固定

    父级高度不固定的时，高度只能通过内部文本来撑开。所以，我们可以通过设置内填充（padding）的值来使文本看起来垂直居中，只需设置 padding-top 和 padding-bottom 的值相等：

    ```cpp
    <!DOCTYPE html>
    <html>
     <head>
       <meta charset="UTF-8">
       <title>css 垂直居中</title>
       <style>
         .box {
           width: 300px;
           margin: 50px auto;
           background: paleturquoise;
           padding: 50px 20px;
         }
       </style>
     </head>
     <body>
       <div class="box">css 垂直居中了--文本文字,文本文字,文本文字,文本文字,文本文字,文本文字</div>
     </body>
    </html>
    ```

2) 父级元素高度固定

使用 vertical-align:middle +display:table-cell 使文字垂直居中

```cpp
   <!DOCTYPE html>
   <html>
    <head>
      <meta charset="UTF-8">
      <title>css 垂直居中</title>
      <style>
        .box {
          width: 300px;
          height: 300px;
          background: paleturquoise;
          vertical-align:middle;
          display:table-cell;
        }
      </style>

    </head>

    <body>

      <div class="box">css 垂直居中了--文本文字,文本文字,文本文字,文本文字,文本文字,文本文字。</div>

    </body>

   </html>
```

说明：vertical-align:middle +display:table-cell 能够使单行文字、多行文字都居中。但是因为 table-cell 是 inline 类型，所以会导致原来的块级元素每个 div 一行移动到了同一行。如果需要分列两行，需要在外面额外添加容器对位置进行控制。

#### 11.5 用 flex 实现九宫格讲思路

**参考答案：**

利用了 padding-top 和 flex-wrap:wrap，当设置 background-color 时，是包括盒子模型中的 content 和 padding 的，但是为什么不设置 height 呢？因为父元素没有高度，所以定义 height:30%是没有用的，且若想每个 block 都为正方形，最好的方式就是设置 padding-top/padding-bottom：a%，因为此时的百分比是父元素宽度的百分比，而 width 也为父元素宽度的百分比，所以 block 可以成为正方形。

```cpp
<!DOCTYPE html>
<html>
<style>
.block {
    padding-top: 30%;
    margin-top: 3%;
    border-radius: 10%;
    background-color: orange;
    width: 30%;
}
.container-flex2  {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
}
</style>
<body>
   <div class="container-flex2">
        <div class="block"></div>
        <div class="block"></div>
        <div class="block"></div>
        <div class="block"></div>
        <div class="block"></div>
        <div class="block"></div>
        <div class="block"></div>
        <div class="block"></div>
        <div class="block"></div>
    </div>
</body>
</html>
```

#### 11.6 CSS 实现一个等腰三角形

**参考答案**：

主要是通过把宽高设置成 0，边框宽度设置宽一些，设置其中三个边透明，只留一个边显示

等边三角形是特殊的等腰三角形，它的三条边都相等，顶角为 60 度，而高是边长的 3^(1/2)/2 倍，约等于 0.866……假设底为 160px，则高约为 138.56px，因此要做边长为 160px 的等边三角形，可以这么做：

```cpp
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8" />
  <title>测试</title>
  <style type="text/css">
    div {
       width:0px;height:0px;margin:100px auto;
       border-left:80px solid transparent; 
       border-right:80px solid transparent; 
       border-bottom:138.56px solid #A962CE; /*--三角形的高--*/
    }
  </style>
</head>
<body>
  <div>
  </div>
</body>
</html>
```

**扩展：**

用 CSS 实现一个等边三角形：

根据各个边之间的长度关系，我们易知：需要展示的边框的宽度：相邻的透明的边框的宽度 = √3 ：1

```cpp
.triangle{
  width: 0px;
  height: 0px;
  border-left: 10px solid transparent;
  border-right: 10px solid transparent;;
  border-top: 17.32px solid transparent;
  border-bottom: 17.32px solid red; 
}
```