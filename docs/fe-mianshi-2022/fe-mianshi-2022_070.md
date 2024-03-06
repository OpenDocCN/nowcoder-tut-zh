# 第七章 第 2 节 前端-业务场景 2

> 原文：[`www.nowcoder.com/tutorial/10072/7c248f5313254da4b6b4a741c2b90072`](https://www.nowcoder.com/tutorial/10072/7c248f5313254da4b6b4a741c2b90072)

#### 2\. 懒加载

**参考答案**：

**什么是懒加载**？

懒加载是一种在页面加载时延迟加载一些非关键资源的技术，换句话说就是按需加载。对于图片来说，非关键通常意味着离屏。
我们之前看到的懒加载一般是这样的形式：

1.  浏览一个网页，准备往下拖动滚动条
2.  拖动一个占位图片到视窗
3.  占位图片被瞬间替换成最终的图片

网页首先用一张轻量级的图片占位，当占位图片被拖动到视窗，瞬间加载目标图片，然后替换占位图片。

**为什么要懒加载而不直接加载？**

*   浪费流量。在不计流量收费的网络，这可能不重要；在按流量收费的网络中，毫无疑问，一次性加载大量图片就是在浪费用户的钱。
*   消耗额外的电量和其他的系统资源，并且延长了浏览器解析的时间。因为媒体资源在被下载完成后，浏览器必须对它进行解码，然后渲染在视窗上，这些操作都需要一定的时间。

懒加载图片和视频，可以减少页面加载的时间、页面的大小和降低系统资源的占用，这些对于性能都有显著地提升。

**懒加载图片**

图片懒加载在技术上实现很简单，不过对于细节要求比较严格。目前有很多实现懒加载的方法，先从懒加载内联图片说起吧。

内联图片

最常见的懒加载方式就是利用标签。懒加载图片时，我们利用 JavaScript 检查标签是否在视窗中。如果在的 src（有时候是 srcset）就会设置为目标图片的 url。

利用 intersection observer

如果你之前用过懒加载，你很可能是通过监听一些事件比如`scroll`或者`resize`来检测元素出现在视窗，这种方法很成熟，能够兼容大部分的浏览器。但是，现代浏览器提供了一个更好的方法给我们 Intersection observer

> *注意：Intersection observer 目前只能在 Chrome63+和 firefox58+使用*

比起事件监听，Intersection observer 用起来比较简单，可阅读性也大大提高。开发者只需要注册一个 observer 去监控元素而不是写一大堆乱七八糟的视窗检测代码。注册 observer 之后我们只需要做的就是当元素可见时改变它的行为。举个例子吧：

```cpp
<img class="lazy" src="placeholder-image.jpg" data-src="image-to-lazy-load-1x.jpg" data-srcset="image-to-lazy-load-2x.jpg 2x, image-to-lazy-load-1x.jpg 1x" alt="I'm an image!">
```

需要注意三个相关的属性

*   class：用于在 JavaScript 中关联元素
*   src 属性：指向了一张占位图片，图片在页面的第一次加载会显现
*   data-src 和 data-srcset 属性：这是占位属性，里面放的是目标图片的 url

ok，看一下怎么在 JavaScript 中使用 Intersection observer 吧：

```cpp
document.addEventListener("DOMContentLoaded", function() {
  var lazyimages = [].slice.call(document.querySelectorAll("img.lazy"));

  if ("IntersectionObserver" in window) {
    let lazyImageObserver = new IntersectionObserver(function(entries, observer) {
      entries.forEach(function(entry) {
        if (entry.isIntersecting) {
          let lazyImage = entry.target;
          lazyImage.src = lazyImage.dataset.src;
          lazyImage.srcset = lazyImage.dataset.srcset;
          lazyImage.classList.remove("lazy");
          lazyImageObserver.unobserve(lazyImage);
        }
      });
    });

    lazyimages.forEach(function(lazyImage) {
      lazyImageObserver.observe(lazyImage);
    });
  } else {
    // Possibly fall back to a more compatible method here
  }
});
```

当`DOMContentLoaded`触发后，js 会查询 class 为`lazy`的 img 元素。然后我们检测浏览器支不支持 intersection observer，如果可以用，先创建一个 observer，然后传入回调函数，回调函数将会在元素可见性变化时被调用。
最后比较麻烦的是处理兼容性，在不支持 intersection observer 的浏览器，你需要引入 polyfill，或者回退到更安全的方法。

利用事件

当你选择使用 intersection observer 来实现懒加载时，你要考虑它的兼容性，当然你可以使用 polyfill，实际上这也非常简单。事实上你也可以针对低版本的浏览器使用事件来完成更安全地回退。你可以使用`scroll`、`resize`和`orientationchange`事件，再配合`getBoundingClientRect`API 就可以实现懒加载了。
和上面一样的例子，现在 JavaScript 程序变成了这样：

```cpp
document.addEventListener("DOMContentLoaded", function() {
  let lazyimages = [].slice.call(document.querySelectorAll("img.lazy"));
  let active = false;

  const lazyLoad = function() {
    if (active === false) {
      active = true;

      setTimeout(function() {
        lazyimages.forEach(function(lazyImage) {
          if ((lazyImage.getBoundingClientRect().top <= window.innerHeight && lazyImage.getBoundingClientRect().bottom >= 0) && getComputedStyle(lazyImage).display !== "none") {
            lazyImage.src = lazyImage.dataset.src;
            lazyImage.srcset = lazyImage.dataset.srcset;
            lazyImage.classList.remove("lazy");

            lazyimages = lazyimages.filter(function(image) {
              return image !== lazyImage;
            });

            if (lazyimages.length === 0) {
              document.removeEventListener("scroll", lazyLoad);
              window.removeEventListener("resize", lazyLoad);
              window.removeEventListener("orientationchange", lazyLoad);
            }
          }
        });

        active = false;
      }, 200);
    }
  };

  document.addEventListener("scroll", lazyLoad);
  window.addEventListener("resize", lazyLoad);
  window.addEventListener("orientationchange", lazyLoad);
});
```

上面的代码用了`getBoundingClientRect`，在 scroll 事件中检测 img 是否在视窗。`setTimeout`用于延迟执行操作，`active`变量代表了处理状态防止同时响应。当图片被懒加载完成后，事件处理程序将被移除，尽管上面这段代码可以在绝大部分的浏览器上运行，但存在显著的性能损耗。在此示例中，无论在视口中是否存在图像，文档滚动或窗口大小调整时都会每 200 毫秒执行一次检查。 另外，跟踪有多少元素留给延迟加载和解除事件处理程序的繁琐工作也留给了开发者。

> *建议：尽可能使用 intersection observer，如果应用要求兼容低版本的浏览器才考虑利用事件*

CSS 图像

展示图像不是标签的特权，CSS 利用`background-image`也可以做到。相比较而言，CSS 加载图片比较容易控制。当文档对象模型、CSS 对象模型和渲染树被构造完成后，开始请求外部资源之前，浏览器会检测 CSS 规则是怎么应用到 DOM 上的。如果浏览器检测到 CSS 引用的外部资源并没有应用到已存在的 DOM 节点上，浏览器就不会请求这些资源。
这个行为可用于延迟 CSS 图片资源的加载，思路是通过 JavaScript 检测到元素处于视窗中时，加一个 class 类名，这个 class 就引用了外部图片资源。
这可以实现图片按需加载而不是一次性全部加载。给个例子：

```cpp
<div class="lazy-background">
  <h1>Here's a hero heading to get your attention!</h1>
  <p>Here's hero copy to convince you to buy a thing!</p>
  <a href="/buy-a-thing">Buy a thing!</a>
</div>
```

这个`div.lazy-background`元素会正常地显示 CSS 规则加载的占位图片。当元素处于可见状态时，我们可以添加一个类名完成懒加载：

```cpp
.lazy-background {
  background-image: url("hero-placeholder.jpg"); /* 占位图片 */
}

.lazy-background.visible {
  background-image: url("hero.jpg"); /* 真正的图片 */
}
```

下面是利用 JavaScript 去检测元素是否处于视窗（intersection observer），如果可见就为它加上一个 visible 的类名。

```cpp
document.addEventListener("DOMContentLoaded", function() {
  var lazyBackgrounds = [].slice.call(document.querySelectorAll(".lazy-background"));

  if ("IntersectionObserver" in window) {
    let lazyBackgroundObserver = new IntersectionObserver(function(entries, observer) {
      entries.forEach(function(entry) {
        if (entry.isIntersecting) {
          entry.target.classList.add("visible");
          lazyBackgroundObserver.unobserve(entry.target);
        }
      });
    });

    lazyBackgrounds.forEach(function(lazyBackground) {
      lazyBackgroundObserver.observe(lazyBackground);
    });
  }
});
```

**懒加载视频**

就像图片一样，我们同样可以懒加载视频，播放视频会用到``标签。如何懒加载视频取决于特定的场景，先来讨论几个需要不同解决方案的场景。

视频不需要自动播放

```cpp
<video controls preload="none" poster="one-does-not-simply-placeholder.jpg">
  <source src="one-does-not-simply.webm" type="video/webm">
  <source src="one-does-not-simply.mp4" type="video/mp4">
</video>
```

我们还需要添加一个 poster 属性给`preload`标签，这相当于一个占位符。preload 属性则规定是否在页面加载后载入视频。鉴于浏览器之间的 preload 默认值差异，显式定义会更具兼容性。在这种情况下，当用户点击播放视频时，视频才会被加载，预加载视频简单地实现了。不幸的是，当我们想用视频替代 GIF 动画时，这个方法就行不通了。

用视频模拟 GIF

GIF 在很多地方都不及视频，特别是文件大小方面。在相同质量下，视频的尺寸通常会比 GIF 文件小得多。因为 GIF 图片有三种要注意的行为：

1.  加载完后自动播放
2.  不停地循环播放
3.  没有音轨，要实现这些，HTML 是这样的：

```cpp
<video autoplay muted loop playsinline>
  <source src="one-does-not-simply.webm" type="video/webm">
  <source src="one-does-not-simply.mp4" type="video/mp4">
</video>
```

autoplay、muted 和 loop 的作用是为了实现上述三个功能，playsinline 是为了兼容 IOS 的 autoplay。现在我们已经有了一个跨平台的视频模版用于取代 GIF 图片了。接下来怎么进行懒加载呢？Chrome 会帮我们自动完成这项工作，但你不能保证所有浏览器都能做到这个。

```cpp
<video autoplay muted loop playsinline width="610" height="254" poster="one-does-not-simply.jpg">
  <source data-src="one-does-not-simply.webm" type="video/webm">
  <source data-src="one-does-not-simply.mp4" type="video/mp4">
</video>
```

注意到了吗？有一个奇怪的 poster 属性。这个属性其实是一个占位符，在被懒加载之前，poster 里面指定的内容会在标签中显现

```cpp
document.addEventListener("DOMContentLoaded", function() {
  var lazyVideos = [].slice.call(document.querySelectorAll("video.lazy"));

  if ("IntersectionObserver" in window) {
    var lazyVideoObserver = new IntersectionObserver(function(entries, observer) {
      entries.forEach(function(video) {
        if (video.isIntersecting) {
          for (var source in video.target.children) {
            var videoSource = video.target.children[source];
            if (typeof videoSource.tagName === "string" && videoSource.tagName === "SOURCE") {
              videoSource.src = videoSource.dataset.src;
            }
          }

          video.target.load();
          video.target.classList.remove("lazy");
          lazyVideoObserver.unobserve(video.target);
        }
      });
    });

    lazyVideos.forEach(function(lazyVideo) {
      lazyVideoObserver.observe(lazyVideo);
    });
  }
});
```

当懒加载一个视频的时，首先要迭代标签里面的每一个，然后将 data-src 中的 url 分配给 src 属性。然后调用元素的 load 方法，现在视频就可以自动播放了。
通过这个方法，我们有了一个模拟 GIF 动画的视频解决方案，不会消耗带宽加载不必要的媒体资源，而且还能实现懒加载。

**懒加载库**

如果你不关心懒加载背后是如何实现的，你只是想找一个库去实现这个功能，可供选择的有：

*   lazysizes 是一个功能十分强大的懒加载库，主要用于加载图片和 iframes。你只需要指定 data-src/data-srcset 属性，lazysizes 会帮你自动懒加载内容。值得注意的是，lazysizes 基于 intersection observer，因此你需要一个 polyfill。你还可以通过一些插件扩展库的功能以用于懒加载视频。
*   lozad.js 是一个轻量级、高性能的懒加载库，基于 intersection observer，你同样需要提供一个相关的 polyfill。
*   blazy 是一个轻量级的懒加载库，大小仅为 1.4KB。相对于 lazysizes，它不需要任何的外部依赖，并且兼容 IE7+。你可能猜测到了，blazy 不支持 intersection observer，性能相对较差。
*   yall.js 是作者本人写的一个懒加载库，基于 IntersectionObserver 和事件，兼容 IE11 和大部分的浏览器。
*   如果你想寻找一个基于 React 的懒加载工具，react-lazyload 可能是你的选择。

上述每个懒加载库的文档都写得很好，同时提供了大量的标记模式。如果你不想深究懒加载的技术细节，就选择任意一个去使用，这能节省你很多的时间和功夫。