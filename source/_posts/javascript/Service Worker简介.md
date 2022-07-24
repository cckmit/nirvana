---
title: Service Worker简介
date: 2022-04-24 16:28:03
categories: 前端
---
### Service Worker的由来

W3C 组织早在 2014 年 5 月就提出过 Service Worker 这样的一个 HTML5 API ，主要用来做持久的离线缓存。service worker是浏览器的一个高级特性，本质是一个web worker，`是独立于网页运行的脚本`。 web worker这个api被造出来时，就是为了`解放主线程`。因为，浏览器中的JavaScript都是运行在单一个线程上，随着web业务变得越来越复杂，js中耗时间、耗资源的运算过程则会导致各种程度的性能问题。 而web worker由于独立于主线程，则可以将一些复杂的逻辑交由它来去做，完成后再通过postMessage的方法告诉主线程。 service worker则是web worker的升级版本，相较于后者，前者拥有了持久离线缓存的能力。

### Service Worker的特点

- 独立于主线程、在后台运行的脚本
- 被install后就永远存在，除非被手动卸载
- 必须是https的协议才能使用。不过在本地调试时，在```http://localhost``` 和```http://127.0.0.1``` 下也是可以跑起来的。
- 不能直接操纵dom：因为sw是个独立于网页运行的脚本。
- 可拦截请求和返回，缓存文件。sw可以通过fetch这个api，来拦截网络和处理网络请求，再配合cacheStorage来实现web页面的缓存管理以及与前端postMessage通信。

### Service Worker的兼容性

