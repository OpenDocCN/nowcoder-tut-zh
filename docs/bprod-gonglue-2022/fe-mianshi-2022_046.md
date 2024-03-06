# 第五章 第 27 节 前端基础-属性 2

> 原文：[`www.nowcoder.com/tutorial/10072/e63ed8dc1e3f4fc3be1f1c109ab029ce`](https://www.nowcoder.com/tutorial/10072/e63ed8dc1e3f4fc3be1f1c109ab029ce)

#### 12.5 css3 新特性

**参考答案**：

1.  **CSS3 边框**

    在 css3 中新增的边框属性如下：

    **创建圆角**

    **语法：** `border-radius : length length;`

    ​ length： 由浮点数字和单位标识符组成的长度值（如：20px）。不可为负值，如果为负值则与 0 展示效果一样。第一个值设置其水平半径，第二个值设置其垂直半径，如果第二个值省略则默认第二个值等于第一个值。

    ```cpp
    div{
      border: 1px solid;
      /* 设置每个圆角水平半径和垂直半径都为 30px */
      border-radius: 30px;
    }
    ```

    `border-radius` 是 4 个角的缩写方法。四个角的表示顺序与`border`类似按照`border-top-left-radius`、`border-top-right-radius`、`border-bottom-right-radius`、`border-bottom-left-radius`的顺序来设置：

    ```cpp
    div{
      border: 1px solid;
      /* 如果 / 前后的值都存在，那么 / 前面的值设置其水平半径，/ 后面值设置其垂直半径，如果没有 / ，则水平和垂直半径相等 */
      border-radius: 10px 15px 20px 30px / 20px 30px 10px 15px;

      /* 上面写法等价于下面的写法，第一个值是水平半径，第二个值是垂直半径 */
      border-top-left-radius: 10px 20px;
      border-top-right-radius: 15px 30px;
      border-bottom-right-radius: 20px 10px;
      border-bottom-left-radius: 30px 15px;
    }
    ```

    `border-radius` 指定不同数量的值遵循对角相等的原则，即指定了值的取指定值，没有指定值的与对角值相等，对角相等模型

    **边框阴影**

    通过属性`box-shadow` 向边框添加阴影。

    **语法：** `{box-shadow : [inset] x-offset y-offset blur-radius extension-radius spread-radiuscolor}`

    说明：对象选择器 {box-shadow:[投影方式] X 轴偏移量 Y 轴偏移量 模糊半径 阴影扩展半径 阴影颜色}

    ```cpp
    div{
      /* 内阴影，向右偏移 10px，向下偏移 10px，模糊半径 5px，阴影缩小 10px */
      box-shadow: inset 10px 10px 5px -10px #888888;
    }
    ```

    **边框图片**

    **语法：**

    `border-image : border-image-source || border-image-slice [ / border-image-width] || border-image-repeat`

    `border-image ： none | image [ number | percentage]{1,4} [ / border-width>{1,4} ] ? [ stretch | repeat | round ]{0,2}`

    ```cpp
    div{
      border-image:url(border.png) 30 30 round;

      border-image: url(border.png) 20/10px repeat;
    }
    ```

2.  **CSS3 背景**

**`background-size` 属性**

在 CSS3 之前，背景图片的尺寸是由图片的实际尺寸决定的。在 CSS3 中，可以设置背景图片的尺寸，这就允许我们在不同的环境中重复使用背景图片。可以像素或百分比规定尺寸。如果以百分比规定尺寸，那么尺寸相对于父元素的宽度和高度。

```cpp
div{
  background:url(bg_flower.gif);
  /* 通过像素规定尺寸 */
  background-size:63px 100px;

  /* 通过百分比规定尺寸 */
  background-size:100% 50%;
  background-repeat:no-repeat;
}
```

**`background-origin` 属性**

规定背景图片的定位区域，背景图片可以放置于 `content-box`、`padding-box` 或 `border-box` 区域，

```cpp
div{
  background:url(bg_flower.gif);
  background-repeat:no-repeat;
  background-size:100% 100%;
  /* 规定背景图片的定位区域 */
  background-origin:content-box;
}
```

**`background-clip` 属性**

与`background-origin` 属性相似，规定背景颜色的绘制区域，区域划分与`background-origin` 属性相同。

```cpp
div{
  background-color:yellow;
  background-clip:content-box;
}
```

**CSS3 多重背景图片**

CSS3 允许为元素设置多个背景图像

```cpp
body{
  background-image:url(bg_flower.gif),url(bg_flower_2.gif);
}
```

3.  CSS3 文本效果

**`text-shadow` 属性**

给为本添加阴影，能够设置水平阴影、垂直阴影、模糊距离，以及阴影的颜色。

```cpp
h1{
  text-shadow: 5px 5px 5px #FF0000;
}
```

**text-wrap 属性**

设置区域内的自动换行。

**语法：**`text-wrap: normal | none | unrestricted | suppress | break-word;`

```cpp
/* 允许对长单词进行拆分，并换行到下一行 */
p {word-wrap:break-word;}
```

| 值 | 描述 |
| --- | --- |
| normal | 只在允许的换行点进行换行。 |
| none | 不换行。元素无法容纳的文本会溢出。 |
| break-word | 在任意两个字符间换行。 |
| suppress | 压缩元素中的换行。浏览器只在行中没有其他有效换行点时进行换行。 |

4.  CSS3 字体

**字体定义**

​ 在 CSS3 之前，web 设计师必须使用已在用户计算机上安装好的字体。但是通过 CSS3，web 设计师可以使用他 们喜欢的任意字体。当找到或购买到希望使用的字体时，可将该字体文件存放到 web 服务器上，它会在需要时 被自动下载到用户的计算机上。字体需要在 CSS3 @font-face 规则中定义。

```cpp
/* 定义字体 */
@font-face{
  font-family: myFont;
  src: url('Sansation_Light.ttf'),
       url('Sansation_Light.eot');     /* IE9+ */
}

div{
  font-family:myFont;
}
```

**使用粗体字体**

"Sansation_Light.ttf"文件 是定义的正常字体，"Sansation_Bold.ttf" 是另一个字体文件，它包含了 Sansation 字体的粗体字符。只要 font-family 为 "myFirstFont" 的文本需要显示为粗体，浏览器就会使用该字体。

（其实没弄清楚这样处理的原因，经测试直接在 html 中通过 b 标签也可以实现加粗的效果）

```cpp
/* 定义正常字体 */
@font-face{
  font-family: myFirstFont;
  src: url('/example/css3/Sansation_Light.ttf'),
       url('/example/css3/Sansation_Light.eot'); /* IE9+ */
}

/* 定义粗体时使用的字体 */
@font-face{
  font-family: myFirstFont;
  src: url('/example/css3/Sansation_Bold.ttf'),
       url('/example/css3/Sansation_Bold.eot'); /* IE9+ */
  /* 标识属性 */
  font-weight:bold;
}

div{
  font-family:myFirstFont;
}
```

5.  CSS3 2D 转换

通过 CSS3 转换，我们能够对元素进行**移动、缩放、转动、拉长或拉伸**，转换是使元素改变形状、尺寸和位置的一种效果。

**translate() 方法**

通过 translate(x , y) 方法，元素根据给定的 left（x 坐标） 和 top（y 坐标） 位置参数从其当前位置移动，x 为正值向右移动，为负值向左移动；y 为正值向下移动，为负值向上移动；

```cpp
div{
  transform: translate(50px,100px);
  -ms-transform: translate(50px,100px);        /* IE 9 */
  -webkit-transform: translate(50px,100px);     /* Safari and Chrome */
  -o-transform: translate(50px,100px);         /* Opera */
  -moz-transform: translate(50px,100px);        /* Firefox */
}
```

**rotate() 方法**

控制元素顺时针旋转给定的角度。为正值，元素将顺时针旋转。为负值，元素将逆时针旋转。

```cpp
div{
  transform: rotate(30deg);
  -ms-transform: rotate(30deg);        /* IE 9 */
  -webkit-transform: rotate(30deg);    /* Safari and Chrome */
  -o-transform: rotate(30deg);         /* Opera */
  -moz-transform: rotate(30deg);       /* Firefox */
}
```

**scale() 方法**

根据给定的宽度（X 轴）和高度（Y 轴）参数，控制元素的尺寸的增加、减少。

```cpp
div{
  transform: scale(2,4);
  -ms-transform: scale(2,4);         /* IE 9 */
  -webkit-transform: scale(2,4);     /* Safari 和 Chrome */
  -o-transform: scale(2,4);         /* Opera */
  -moz-transform: scale(2,4);       /* Firefox */
}
```

**skew() 方法**

根据给定的水平线（X 轴）和垂直线（Y 轴）参数设置元素翻转给定的角度。

```cpp
/* 设置围绕 X 轴把元素翻转 30 度，围绕 Y 轴翻转 20 度。 */
div{
  transform: skew(30deg,20deg);
  -ms-transform: skew(30deg,20deg);         /* IE 9 */
  -webkit-transform: skew(30deg,20deg);     /* Safari and Chrome */
  -o-transform: skew(30deg,20deg);          /* Opera */
  -moz-transform: skew(30deg,20deg);        /* Firefox */
}
```

**matrix() 方法**

matrix() 方法把所有 2D 转换方法组合在一起。matrix() 方法需要六个参数，包含数学函数，允许旋转、缩放、移动以及倾斜元素。

```cpp
/* 使用 matrix 方法将 div 元素旋转 30 度 */
div{
  transform:matrix(0.866,0.5,-0.5,0.866,0,0);
  -ms-transform:matrix(0.866,0.5,-0.5,0.866,0,0);          /* IE 9 */
  -moz-transform:matrix(0.866,0.5,-0.5,0.866,0,0);         /* Firefox */
  -webkit-transform:matrix(0.866,0.5,-0.5,0.866,0,0);      /* Safari and Chrome */
  -o-transform:matrix(0.866,0.5,-0.5,0.866,0,0);           /* Opera */
}
```

**2D Transform 方法汇总**

| 函数 | 描述 |
| --- | --- |
| matrix(n,n,n,n,n,n) | 定义 2D 转换，使用六个值的矩阵。 |
| translate(x,y) | 定义 2D 转换，沿着 X 和 Y 轴移动元素。 |
| translateX(n) | 定义 2D 转换，沿着 X 轴移动元素。 |
| translateY(n) | 定义 2D 转换，沿着 Y 轴移动元素。 |
| scale(x,y) | 定义 2D 缩放转换，改变元素的宽度和高度。 |
| scaleX(n) | 定义 2D 缩放转换，改变元素的宽度。 |
| scaleY(n) | 定义 2D 缩放转换，改变元素的高度。 |
| rotate(angle) | 定义 2D 旋转，在参数中规定角度。 |
| skew(x-angle,y-angle) | 定义 2D 倾斜转换，沿着 X 和 Y 轴。 |
| skewX(angle) | 定义 2D 倾斜转换，沿着 X 轴。 |
| skewY(angle) | 定义 2D 倾斜转换，沿着 Y 轴。 |

6.  CSS3 3D 转换

CSS3 允许使用 3D 转换来对元素进行格式化

**rotateX() 方法**

```cpp
/* 设置元素围绕其 X 轴以给定的度数进行旋转 */
div{
  transform: rotateX(120deg);
  -webkit-transform: rotateX(120deg);   /* Safari 和 Chrome */
  -moz-transform: rotateX(120deg);  /* Firefox */
}
```

**rotateY() 旋转**

```cpp
/* 设置元素围绕其 Y 轴以给定的度数进行旋转 */
div{
  transform: rotateY(130deg);
  -webkit-transform: rotateY(130deg);   /* Safari 和 Chrome */
  -moz-transform: rotateY(130deg);  /* Firefox */
}
```

7.  CSS3 过渡

    通过 CSS3 可以在不使用 Flash 动画或 JavaScript 的情况下，当元素从一种样式变换为另一种样式时为元素添加效果。

    要实现这一点，必须规定以下两项内容：

    *   设置添加过渡效果的 CSS 属性；
    *   设置过渡效果的时长；

    **注意：** 如果时长未设置，则不会有过渡效果，因为默认值是 0。

**单项改变**

```cpp
/* 设置将变化效果添加在“宽度”上，时长为 2 秒；该时长在其他属性上并不适用 */
div{
  transition: width 2s;
  -moz-transition: width 2s;         /* Firefox 4 */
  -webkit-transition: width 2s;      /* Safari 和 Chrome */
  -o-transition: width 2s;           /* Opera */
}
/* 配合在一起使用的效果就是当鼠标移上去的时候宽度变为 300px，这个过程耗时 2 秒 */
div:hover{
  width:300px;
}
```

​ **注意：** 当鼠标移出元素时，它会逐渐变回原来的样式。

**多项改变**

如需向多个样式添加过渡效果，请添加多个属性，由逗号隔开。

```cpp
/* 同时向宽度、高度和转换添加过渡效果 */
div{
  transition: width 2s, height 2s, transform 2s;
  -moz-transition: width 2s, height 2s, -moz-transform 2s;
  -webkit-transition: width 2s, height 2s, -webkit-transform 2s;
  -o-transition: width 2s, height 2s,-o-transform 2s;
}

/* 当鼠标移上时宽度和高度都变成 200px，同时旋转 180 度，每个属性变化都耗时 2 秒 */
div:hover{
  width:200px;
  height:200px;
  transform:rotate(180deg);
  -moz-transform:rotate(180deg);        /* Firefox 4 */
  -webkit-transform:rotate(180deg);     /* Safari and Chrome */
  -o-transform:rotate(180deg);          /* Opera */
}
```

**过渡属性详解**

`transition` 是简写属性，

**语法：** `transition : transition-property | transition-duration | transition-timing-function | transition-delay;`

```cpp
/* 设置在宽度上添加过渡效果，时长为 1 秒，过渡效果时间曲线为 linear，等待 2 秒后开始过渡 */
div{
  transition: width 1s linear 2s;
  -moz-transition: width 1s linear 2s;       /* Firefox 4 */
  -webkit-transition: width 1s linear 2s;    /* Safari and Chrome */
  -o-transition: width 1s linear 2s;         /* Opera */
}
```

| 属性 | 描述 |
| --- | --- |
| transition | 简写属性，用于在一个属性中设置四个过渡属性。 |
| transition-property | 规定应用过渡的 CSS 属性的名称。 |
| transition-duration | 定义过渡效果花费的时间。默认是 0。 |
| transition-timing-function | 规定过渡效果的时间曲线。默认是 "ease"。 |
| transition-delay | 规定过渡效果何时开始。默认是 0。 |

8.  CSS3 动画

    通过 CSS3 可以创建动画，这些动画可以取代网页中的画图片、Flash 动画以及 JavaScript。

    CSS3 中通过@keyframes 规则来创建动画。在 @keyframes 中规定某项 CSS 样式，就能创建由当前样式（动画开始前的样式）逐渐改为新样式（需要变到的样式）的动画效果。

**通过 from , to 关键字设置动画发生的时间**

```cpp
/* 通过@keyframes 创建动画 */
@keyframes myfirst{
  from {background: red;}
  to {background: yellow;}
}
/* Firefox */
@-moz-keyframes myfirst {
  from {background: red;}
  to {background: yellow;}
}
/* Safari 和 Chrome */
@-webkit-keyframes myfirst {
  from {background: red;}
  to {background: yellow;}
}
/* Opera */
@-o-keyframes myfirst {
  from {background: red;}
  to {background: yellow;}
}

/*
   将创建的动画绑定到选择器，并至少指定以下两项 CSS3 动画属性
   1.指定动画的名称；
   2.指定动画的时长；
*/
div{
  animation: myfirst 5s;
  -moz-animation: myfirst 5s;       /* Firefox */
  -webkit-animation: myfirst 5s;    /* Safari 和 Chrome */
  -o-animation: myfirst 5s;         /* Opera */
}
```

**通过百分比设置动画发生的时间**

动画是使元素从一种样式逐渐变化为另一种样式的效果。可以改变任意多的样式任意多的次数。可以用关键词 "from" 和 "to"来设置动画变化发生的时间，其效果等同于 0% 和 100%。0% 是动画的开始，100% 是动画的完成。为了得到最佳的浏览器支持，应该始终定义 0% 和 100% 选择器。

```cpp
/* 当动画为 25% 及 50% 时改变背景色，然后当动画 100% 完成时再次改变 */
@keyframes myfirst{
  0%   {background: red;}
  25%  {background: yellow;}
  50%  {background: blue;}
  100% {background: green;}
}

/* 同时改变背景色和位置 */
@keyframes myfirst{
  0%   {background: red; left:0px; top:0px;}
  25%  {background: yellow; left:200px; top:0px;}
  50%  {background: blue; left:200px; top:200px;}
  75%  {background: green; left:0px; top:200px;}
  100% {background: red; left:0px; top:0px;}
}
```

**动画属性详解**

`animation` 是除了 `animation-play-state` 属性所有动画属性的简写属性。

**语法：** `animation : animation-name | animation-duration | animation-timing-function | animation-delay | animation-iteration-count | animation-direction`

```cpp
/* 应用的动画为 myfirst，一个动画周期为 5 秒，动画的速度曲线为 linear，动画 2 秒后播放，播放次数为 infinite，即无限循环，动画下一周期是否逆向播放取值 alternate，即逆向播放 */
div{
  animation: myfirst 5s linear 2s infinite alternate;
  /* Firefox: */
  -moz-animation: myfirst 5s linear 2s infinite alternate;
  /* Safari 和 Chrome: */
  -webkit-animation: myfirst 5s linear 2s infinite alternate;
  /* Opera: */
  -o-animation: myfirst 5s linear 2s infinite alternate;
}
```

| 属性 | 描述 |
| --- | --- |
| @keyframes | 规定动画。 |
| animation | 所有动画属性的简写属性，除了 animation-play-state 属性。 |
| animation-name | 规定 @keyframes 动画的名称。 |
| animation-duration | 规定动画完成一个周期所花费的秒或毫秒。默认是 0。 |
| animation-timing-function | 规定动画的速度曲线。默认是 "ease"。 |
| animation-delay | 规定动画何时开始。默认是 0。 |
| animation-iteration-count | 规定动画被播放的次数。默认是 1。 |
| animation-direction | 规定动画是否在下一周期逆向地播放。默认是 "normal"。 |
| animation-play-state | 规定动画是否正在运行或暂停。默认是 "running"。 |
| animation-fill-mode | 规定对象动画时间之外的状态。 |

9.  CSS3 多列

    通过 CSS3 够创建多个列来对文本进行布局，就像我们经常看到的报纸的布局一样。

    **CSS3 创建多列**

    `column-count` 属性规定元素应该被分隔的列数。

```cpp
/* 将 div 中的文本分为 3 列 */
div{
  column-count:3;
  -moz-column-count:3;        /* Firefox */
  -webkit-column-count:3;     /* Safari 和 Chrome */
}
```

**CSS3 规定列之间的间隔**

`column-gap` 属性规定列之间的间隔。

```cpp
/* 设置列之间的间隔为 40 像素 */
div{
  column-gap:40px;
  -moz-column-gap:40px;        /* Firefox */
  -webkit-column-gap:40px;     /* Safari 和 Chrome */
}
```

**CSS3 列规则**

`column-rule` 属性设置列之间的宽度、样式和颜色规则。

**语法：** `column-rule : column-rule-width | column-rule-style | column-rule-color`

```cpp
div{
  column-rule:3px outset #ff0000;
  -moz-column-rule:3px outset #ff0000;       /* Firefox */
  -webkit-column-rule:3px outset #ff0000;    /* Safari and Chrome */
}
```

| 属性 | 描述 |
| --- | --- |
| column-count | 规定元素应该被分隔的列数。 |
| column-fill | 规定如何填充列。 |
| column-gap | 规定列之间的间隔。 |
| column-rule | 设置所有 column-rule-* 属性的简写属性。 |
| column-rule-width | 规定列之间规则的宽度。 |
| column-rule-style | 规定列之间规则的样式。 |
| column-rule-color | 规定列之间规则的颜色。 |
| column-span | 规定元素应该横跨的列数。 |
| column-width | 规定列的宽度。 |
| columns | 语法 : column-width column-count。 |

10.  CSS3 用户界面

**CSS3 resize**

在 CSS3 中`resize` 属性设置是否可由用户调整元素尺寸。

```cpp
/* 设置 div 可以由用户调整大小 */
div{
  resize:both;
  overflow:auto;
}
```

**CSS3 box-sizing**

`box-sizing` 属性允许您以确切的方式定义适应某个区域的具体内容。边框计算在 width 中

```cpp
/* 规定两个并排的带边框方框 */
div{
  box-sizing:border-box;
  -moz-box-sizing:border-box;        /* Firefox */
  -webkit-box-sizing:border-box;     /* Safari */
  width:50%;
  float:left;
}
```

**CSS3 outline-offset**

`outline-offset` 属性对轮廓进行偏移，并在超出边框边缘的位置绘制轮廓。

轮廓与边框有两点不同：

> *   轮廓不占用空间；
> *   轮廓可能是非矩形；

```cpp
/* 规定边框边缘之外 15 像素处的轮廓 */
div{
  border:2px solid black;
  outline:2px solid red;
  outline-offset:15px;
}
```

#### 12.6 css：inline-block 的 div 之间的空隙，原因及解决

**参考答案**：

display:inline-block 布局的元素在 chrome 下会出现几像素的间隙，原因是因为我们在编辑器里写代码的时候，同级别的标签不写在同一 行以保持代码的整齐可读性，即 inline-block 布局的元素在编辑器里不在同一行，即存在换行符，因此这就是著名的 inline-block“换行 符/空格间隙问题”。如果 inline-block 元素间有空格或是换行产生了间隙，那是正常的，应该的。如果没有空格与间隙才是不正常的（**IE6/7** block 水平元素）。

**解决方法：**

1、把 img 标签的 display 属性改成 block：

```cpp
img{dispaly:block;}
```

2、把 div 中的字体大小设为 0：

```cpp
div{font-size:0;}
```

3、如果是 img，修改 img 的 vertical-align 属性：

```cpp
img{vertical-align:buttom;}
img{vertical-align:middle;}
img{vertical-align:top;}
```

4.  移除标签间的空格

```cpp
<ul>
    <li>这是一个 li</li><li>这是另一个 li</li><li>这是另另一个 li</li><li>这是另另另一个 li</li>
</ul>

// 方式二：在标签结束处消除换行符
<ul>
    <li>这是一个 li
    </li><li>这是另一个 li
    </li><li>这是另另一个 li
    </li><li>这是另另另一个 li</li>
</ul>

// 方式三：HTML 注释标签
<ul>
    <li>这是一个 li</li><!--
    --><li>这是另一个 li</li><!--
    --><li>这是另另一个 li</li><!--
    --><li>这是另另另一个 li</li>
</ul>

```