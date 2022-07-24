---
title: HTTP2 Server Push
date: 2021-09-22 16:28:03
categories: 前端
---
## 1，HTTP2 的新特性。

关于 HTTP2 的新特性，读着可以参看我之前的文章，这里就不在多说了，本篇文章主要讲一下 server push 这个特性。

[HTTP,HTTP2.0,SPDY,HTTPS 你应该知道的一些事](http://www.alloyteam.com/2016/07/httphttp2-0spdyhttps-reading-this-is-enough/)

## 2，Server Push 是什么。

简单来讲就是当用户的浏览器和服务器在建立链接后，服务器主动将一些资源推送给浏览器并缓存起来，这样当浏览器接下来请求这些资源时就直接从缓存中读取，不会在从服务器上拉了，提升了速率。举一个例子就是：

假如一个页面有 3 个资源文件 **index.html**,**index.css**,**index.js**, 当浏览器请求 index.html 的时候，服务器不仅返回 index.html 的内容，同时将 index.css 和 index.js 的内容 push 给浏览器，当浏览器下次请求这 2 两个文件时就可以直接从缓存中读取了。

## 3，Server Push 原理是什么。

要想了解 server push 原理，首先要理解一些概念。我们知道 HTTP2 传输的格式并不像 HTTP1 使用文本来传输，而是启用了二进制帧 (Frames) 格式来传输，和 server push 相关的帧主要分成这几种类型：

1.  HEADERS frame(请求返回头帧): 这种帧主要携带的 http 请求头信息，和 HTTP1 的 header 类似。
2.  DATA frames(数据帧) : 这种帧存放真正的数据 content，用来传输。
3.  PUSH_PROMISE frame(推送帧): 这种帧是由 server 端发送给 client 的帧，用来表示 server push 的帧，这种帧是实现 server push 的主要帧类型。
4.  RST_STREAM(取消推送帧): 这种帧表示请求关闭帧，简单讲就是当 client 不想接受某些资源或者接受 timeout 时会向发送方发送此帧，和 PUSH_PROMISE frame 一起使用时表示拒绝或者关闭 server push。

Note:HTTP2.0 相关的帧其实包括 [10 种帧](https://tools.ietf.org/html/rfc7540#section-11.2)，正是因为底层数据格式的改变，才为 HTTP2.0 带来许多的特性，帧的引入不仅有利于压缩数据，也有利于数据的安全性和可靠传输性。

**了解了相关的帧类型，下面就是具体 server push 的实现过程了：**

1.  由多路复用我们可以知道 HTTP2 中对于同一个域名的请求会使用一条 tcp 链接而用不同的 stream ID 来区分各自的请求。
2.  当 client 使用 stream 1 请求 index.html 时，server 正常处理 index.html 的请求，并可以得知 index.html 页面还将要会请求 index.css 和 index.js。
3.  server 使用 stream 1 发送 PUSH_PROMISE frame 给 client 告诉 client 我这边可以使用 stream 2 来推送 index.js 和 stream 3 来推送 index.css 资源。
4.  server 使用 stream 1 正常的发送 HEADERS frame 和 DATA frames 将 index.html 的内容返回给 client。
5.  client 接收到 PUSH_PROMISE frame 得知 stream 2 和 stream 3 来接收推送资源。
6.  server 拿到 index.css 和 index.js 便会发送 HEADERS frame 和 DATA frames 将资源发送给 client。
7.  client 拿到 push 的资源后会缓存起来当请求这个资源时会从直接从从缓存中读取。

## 4，Server Push 怎么用。

既然 server push 这么神奇，那么我们如何使用呢？怎么设置服务器 push 哪些文件呢？

首先并不是所有的服务器都支持 server push，nginx 目前还不支持这个特性，可以在 nginx 的官方博客上得到证实 [https://www.nginx.com/blog/http2-r7/](https://www.nginx.com/blog/http2-r7/)，但是 Apache 和 nodejs 都已经支持了 server push 这一个特性，需要说明一点的是 server push 这个特性是基于浏览器和服务器的，所以浏览器并没有提供相应的 js api 来让用户直接操作和控制 push 的内容，所以只能是通过 header 信息和 server 的配置来实现具体的 push 内容，本文主要以 nodejs 来说明具体如何使用 server push 这一特性。

**准备工作**：下载 [nodejs http2](https://github.com/molnarg/node-http2) 支持，本地启动 nodejs 服务。

**1\. 首先我们使用 nodejs 搭建基本的 server：**

```

var  http2  =  require('http2');

var  url=require('url');

var  fs=require('fs');

var  mine=require('./mine').types;

var  path=require('path');

var  server  =  http2.createServer({

  key:  fs.readFileSync('./zs/localhost.key'),

  cert:  fs.readFileSync('./zs/localhost.crt')

},  function(request,  response)  {

    var  pathname  =  url.parse(request.url).pathname;

    var  realPath  =  path.join("my",  pathname);    //这里设置自己的文件名称;

    var  pushArray  =  [];

    var  ext  =  path.extname(realPath);

    ext  =  ext  ?  ext.slice(1)  :  'unknown';

    var  contentType  =  mine[ext]  ||  "text/plain";

    if  (fs.existsSync(realPath))  {

        response.writeHead(200,  {

            'Content-Type':  contentType

        });

        response.write(fs.readFileSync(realPath,'binary'));

    }  else  {

      response.writeHead(404,  {

          'Content-Type':  'text/plain'

      });

      response.write("This request URL "  +  pathname  +  " was not found on this server.");

      response.end();

    }

});

server.listen(443,  function()  {

  console.log('listen on 443');

});

```

这几行代码就是简单搭建一个 nodejs http2 服务，打开 chrome，我们可以看到所有请求都走了 http2，同时也可以验证多路复用的特性。

**这里需要注意几点：**

1.  创建 http2 的 nodejs 服务必须时基于 https 的，因为现在主流的浏览器都要支持 SSL/TLS 的 http2，证书和私钥可以自己通过 [OPENSSL](https://www.openssl.org/) 生成。
2.  node http2 的相关 api 和正常的 node httpserver 相同，可以直接使用。

2\. 设置我们的 server push：

```
var  pushItem  =  response.push('/css/bootstrap.min.css',  {

 request:  {

            accept:  '*/\*'

 },

      response:  {

            'content-type':  'text/css'

 }

});

pushItem.end(fs.readFileSync('/css/bootstrap.min.css','binary'));

```

我们设置了 bootstrap.min.css 来通过 server push 到我们的浏览器, 我们可以在浏览器中查看：

可以看到，启动 server push 的资源 timelime 非常快，大大加速了 css 的获取时间。

**这里需要注意下面几点：**

1.  我们调用 response.push(), 就是相当于 server 发起了 PUSH_PROMISE frame 来告知浏览器 bootstrap.min.css 将会由 server push 来获取。
2.  response.push() 返回的对象时一个正常的 ServerResponse,end(),writeHeader() 等方法都可以正常调用。
3.  这里一旦针对某个资源调用 response.push() 即发起 PUSH_PROMISE frame 后，要做好容错机制，因为浏览器在下次请求这个资源时会且只会等待这个 server push 回来的资源，这里要做好超时和容错即下面的代码：
```

    try  {

        pushItem.end(fs.readFileSync('my/css/bootstrap.min.css','binary'));

        }  catch(e)  {

     response.writeHead(404,  {

     'Content-Type':  'text/plain'

     });

     response.end('request error');

    }

    pushItem.stream.on('error',  function(err){

        response.end(err.message);

    });

    pushItem.stream.on('finish',  function(err){

     console.log('finish');

    });

```

    上面的代码你可能会发现许多和正常 nodejs 的 httpserver 不一样的东西，那就是 stream，其实整个 http2 都是以 stream 为单位，这里的 stream 其实可以理解成一个请求，更多的 api 可以参考：[node-http2](https://github.com/molnarg/node-http2/wiki/Public-API)。

5.  最后给大家推荐一个老外写的专门服务 http2 的 node server 有兴趣的可以尝试一下。[https://gitlab.com/sebdeckers/http2server](https://gitlab.com/sebdeckers/http2server)

## 5，Server Push 相关问题。

1.  我们知道现在我们 web 的资源一般都是放在 CDN 上的，那么 CDN 的优势和 server push 的优势有何区别呢，到底是哪个比较快呢？这个问题笔者也一直在研究，本文的相关 demo 都只能算做一个演示，具体的线上实践还在进行中。
2.  由于 HTTP2 的一些新特性例如多路复用，server push 等等都是基于同一个域名的，所以这可能会对我们之前对于 HTTP1 的一些优化措施例如 (资源拆分域名，合并等等) 不一定适用。
3.  server push 不仅可以用作拉取静态资源，我们的 cgi 请求即 ajax 请求同样可以使用 server push 来发送数据。
4.  最完美的结果是 CDN 域名支持 HTTP2,web server 域名也同时支持 HTTP2。

参考资料：

1.  HTTP2 官方标准：[https://tools.ietf.org/html/rfc7540](https://tools.ietf.org/html/rfc7540)
2.  维基百科：[https://en.wikipedia.org/wiki/HTTP/2_Server_Push](https://en.wikipedia.org/wiki/HTTP/2_Server_Push)
3.  [https://www.nihaoshijie.com.cn/index.php/archives/651](https://www.nihaoshijie.com.cn/index.php/archives/651)