下图是Service worker现有的浏览器支持版本， 从图上可以看出火狐和谷歌的支持是比较良好的，IE和safari需要相对比较高的版本才能够支持。移动端的话ios也需要从ios13才开始支持在安卓上的支持会相对广泛一点。
![](https://upload-images.jianshu.io/upload_images/10024246-2a769b1724e822b5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### Service Worker的工作流程

![](https://upload-images.jianshu.io/upload_images/10024246-4aaf1b3468a02314.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* Service Worker 文件只在首次注册的时候执行了一次。
* 安装、激活流程也只是在首次执行 Service Worker 文件的时候进行了一次。
* 首次注册成功的 Service Worker 不能拦截当前页面的请求。
* 非首次注册的 Service Worker 可以控制当前的页面并能拦截请求
* Service Worker 首次注册或者有新版本触发更新的时候，才会重新创建一个 worker 工作线程并解析执行 Service Worker 文件，在这之后并进入 Service Worker 的安装和激活生命周期

### Service Worker的生命周期

当一个servicework被注册成功后，它将开始它的生命周期，我们对servicework的操作一般都是在其生命周期里面进行的。servicework的生命周期分为这么几个状态 安装中, 安装后, 激活中, 激活后, 废弃。

* **安装( installing )**：这个状态发生在 Service Worker 注册之后，表示开始安装，这个状态会触发 install 事件，一般会在install事件的回调里面进行静态资源的离线缓存， 如果这些静态资源缓存失败了，那 Service Worker 安装就会失败，生命周期终止。

* **安装后( installed )**：当成功捕获缓存到的资源时，servicework会变为这个状态，当此时没有其他的servicework线程在工作时，会立即进入激活状态，如果此时有正在工作的servicework工作线程，则会等待其他的 Service Worker 线程被关闭后才会被激活。可以使用 self.skipWaiting() 方法强制正在等待的servicework工作线程进入激活状态。

* **激活( activating )**：在这个状态下会触发activate事件，在activate 事件的回调中去清理旧版缓存。

* **激活后( activated )**：在这个状态下，servicework会取得对整个页面的控制

* **废弃状态 ( redundant )**：这个状态表示一个 Service Worker 的生命周期结束。新版本的 Service Worker 替换了旧版本的 Service Worker会出现这个状态

### 更新Service Worker

更新一个servicework，最直接的办法就是修改servicework.js这个文件，当刷新浏览器时，浏览器尝试重新下载servicework.js脚本文件，然后会与之前的版本比对，一旦发现文件内容不一致，就会进入更新流程。

* 新的 servicework 被启动安装并触发 install事件。

* 安装成功后，新版 servicework 进入等待状态，此时页面的控制权还在老版 servicework手中。

* 当servicework控制的所有终端都关闭之后，或者手动self.skipWaiting()，旧的 servicework 才能被终止，此时新的servicework被激活，触发activate 事件。

* 用户再次访问页面，或刷新页面，新的 service work 启动控制页面。

### Service Worker 的简单实践

1. 注册。 serviceWorker对象存在于**navigator**对象下，可以再主线程中调用```navigator.serviceWorker.register()```方法来注册servicework,register 方法接受两个参数,第一个参数表示servicework.js相对于**origin**的路径，第二个参数是 Serivce Worker 的配置项，可选填，其中比较重要的是 scope 属性，用来指定你想让 service worker 控制的内容的目录。 默认值为servicework.js所在的目录。这个属性所表示的路径不能在 service worker 文件的路径之上，默认是 Serivce Worker 文件所在的目录。 成功注册或返回一个promise。

```
// 页面的入口文件

if (navigator.serviceWorker) {
  window.addEventListener('load', () => {
    console.log('开始注册ServiceWorker')
    navigator.serviceWorker
      .register('./serviceworker.js')
      .then((reg) => {
        console.log('ServiceWorker register success: ', reg)
      })
      .catch((err) => {
        console.log('ServiceWorker register failed: ', err)
      })
  })
}

```
2. 安装 

```
self.addEventListener('install', (event) => {
  console.log('install事件')
  self.skipWaiting() //用来强制更新的servicework跳过等待时间
  event.waitUntil(
    caches.open(CACHE_NAME).then(function (cache) {
      return cache.addAll(urlsToCache)
    })
  )
})
```
首先 ``self.skipWaiting()`` 执行，告知浏览器直接跳过等待阶段，淘汰过期的Service Worker脚本，直接开始尝试激活新的Service Worker。然后使用 ``caches.open`` 打开一个Cache，打开后，通过``cache.addAll``尝试缓存我们预先声明的文件。 CacheStorage 全局的cache Api 并非只有在sw中才能用 浏览器控制台直接用也是可以的，所以是挂在window下的

``event.waitUntil()`` 只能在 Service Worker 的 install 或者 activate 事件中使用；看起来像是一个 callback，用来延长事件的作用时间,但是，即便你不使用它，程序也可能正常运行。如果你传递了一个 Promise 给它，那么只有当该 Promise resolved 时，Service Worker 才会完成 install；如果 Promise rejected 掉，那么整个 Service Worker 便会被废弃掉。因此，cache.addAll 里面，只要有一个资源获取失败，整个 Service Worker 便会失效。

在 install 事件回调被调用时，它把即将被激活的 worker 线程状态延迟为 installing 状态，直到传递的 Promise 被成功地 resolve。这主要用于确保：Service Worker 工作线程在所有依赖的核心 cache 被缓存之前都不会被激活。

在 activate 事件回调被调用时，它把即将被激活的 worker 线程状态延迟为 activating 状态，直到传递的 Promise 被成功地 resolve。这主要用于确保：任何功能事件不会被分派到 ServiceWorkerGlobalScope 对象，直到它删除过期的缓存条目。

当 waitUntil()运行时，如果 Promise 是 rejected 那么installing 或者 activating 的状态会被设置为 redundant。

3. 激活
```
self.addEventListener('activate', (event) => {
  console.log('activate事件')
  var cacheWhitelist = [CACHE_NAME]
  self.clients.claim() // 保证 激活之后能够马上作用于所有的终端
  event.waitUntil(
    caches.keys().then((cacheNames) => {
      return Promise.all(
        cacheNames.map((cacheName) => {
          if (cacheWhitelist.indexOf(cacheName) === -1) {
            return caches.delete(cacheName)
          }
        })
      )
    })
  )
})
```
在激活servicework时需要删除之前的缓存，可将需要的缓存放在有个白名单中，然后通过caches.keys()拿到所有缓存，将不再白名单中的缓存删掉。

4. 拦截网络请求
```
  self.addEventListener('fetch', (event) => {
    event.respondWith(
      caches.match(event.request).then((response) => {
        if (response) 
          return response;
        }
        return fetch(event.request);
      })
    );
  });
```
通过监听servicework的 **fetch** 事件来拦截网络请求，调用 event 上的 **respondWith()** 方法来劫持当前servicework控制域下的 HTTP 请求，该方法会直接返回一个Promise 结果 ，这个结果就会是http请求的响应。上面代码中就一个简单的逻辑，先劫持http请求，然后看看缓存中是否有这个请求的资源，如果有则直接返回，如果没有就去请求服务器上的资源。 **event.respondWith** 方法只能在 Service Worker 的 fetch 事件中使用。

Cache Stroage 只能缓存静态资源，所以它只能缓存用户的 GET 请求；Cache Stroage 中的缓存不会过期，但是浏览器对它的大小是有限制的，所以需要我们定期进行清理。
对应post 请求我们也可以通过 fetch方法拦截到，来进行一些自定义的返回。

5. service worker 与主线程之间的通信
 
  * 主线程 
  ```
    // 传递
    navigator.serviceWorker.controller && navigator.serviceWorker.controller.postMessage("this message is from page");

    // 接收
    navigator.serviceWorker.addEventListener('message', function (e) {
      console.log('service worker传递的信息',e.data); 
    });
  ```
  * service worker 

  ```
  self.addEventListener('message', (event)=>{
    console.log('页面传递过来的数据',event.data)  // 收到主线程传递的信息
    event.source.postMessage('this message is from sw.js to page');  // 向主线程传递信息
  })

  ```

6. service worker 卸载
```
  navigator.serviceWorker.getRegistrations().then(function (registrations) {
    for (let registration of registrations) {
      //安装在网页的service worker不止一个，找到我们的那个并删除
      console.log(registration)
      if (registration && registration.scope === 'http://localhost:8080/') {
        registration.unregister()
      }
    }
  })
```

7. serviceworker 和 http缓存

 - 在请求速度方面： 

![](https://upload-images.jianshu.io/upload_images/10024246-ad2dd76b0a507940.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 在缓存文件更新方面

  * 如果是http缓存，一刷新页面就可以拿到最新的资源

  * 如果是sw缓存， 一刷新页面，会返回当前缓存中的资源（不是最新），然后请求sw.js文件发现更新后重新进入sw生命周期，重新去更新缓存，当你再次刷新时才能拿到最新资源。所以在缓存资源更新时，sw会延迟一次刷新才能获取最新资源

- 在缓存控制方面：

  	http一般是由服务器端控制的，而sw则是可以前端自己控制，可以更好地控制缓存。

  协商缓存返回状态码304， 强缓存返回的是200， 在这点上server worker和强缓存比较类似的返回也是200， 会对请求进行拦截，不会真是的发出请求。


### 其他

会有一些开源的框架对 service worker进行了一些封装，避免了我们重复繁琐的去写一下fetch监听，install事件，active事件等，大大简化了繁琐的写法。关注度比较高的应该是谷歌推出的 [workbox](!https://fed.taobao.org/blog/taofed/do71ct/workbox3/), 围绕他也有一系列的工具，如 ```workbox-cli、gulp-workbox、webpack-workbox-plagin``` 等等。

workbox提供了一下几种缓存策略： 

* **Stale-While-Revalidate** 当请求的路由有对应的 Cache 缓存结果就直接返回，在返回 Cache 缓存结果的同时会在后台发起网络请求拿到请求结果并更新 Cache 缓存，如果本来就没有 Cache 缓存的话，直接就发起网络请求并返回结果 **（ 从缓存中读取资源的同时发送网络请求更新本地缓存 ）**

* **CacheFirst** 当匹配到请求之后直接从 Cache 缓存中取得结果，如果 Cache 缓存中没有结果，那就会发起网络请求，拿到网络请求结果并将结果更新至 Cache 缓存，并将结果返回给客户端。这种策略比较适合结果不怎么变动且对实时性要求不高的请求。 **（有缓存用缓存，无缓存则请求网络）**

* **CacheOnly** 这个策略也比较直接，直接使用 Cache 缓存的结果，并将结果返回给客户端，这种策略比较适合一上线就不会变的静态资源请求。 **（仅使用缓存）**

* **NetworkFirst** 采用网络优先的策略，也就是优先尝试拿到网络请求的返回结果，如果拿到网络请求的结果，就将结果返回给客户端并且写入 Cache 缓存，如果网络请求失败，那最后被缓存的 Cache 缓存结果就会被返回到客户端，这种策略一般适用于返回结果不太固定或对实时性有要求的请求，为网络请求失败进行兜底。 **（有网的情况下采取网络，没网的情况下用缓存）**

* **NetworkOnly** 比较直接的策略，直接强制使用正常的网络请求，并将结果返回给客户端，这种策略比较适合对实时性要求非常高的请求。 **（仅使用网络请求）**

### 总结

本次只是对于service worker 的一些基础API的尝试和学习，可以当做是一些学习资料的整理， 希望有不对的地方可以指出，service worker的知识点还有很多，有比较深入了解的同学也欢迎补充