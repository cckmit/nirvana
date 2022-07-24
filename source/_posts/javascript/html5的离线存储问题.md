---
title: html5的离线存储问题
date: 2021-03-11 21:12:40
categories: 前端
---

HTML5的一个重要特性就是离线存储，所谓的离线存储就是将一些资源文件保存在本地，这样后续的页面重新加载将使用本地资源文件，在离线情况下可以继续访问web应用，同时通过一定的手法（更新相关文件或者使用相关API），可以更新、删除离线存储等操作；

HTML5的离线存储使用一个manifest文件来标明哪些文件是需要被存储的，使用如 <html manifest="offline.manifest"> 来引入一个manifest文件，这个文件的路径可以是相对的，也可以是绝对的，如果你的web应用很多，而且希望能集中管理manifest文件，那么静态文件服务器是个不错的选择。

对于manifest文件，要求：文件的mime-type必须是 text/cache-manifest类型。如果你是JAVA工程，在你的web.xml中配置请求后缀为manifest的格式：

```
<mime-mapping>
<extension>manifest</extension>
<mime-type>text/cache-manifest</mime-type>
</mime-mapping>  
```

这样可以控制请求到的manifest文件格式为text/cache-manifest的。

### manifest文件的格式：

>CACHE MANIFEST# 这一句必须存在，而且必须放在头部# 指明缓存入口CACHE:index.htmlstyle.cssimages/logo.pngscripts/main.js# 以下资源必须在线访问NETWORK:login.php# 如果index.php无法访问则用404.html代替FALLBACK:/index.php /404.html

其中 CACHE 不是必须存在的，可以直接在 CACHE MANIFEST 行之下直接写需要缓存的文件，在这里指明的文件将被缓存到浏览器本地。在NETWORK之下指明的文件，是强制必须通过网络资源获取的，在FALLBACK下指明的是一种失败的回调方案，比如上述index.php无法访问，那么就发出404.htm请求

这样几步就可以完成对离线存储的支持。接下来要思考的，是如何更新离线存储？

当用户本地再次联网的时候，本地的离线存储资源需要检查是否需要更新，这个更新过程，也是通过manifest的更新来控制的，更新了manifest文件，浏览器会自动的重新下载新的manifest文件并在下一次刷新页面的时候进行资源文件的重新请求（第三次刷新替换本地缓存为最新缓存），而且这个请求是全局性的，也就是所有在manifest缓存列表中的文件都会被请求一次，而不是单独请求某个特定修改过的资源文件，因为manifest是不知道哪个文件被修改过了的。

对于全局更新的担心是不必要的，因为对于没有更新过的资源文件，请求依旧是304响应，只有真正更新过的资源文件才是200.

所以控制离线存储的更新，需要2个步骤，一是更新资源文件，二是更新manifest文件，特别的，更新manifest文件是不需要修改什么特定内容的，只要是这个文件随意一处被修改，那么浏览器就会感知，对于我们的资源文件通常名称是固定的，比如**.css，更新内容不会带有文件名更新的情况下，需要更新manifest文件怎么操作呢？一个比较好的方式是更新任意一处# 开头的注释即可，其目的只是告诉浏览器这个manifest文件被更新过。

以上的这些内容，其更新操作都是浏览器自动完成的。同样的，W3C定义了[离线存储的API规范](http://www.whatwg.org/specs/web-apps/current-work/#applicationcache)

### API：

```
  // 更新，一般来说更新下载是通过用户**（如浏览器）自动完成的，但是这个方法适用于一些长期打开的页面，比如邮件系统，可能这个页面是长期打开的，而不会有刷新动作，所以这个就比较适合做自动更新下
 void update();

  // 取消
  void abort();

  // 替换缓存内容 ，对于manifest文件的改变，通常是下一次的刷新才会触发下载更新，第三次刷新才会切换使用新的缓存文件，通过这个方法，可以强制将缓存替换
  void swapCache();

```

提供了如下的事件：

```
  Event handler             Event handler event type
  onchecking             checking
  onerror                     error
  onnoupdate             noupdate
  ondownloading             downloading
  onprogress             progress
  onupdateready             updateready
  oncached                     cached
  onobsolete             obsolete
```

最后说一个对于manifest比较特别的地方：对于某个文件a.htm，其中有 <html manifest='a.manifest'> ，那么离线存储中，会自动将a.htm加入到列表中，这意味着a.htm的再次刷新将从本地缓存中获取，这样的机制从官方得到的答复是“特别的设计”，而对我们来说，这种强加的特性在后续的开发过程中会有不少问题。

比如：

       1、如何计算PV UV，由于当前页面被强制加入manifest，那么PV 和UV的统计，成了一个难题，因为请求不再是发送到服务器；

       2、对于某个使用manifest的文件，其带有的参数可能是随机性的统计参数，如sid=123sss, sid=234fff ，尤其是比如商品详情的id字段等，这样每个页面都自动加入到manifest中，将会带来很大的存储开销，而且是毫无意义的；

       所以伴随而来的，是如何在现有的体系架构下进行数据统计的难题，一个常规的方案是进入离线存储页面后自动发出ajax请求，以告知服务器统计PV UV；

       对于第二个问题，可能就比较棘手，但是将GET请求的方式改成POST的方式确实是个解决问题的方案。
