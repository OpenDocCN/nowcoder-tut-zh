# 第七章 第 7 节 前端-业务场景 7

> 原文：[`www.nowcoder.com/tutorial/10072/e9b02370a258412eac83403b62fd8779`](https://www.nowcoder.com/tutorial/10072/e9b02370a258412eac83403b62fd8779)

#### 9\. 扫描二维码登录的原理

**参考答案**：

1.  **什么是二维码**

> 二维码又称二维条码，常见的二维码为 QR Code，QR 全称 Quick Response，是一个近几年来移动设备上超流行的一种编码方式，它比传统的 Bar Code 条形码能存更多的信息，也能表示更多的数据类型。----来自百度百科

在商品上，一般都会有条形码，条形码也称为一维码，条形码只能表示一串数字。二维码要比条形码丰富很多，可以存储数字、字符串、图片、文件等，比如我们可以把 `www.baidu.com` 存储在二维码中，扫码二维码我们就可以获取到百度的地址。

可能用文字说起来还是比较难理解，您可以百度：草料二维码，一款二维码生成和解析工具，玩一玩你就知道二维码是个啥了。

2.  **移动端基于 token 的认证机制**

在了解扫码登录原理之前，有必要先了解移动端基于 token 的认证机制，对理解扫码登录原理还是非常有帮助的。基于 token 的认证机制跟我们常用的账号密码认证方式有较大的不同，安全系数比账号密码要高，如果每次验证都传入账号密码，那么被劫持的概率就变大了。

基于 token 的认证机制流程图，如下图所示：

![](img/f71b55243f7dcd589a3da37e80a99e44.png)

基于 token 的认证机制，只有在第一次使用需要输入账号密码，后续使用将不在输入账号密码。**其实在登陆的时候不仅传入账号、密码，还传入了手机的设备信息。在服务端验证账号、密码正确后，服务端会做两件事**。

第一，将账号与设备关联起来，在某种意义上，设备信息就代表着账号。

第二，生成一个 token 令牌，并且在 token 与账号、设备关联，类似于 key/value，token 作为 key ，账号、设备信息作为 value，持久化在磁盘上。

将 token 返回给移动端，移动端将 token 存入在本地，往后移动端都通过 token 访问服务端 API ，当然除了 token 之外，还需要携带设备信息，因为 token 可能会被劫持。带上设备信息之后，就算 token 被劫持也没有关系，因为设备信息是唯一的。

这就是基于 token 的认证机制，将账号密码换成了 token、设备信息，从而提高了安全系数，可别小看这个 token ，token 是身份凭证，在扫码登录的时候也会用到。

3.  **二维码扫码登录的原理**

好了，知道了移动端基于 token 的认证机制后，接下来就进入我们的主题：二维码扫码登陆的原理。先上二维码扫码登录的流程图：

![](img/2468de79d2c8499bbb6c079882c7f243.png)

扫码登录可以分为三个阶段：**待扫描、已扫描待确认、已确认**。我们就一一来看看这三个阶段。

**1、待扫描阶段**

待扫描阶段也就是流程图中 1~5 阶段，即生成二维码阶段，这个阶段跟移动端没有关系，是 PC 端跟服务端的交互过程。

首先 PC 端携带设备信息想服务端发起生成二维码请求，服务端会生成唯一的二维码 ID，你可以理解为 UUID，并且将 二维码 ID 跟 PC 设备信息关联起来，这跟移动端登录有点相似。

PC 端接受到二维码 ID 之后，将二维码 ID 以二维码的形式展示，等待移动端扫码。此时在 PC 端会启动一个定时器，轮询查询二维码的状态。**如果移动端未扫描的话，那么一段时间后二维码将会失效。**

**2、已扫描待确认阶段**

流程图中第 6 ~ 10 阶段，我们在 PC 端登录微信时，手机扫码后，PC 端的二维码会变成已扫码，请在手机端确认。这个阶段是移动端跟服务端交互的过程。

首先移动端扫描二维码，获取二维码 ID，**然后将手机端登录的信息凭证（token）和 二维码 ID 作为参数发送给服务端**，此时的手机一定是登录的，不存在没登录的情况。

服务端接受请求后，会将 token 与二维码 ID 关联，为什么需要关联呢？你想想，我们使用微信时，移动端退出， PC 端是不是也需要退出，这个关联就有点把子作用了。然后会**生成一个一次性 token，这个 token 会返回给移动端，一次性 token 用作确认时候的凭证**。

PC 端的定时器，会轮询到二维码的状态已经发生变化，会将 PC 端的二维码更新为已扫描，请确认。

**3、已确认**

流程图中的 第 11 ~ 15 步骤，这是扫码登录的最后阶段，移动端携带上一步骤中获取的临时 token ，确认登录，**服务端校对完成后，会更新二维码状态，并且给 PC 端生成一个正式的 token ，后续 PC 端就是持有这个 token 访问服务端**。

PC 端的定时器，轮询到了二维码状态为登录状态，并且会获取到了生成的 token ，完成登录，后续访问都基于 token 完成。

在服务器端会跟手机端一样，维护着 token 跟二维码、PC 设备信息、账号等信息。

到此，二维码扫描登录原理就差不多了，二维码扫描登录在原理上不难理解，跟 OAuth2.0 有一丝的相似之处，但是实现起来可能就比较复杂。

#### 10\. 页面资源预加载

**参考答案**：

preload 提供了一种声明式的命令，让浏览器提前加载指定资源(加载后并不执行)，在需要执行的时候再执行。提供的好处主要是

*   将加载和执行分离开，可不阻塞渲染和 document 的 onload 事件
*   提前加载指定资源，不再出现依赖的 font 字体隔了一段时间才刷出

**如何使用 preload**

使用 link 标签创建

```cpp
<!-- 使用 link 标签静态标记需要预加载的资源 -->
<link rel="preload" href="/path/to/style.css" as="style">

<!-- 或使用脚本动态创建一个 link 标签后插入到 head 头部 -->
<script>
const link = document.createElement('link');
link.rel = 'preload';
link.as = 'style';
link.href = '/path/to/style.css';
document.head.appendChild(link);
</script>
```

使用 HTTP 响应头的 Link 字段创建

```cpp
Link: <https://example.com/other/styles.css>; rel=preload; as=style
```

如我们常用到的 antd 会依赖一个 CDN 上的 font.js 字体文件，我们可以设置为提前加载，以及有一些模块虽然是按需异步加载，但在某些场景下知道其必定会加载的，则可以设置 preload 进行预加载，如：

```cpp
<link rel="preload" as="font"   href="https://at.alicdn.com/t/font_zck90zmlh7hf47vi.woff">
<link rel="preload" as="script" href="https://a.xxx.com/xxx/PcCommon.js">
<link rel="preload" as="script" href="https://a.xxx.com/xxx/TabsPc.js">
```

#### 11\. 前端切换中英文

**参考答案**：

**方法 1：（中英文各做一份，然后用不同的文件夹区分开来，点击切换语言时，链接跳转到不同文件夹就行了）**

　　　　 　**优点：**各自的版本是分离开来的，比较稳定，不会出现互相干扰（共用数据库资料的除外）

　　　　 　**缺点：**修改一个样式或功能，要把变更的操作（代码逻辑、更换图片、修改样式等）在所有的语言版 本上重复一次，加重了工作量

　　　 　**场景：**个人认为符合下面 2 种场景可以考虑使用这种方法

　　　 　 **注： **如果切换的语言版本很少，并且本身网站不复杂（比如电商网站不推荐）

　　　　　　 　　整体内容相对固定，布局比较简洁，扁平化，改动不会太频繁的（比如新闻类网站不推荐）

**方法 2：借助 jquery 插件**——jquery.i18n.properties

**方法 3：使用微软字典整站翻译**

#### 12\. 贪吃蛇是怎么完成碰撞触发的

**参考答案**：

**实现碰撞:**

1.  蛇头撞到墙,需要判断蛇头的坐标是否和墙的坐标重合
2.  撞到自己的身体，需要判断蛇头的坐标和蛇的某一节坐标是否重合。

所以在蛇移动之后我们用蛇头的坐标去遍历蛇自己的所有坐标并且判断蛇的 X 或者 Y 是否大于或者小于地图边界了，这样就能知道是否发生碰撞，发生碰撞之后直接 break 不在刷新视图就好。

**贪吃蛇实现思路：**

1.  需要一张地图，中间是空的四周有墙体。

2.  需要一条蛇，这条蛇由蛇头和蛇身组成。

3.  需要食物，并且在蛇吃掉食物之后将蛇的身体变长，而且重新生成一个食物。

4.  蛇需要移动，这应该是最难实现的。

5.  蛇撞到墙或者撞到自己的身体就会死亡。

6.  需要能用键盘控制蛇的运动方向，这个会和蛇的移动有一些联系。

#### 13\. `compositionStart`，`compositionEnd`处理中文输入

**参考答案**：

**问题**：

在 Web 开发中，经常要对表单元素的输入进行限制，比如说不允许输入特殊字符，标点。通常我们会监听 input 事件:

```cpp
inputElement.addEventListener('input', function(event) {
  let regex = /[¹-9a-zA-Z]/g;
  event.target.value = event.target.value.replace(regex, '');
  event.returnValue = false
});
```

这段代码在 Android 上是没有问题的，但是在 iOS 中，input 事件会截断非直接输入，什么是非直接输入呢，在我们输入汉字的时候，比如说「喜茶」，中间过程中会输入拼音，每次输入一个字母都会触发 input 事件，然而在没有点选候选字或者点击「选定」按钮前，都属于非直接输入。

**解决：**

这显然不是我们想要的结果，我们希望在直接输入之后才触发 input 事件，这就需要引出我要说的两个事件—— `compositionstart`和`compositionend`。

`compositionstart` 事件在用户开始进行非直接输入的时候触发，而在非直接输入结束，也即用户点选候选词或者点击「选定」按钮之后，会触发 `compositionend` 事件。

```cpp
var inputLock = false;
function do(inputElement) {
    var regex = /[¹-9a-zA-Z]/g;
    inputElement.value = inputElement.value.replace(regex, '');
}
inputElement.addEventListener('compositionstart', function() {
  inputLock = true;
});
inputElement.addEventListener('compositionend', function(event) {
  inputLock = false;
  do(event.target);
})
inputElement.addEventListener('input', function(event) {
  if (!inputLock) {
    do(event.target);
    event.returnValue = false;
  }
});
```

添加一个 inputLock 变量，当用户未完成直接输入前，inputLock 为 true，不触发 input 事件中的逻辑，当用户完成有效输入之后，inputLock 设置为 false，触发 input 事件的逻辑。这里需要注意的一点是，compositionend 事件是在 input 事件后触发的，所以在 compositionend 事件触发时，也要调用 input 事件处理逻辑