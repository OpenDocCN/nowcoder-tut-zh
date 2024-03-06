# 第七章 第 8 节 前端-业务场景 8

> 原文：[`www.nowcoder.com/tutorial/10072/f6eab3785fd94f29a487915531debf90`](https://www.nowcoder.com/tutorial/10072/f6eab3785fd94f29a487915531debf90)

#### 14\. 1000 万 个 ip，如何做到 O(1) 查找

**参考答案**：

用 bitmap 来做这个问题。首先对数据进行预处理。定义 1000 万 bit 位个 int.在 32 位计算机下，一个 int 是 32 位，1000 万位的话，就需要 1000 万除以 32 个 int 整数。大概有很多个。第一个 int 标记 0-31 这个数字范围的 ip 存不存在，比如说 0000001 这个 ip，我就把第一个 int 的第 1 位置 1。第二个 int 能够标记 32-63 这个范围的 ip 存不存在，以此类推。把这 1000 万个 ip 号预处理一遍。然后计算你给我的这个 ip，它是在哪个 int 里面，然后找到相应的数据位，看是 1 还是 0，就能在 O(1)的时间里找到

#### 15\. 如何实现 pwa

**参考答案**：

实现：

准备工作：建议安装 [http-server]和 [ngrok]以便调试和查看。

准备一个 HTML 文件, 以及相应的 CSS 等:

```cpp
<head>
  <title>Minimal PWA</title>
  <meta name="viewport" content="width=device-width, user-scalable=no" />
  <link rel="stylesheet" type="text/css" href="main.css">
</head>
<body>
  <h3>Revision 1</h3>
  <div class="main-text">Minimal PWA, open Console for more~~~</div>
</body>
```

添加 manifest.json 文件

为了让 PWA 应用被添加到主屏幕, 使用 manifest.json 定义应用的名称, 图标等等信息。

```cpp
{
  "name": "Minimal app to try PWA",
  "short_name": "Minimal PWA",
  "display": "standalone",
  "start_url": "/",
  "theme_color": "#8888ff",
  "background_color": "#aaaaff",
  "icons": [
    {
      "src": "e.png",
      "sizes": "256x256",
      "type": "image/png"
    }
  ]
}
```

然后在 HTML 文件当中引入配置:

```cpp
<link rel="manifest" href="manifest.json" />
```

添加 Service Worker

Service Worker 在网页已经关闭的情况下还可以运行, 用来实现页面的缓存和离线, 后台通知等等功能。sw.js 文件需要在 HTML 当中引入:

```cpp
<script>
  if (navigator.serviceWorker != null) {
    navigator.serviceWorker.register('sw.js')
    .then(function(registration) {
      console.log('Registered events at scope: ', registration.scope);
    });
  }
</script>
```

后面我们会往 sw.js 文件当中添加逻辑代码。在 Service Worker 当中会用到一些全局变量:

*   self: 表示 Service Worker 作用域, 也是全局变量
*   caches: 表示缓存
*   skipWaiting: 表示强制当前处在 waiting 状态的脚本进入 activate 状态
*   clients: 表示 Service Worker 接管的页面

处理静态缓存

首先定义需要缓存的路径, 以及需要缓存的静态文件的列表, 这个列表也可以通过 Webpack 插件生成。

```cpp
var cacheStorageKey = 'minimal-pwa-1'

var cacheList = [
  '/',
  "index.html",
  "main.css",
  "e.png"
]
```

借助 Service Worker, 可以在注册完成安装 Service Worker 时, 抓取资源写入缓存:

```cpp
self.addEventListener('install', e => {
  e.waitUntil(
    caches.open(cacheStorageKey)
    .then(cache => cache.addAll(cacheList))
    .then(() => self.skipWaiting())
  )
})
```

调用 self.skipWaiting() 方法是为了在页面更新的过程当中, 新的 Service Worker 脚本能立即激活和生效。

处理动态缓存

网页抓取资源的过程中, 在 Service Worker 可以捕获到 fetch 事件, 可以编写代码决定如何响应资源的请求:

```cpp
self.addEventListener('fetch', function(e) {
  e.respondWith(
    caches.match(e.request).then(function(response) {
      if (response != null) {
        return response
      }
      return fetch(e.request.url)
    })
  )
})
```

真实的项目当中, 可以根据资源的类型, 站点的特点, 可以专门设计复杂的策略。fetch 事件当中甚至可以手动生成 Response 返回给页面。

更新静态资源

缓存的资源随着版本的更新会过期, 所以会根据缓存的字符串名称(这里变量为 cacheStorageKey, 值用了 "minimal-pwa-1")清除旧缓存, 可以遍历所有的缓存名称逐一判断决决定是否清除(备注: 简化的写法, Promise.all 中 return undefined 可能出错, 见评论):

```cpp
self.addEventListener('activate', function(e) {
  e.waitUntil(
    Promise.all(
      caches.keys().then(cacheNames => {
        return cacheNames.map(name => {
          if (name !== cacheStorageKey) {
            return caches.delete(name)
          }
        })
      })
    ).then(() => {
      return self.clients.claim()
    })
  )
})
```

在新安装的 Service Worker 中通过调用 self.clients.claim() 取得页面的控制权, 这样之后打开页面都会使用版本更新的缓存。旧的 Service Worker 脚本不再控制着页面之后会被停止。

**扩展**：

概念：

Google 提出 PWA 的时候，并没有给它一个准确的定义，经过我们的实践和总结， PWA 它不是特指某一项技术，而是应用多项技术来改善用户体验的 Web App，其核心技术包括 Web App Manifest，Service Worker，Web Push 等，用户体验才是 PWA 的核心。

PWA 主要特点如下：

*   可靠 - 即使在网络不稳定甚至断网的环境下，也能瞬间加载并展现
*   用户体验 - 快速响应，具有平滑的过渡动画及用户操作的反馈
*   用户黏性 - 和 Native App 一样，可以被添加到桌面，能接受离线通知，具有沉浸式的用户体验

PWA 的特性：

*   **渐进式** - 适用于所有浏览器，因为它是以渐进式增强作为宗旨开发的
*   **连接无关性** - 能够借助 Service Worker 在离线或者网络较差的情况下正常访问
*   **类原生应用** - 由于是在 App Shell 模型基础上开发，因此应具有 Native App 的交互，给用户 Native App 的体验
*   **持续更新** - 始终是最新的，无版本和更新问题
*   **安全** - 通过 HTTPS 协议提供服务，防止窥探，确保内容不被篡改
*   **可索引** - manifest 文件和 Service Worker 可以让搜索引擎索引到，从而将其识别为『应用』
*   **黏性** - 通过推送离线通知等，可以让用户回流
*   **可安装** - 用户可以添加常用的 Web App 到桌面，免去到应用商店下载的麻烦
*   **可链接** - 通过链接即可分享内容，无需下载安装