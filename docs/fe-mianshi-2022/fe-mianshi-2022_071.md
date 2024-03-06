# 第七章 第 3 节 前端-业务场景 3

> 原文：[`www.nowcoder.com/tutorial/10072/5e87cfd685ce470485327ff9b1a87536`](https://www.nowcoder.com/tutorial/10072/5e87cfd685ce470485327ff9b1a87536)

#### 3.图片压缩算法

**参考答案**：

**PNG 图片的压缩，分两个阶段：**

*   `预解析（Prediction）`：这个阶段就是对 png 图片进行一个预处理，处理后让它更方便后续的压缩。说白了，就是一个女神，在化妆前，会先打底，先涂乳液和精华，方便后续上妆、美白、眼影、打光等等。
*   `压缩（Compression）`：执行 Deflate 压缩，该算法结合了 LZ77 算法和 Huffman 算法对图片进行编码。

**预解析**（Prediction）

png 图片用差分编码（Delta encoding）对图片进行预处理，处理每一个的像素点中每条通道的值，差分编码主要有几种：

*   不过滤
*   X-A
*   X-B
*   X-(A+B)/2(又称平均值)
*   Paeth 推断（这种比较复杂）

假设，一张 png 图片如下:

![](img/ae3ae843c4168ea3beb0c67c5539b812.png)

这张图片是一个红色逐渐增强的渐变色图，它的红色从左到右逐渐加强，映射成数组的值为[1,2,3,4,5,6,7,8]，使用 X-A 的差分编码的话，那就是:

[2-1=1, 3-2=1, 4-3=1, 5-4=1, 6-5=1, 7-6=1, 8-7=1]

得到的结果为

[1,1,1,1,1,1,1]

最后的[1,1,1,1,1,1,1]这个结果出现了大量的重复数字，这样就非常适合进行压缩。

这就是为什么渐变色图片、颜色值变化不大并且颜色单一的图片更容易压缩的原理。

差分编码的目的，就是尽可能的将 png 图片数据值转换成一组重复的、低的值，这样的值更容易被压缩。

最后还要注意的是，差分编码处理的是每一个的像素点中每条颜色通道的值，R（红）、G（绿）、B（蓝）、A（透明）四个颜色通道的值分别进行处理。

**压缩（Compression）**

压缩阶段会将预处理阶段得到的结果进行 Deflate 压缩，它由 Huffman 编码 和 LZ77 压缩构成。

如前面所说，Deflate 压缩会标记图片所有的重复数据，并记录数据特征和结构，会得到一个压缩比最大的 png 图片 编码数据。

Deflate 是一种压缩数据流的算法. 任何需要流式压缩的地方都可以用。

还有就是我们前面说过，一个 png 图片，是由很多的数据块构成的，但是数据块里面的一些信息其实是没有用的，比如用 Photoshop 保存了一张 png 图片，图片里就会有一个区块记录“这张图片是由 photshop 创建的”，很多类似这些信息都是无用的，如果用 photoshop 的“导出 web 格式”就能去掉这些无用信息。导出 web 格式前后对比效果如下图所示：

![](img/34acb3c49c94cb3e94ffd115589484dd.png)

#### 4\. 加载很多图片时的优化方法，页面加载时的交互优化

**参考答案**：

**1\. 图片压缩**

页面是由“小图”平铺来的，却需要加载大量原图，得不偿失。于是很自然的会想到，将“小图”变为真正的小图，当实际点击大图时再去请求原图，这样便会大大减少页面加载时间。

**a. 图片异源加载**
HTML 代码 img 标签中将真实图片地址写在 data-original 属性中，而 src 属性中的图片换成占位符的图片（压缩图）

```cpp
<!--
添加 width 和 height 属性有助于在图片未加载时占满所需要的空间
-->
<img class="lazy" src="grey.gif" data-original="example.jpg" width="640" heigh="480">
```

* * *

**b. Java 后台图片压缩**

*   **利用 Java 原生的 imageIO 类进行裁剪**

```cpp
/**
     * 缩放图像（按比例缩放）
     *
     * @param src    源图像
     * @param output 输出流
     * @param scale  缩放比例
     * @param flag   缩放选择:true 放大; false 缩小;
     */
    public static final void zoomScale(BufferedImage src, OutputStream output, String type, double scale, boolean flag) {
        try {
            // 得到源图宽
            int width = src.getWidth();
            // 得到源图长
            int height = src.getHeight();
            if (flag) {
                // 放大
                width = Long.valueOf(Math.round(width * scale)).intValue();
                height = Long.valueOf(Math.round(height * scale)).intValue();
            } else {
                // 缩小
                width = Long.valueOf(Math.round(width / scale)).intValue();
                height = Long.valueOf(Math.round(height / scale)).intValue();
            }
            Image image = src.getScaledInstance(width, height, Image.SCALE_DEFAULT);
            BufferedImage tag = new BufferedImage(width, height, BufferedImage.TYPE_INT_RGB);
            Graphics g = tag.getGraphics();
            // 绘制缩小后的图
            g.drawImage(image, 0, 0, null);
            g.dispose();
            // 输出为文件
            ImageIO.write(tag, type, output);
        } catch (IOException e) {
            throw new KitException(e);
        }
    }
```

使用原生 imageIO 类进行压缩图片，速度较快，但仅能对图片尺寸进行压缩，但不能压缩图片质量。

**借助一些三方插件，如使用 google 开源工具 Thumbnailator 实现图片压缩**

Thumbnailator 是一个用 java 生成高质量缩略图的第三方库,可以用来：

1.生成缩率图;2.添加水印;3.图片旋转;4.图片大小缩放;5.图片压缩;

Thumbnailator 库只有一个 jar,不依赖第三方库,maven 依赖

```cpp
<dependency>
  <groupId>net.coobird</groupId>
  <artifactId>thumbnailator</artifactId>
  <version>0.4.8</version>
</dependency>
```

Thumbnailator 接口链式风格写法，使用简单

```cpp
Thumbnails.of("原图文件的路径").scale(1f).outputQuality(0.5f).toFile("压缩后文件的路径");
```

**前端 JS 实现图片压缩**
HTML5 file API 加 canvas 实现图片 JS 压缩

```cpp
/**
 * 图片压缩，默认同比例压缩
 * @param {Object} path 
 *   图片路径
 * @param {Object} obj
 *   obj 对象 有 width， height， quality(0-1)   不传 width 和 height，图片大小不变只改变像素值
 * @param {Object} callback
 *   回调函数有一个参数，base64 的字符串数据
*/
function dealImage(path, obj, callback){

        var img = new Image();
        img.src = path;
        img.onload = function(){
            var that = this;
            // 默认按比例压缩
            var w = that.width,
            h = that.height,
            scale = w / h;
            w = obj.width || w;
            h = obj.height || (w / scale);
            var quality = 0.3;  // 默认图片质量为 0.7
            //生成 canvas
            var canvas = document.createElement('canvas');
            var ctx = canvas.getContext('2d');
            // 创建属性节点
            var anw = document.createAttribute("width");
            anw.nodeValue = w;
            var anh = document.createAttribute("height");
            anh.nodeValue = h;
            canvas.setAttributeNode(anw);
            canvas.setAttributeNode(anh); 
            ctx.drawImage(that, 0, 0, w, h);
            // 图像质量
            if(obj.quality && obj.quality <= 1 && obj.quality > 0){
                quality = obj.quality;
            }
            // quality 值越小，所绘制出的图像越模糊
            var base64 = canvas.toDataURL('image/jpeg', quality );
            // 回调函数返回 base64 的值
            callback(base64);
        }
    }       
```

**TIPS：**上述压缩图片的方式，存在资源争抢时效率会大大降低。压缩图片均需要读原图然后进行压缩生成图片流或文件，如果原图本身较大、数量较多且有多个线程同时进行压缩时，每张图片压缩的时长均会成倍的增长。

**2\. 将图片转 Base64 格式来节约请求**

当我们的一个页面中要传入很多图片时，特别是一些比较小的图片，十几 K、几 K，这些小图标都会增加 HTTP 请求。比如要下载一些一两 K 大的小图标，其实请求时带上的额外信息有可能比图标的大小还要大。所以，在请求越多时，在网络传输的数据自然就越多了，传输的数据自然也就变慢了。而这里，我们采用 Base64 的编码方式将图片直接嵌入到网页中，而不是从外部载入，这样就减少了 HTTP 请求。

*   **Base64 编码由来**

> 为什么会有 Base64 编码呢？因为有些网络传送渠道并不支持所有的字节，例如传统的邮件只支持可见字符的传送，像 ASCII 码的控制字符就 不能通过邮件传送。这样用途就受到了很大的限制，比如图片二进制流的每个字节不可能全部是可见字符，所以就传送不了。最好的方法就是在不改变传统协议的情 况下，做一种扩展方案来支持二进制文件的传送。把不可打印的字符也能用可打印字符来表示，问题就解决了。Base64 编码应运而生，Base64 就是一种 基于 64 个可打印字符来表示二进制数据的表示方法。

*   **Base64 编码索引**

Base64 的索引表如下，字符选用了"A-Z、a-z、0-9、+、/" 64 个可打印字符。数值代表字符的索引，这个是标准 Base64 协议规定的，不能更改。

![](img/e8f4fec2980219f995e079deb9ab622f.png)

*   **Base64 编码原理**

Base64 的码表只有 64 个字符， 如果要表达 64 个字符的话，使用 6 的 bit 即可完全表示(2 的 6 次方为 64)。因为 Base64 的编码只有 6 个 bit 即可表示，而正常的字符是使用 8 个 bit 表示， 8 和 6 的最小公倍数是 24，所以 4 个 Base64 字符可以表示 3 个标准的 ascll 字符；
对以某编码方式编码后的字节数组为对象，以 3 个字节为一组，按顺序排列 24bit 数据，然后以 6bit 一组分成 4 组；再在每组的最高位补 2 个 0 凑足一个字节。这时一组就有 4 个字节了。若字节数组不是 3 的倍数，那么最后一组就填充 1 到 2 个 0 字节。
然后按 Base64 编码方式（就是映射关系）对字节数组进行解码，就会得到平时看到的 Base64 编码文本。对于字节数组不是 3 的倍数，最后一组填充 1 到 2 个 0 字节的情况，填补的 0 字节对应的是=（等号）。以下为具体的解析过程案例：

把 AB 这两个字符转换为 Base64 的过程

```cpp
 ①. 对 AB 进行 ASCII 编码：得到 A(65)B(66)
 ②. 转成二进制形式：得到 A（01000001）B（01000010）
 ③. 以 3 个字节为一组，非 3 的倍数补 0 字节：010000010100001000000000
 ④. 以 6bit 为一组后高位补两个 0：(00 010000)(00 010100)(00 001000)(00 000000)
 ⑤. 转为十进制：(16)(20)(8)(0)
 ⑥. 根据映射关系解码：QUI=
```

当转换到最后， 如果不足三个字符的话，我们直接在最后添加＝号即可。

*   **图像和 base64 转换**

图片的本质就是每个像素点都是一个数字，该数字表示颜色，然后把很多很多像素点的数字拼到一起，就是图像。图像转 Base64，就是把图像的直方图所有数字转成 Base64 编码，反之，Base64 也能转换回图像。

*   **Data URI Scheme**

data URI scheme 允许我们使用内联（inline-code）的方式在网页中包含数据，目的是将一些小的数据，直接嵌入到网页中，从而不用再从外部文件载入。常用于将图片嵌入网页。

```cpp
//传统的图片 HTML 是这样用的,
<img src="http://gpeng.win/test.png" />
//Data URI 的图片内嵌式是这样用的,
<img src="data:image/gif;base64,R0lGODlhAwADAIABAL6+vv///yH5BAEAAAEALAAAAAADAAMAAAIDjA9WADs=" />
//目前，Data URI scheme 支持的类型有：
data:,                            文本数据
data:text/plain,                    文本数据
data:text/html,                  HTML 代码
data:text/html;base64,            base64 编码的 HTML 代码
data:text/css,                    CSS 代码
data:text/css;base64,              base64 编码的 CSS 代码
data:text/javascript,              Javascript 代码
data:text/javascript;base64,        base64 编码的 Javascript 代码
data:image/gif;base64,            base64 编码的 gif 图片数据
data:image/png;base64,            base64 编码的 png 图片数据
data:image/jpeg;base64,          base64 编码的 jpeg 图片数据
data:image/x-icon;base64,          base64 编码的 icon 图片数据
```

Data URI Scheme 优缺点：

优点：

1.  减少资源请求链接数。
2.  当访问外部资源很麻烦或受限时，可以很好的利用 Data URI Scheme
3.  没有跨域问题，无需考虑缓存、文件头或者 cookies 问题

缺点：

1.  Data URL 形式的图片不会被浏览器缓存，这意味着每次访问这样页面时都被下载一次，
    但可通过在 css 文件的 background-image 样式规则使用 Data URI Scheme，使其随 css 文件一同被浏览器缓存起来）。
2.  Base64 编码的数据体积通常是原数据的体积 4/3，也就是 Data URL 形式的图片会比二进制格式的图片体积大 1/3。
3.  仅适用于极小或者极简单图片，不适用于大图片。
4.  移动端性能比较低。

**3\. 图片预加载**

*   图片预加载的主要思路就是把稍后需要用到的图片悄悄的提前加载到本地，因为浏览器有缓存的原因，如果稍后用到这个 url 的图片了，浏览器会优先从本地缓存找该 url 对应的图片，如果图片没过期的话，就使用这个图片其中图片预加载也分为三种，无序加载，有序加载，基于用户行为的预加载（点击某个按钮或者滚动的时候进行加载）。
    预加载的实现很简单，其核心说到底就两句话：

```cpp
var img = new Image();  
img.src = "my_image.jpg";
```

**a. memory cache**

> MemoryCache 顾名思义，就是将资源缓存到内存中，等待下次访问时不需要重新下载资源，而直接从内存中获取。Webkit 早已支持 memoryCache。
> 目前 Webkit 资源分成两类，一类是主资源，比如 HTML 页面，或者下载项，一类是派生资源，比如 HTML 页面中内嵌的图片或者脚本链接，分别对应代码中两个类：MainResourceLoader 和 SubresourceLoader。虽然 Webkit 支持 memoryCache，但是也只是针对派生资源，它对应的类为 CachedResource，用于保存原始数据（比如 CSS，JS 等），以及解码过的图片数据。

**b. disk cache**

> diskCache 顾名思义，就是将资源缓存到磁盘中，等待下次访问时不需要重新下载资源，而直接从磁盘中获取，它的直接操作对象为 CurlCacheManager。它与 memoryCache 最大的区别在于，当退出进程时，内存中的数据会被清空，而磁盘的数据不会，所以，当下次再进入该进程时，该进程仍可以从 diskCache 中获得数据，而 memoryCache 则不行。
> diskCache 与 memoryCache 相似之处就是也只能存储一些派生类资源文件。它的存储形式为一个 index.dat 文件，记录存储数据的 url，然后再分别存储该 url 的 response 信息和 content 内容。Response 信息最大作用就是用于判断服务器上该 url 的 content 内容是否被修改。

**4\. 其他常见优化**

**a. 将图片服务和应用服务分离**
对于服务器来说,图片是比较消耗系统资源的,如果将图片服务和应用服务放在同一服务器的话,应用服务器很容易会因为图片的高 I/O 负载而崩溃,所以当网站存在大量的图片读写操作时,建议使用图片服务器。

**b. 图片懒加载**
页面加载后只让文档可视区内的图片显示，其它不显示，随着用户对页面的滚动，判断其区域位置，生成 img 标签，让到可视区的图片加载出来。jQuery 的 lazyload 插件便是一个可以实现图片延迟加载的插件，在用户触发某个条件之后再加载对应的图片资源，这对网页的打开速度有很大提升。
引入 lazyload.js，对我们想要延迟加载的图片添加 lazy 样式，用”data-original” 替换图片的引用路径

```cpp
<!-- 对 img 标签使用 -->
<img class="lazy" data-original="img/example.jpg">
<!-- 延迟加载元素的背景图 -->
<div class="lazy" data-original="img/bg.jpg">
    ...
</div>
```

在 JS 文件中调用 lazyload()方法

```cpp
$().ready(function(){
    //可任意选择你想延迟加载的目标元素，例如直接使用样式名 lazy 作为选择条件
    $("img .lazy").lazyload({
         placeholder : "img/grey.gif", //占位图
         effect: "fadeIn", // 加载效果
         threshold: 200, // 提前加载
         event: 'click',  // trigger
         container: $("#container"),  //指定容器
         failurelimit : 5 // 发生混乱时的 hack 手段
    })
})
```

**ｃ. CSS Sprites**
当网站或者 APP 有大量小 icon,要加载所有这些小 icon 将增加大量请求,这无疑将增加很多成本.
CSS Sprites 技术就是将这些小 icon 合并成一张图片,只需要加载一次,每次通过 background-position 来控制显示 icon,这样就可以节约大量请求,节约成本.

![](img/2d30f674910e038107f05672552a01c6.png)