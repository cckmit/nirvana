---
title: HTTP/3 官宣，什么是 HTTP/3?
date: 2022-06-09 16:28:03
categories: 前端
---
## 介绍
##### HTTP 简史
发布的第一个 HTTP 版本是 HTTP/0.9。Tim Berners-Lee 于 1989 年创建了它，并于 1991 年将其命名为 HTTP/0.9。HTTP/0.9 功能是有限的，只能做基本的事情。除了网页之外，它无法返回任何内容，并且不支持 cookie 和其他现代功能。1996 年，HTTP/1.0 发布，带来了新功能，如 POST 请求和发送网页以外的内容的能力。但是，与今天相比，还有很长的路要走。HTTP / 1.1在1997年发布，并进行了两次修订，一次是在1999年，一次是在2007年。它带来了许多主要的新功能，例如cookie和连接仍然存在。最后，在 2015 年，HTTP/2 发布并允许提高性能，使诸如服务器发送事件和一次发送多个请求的能力成为可能。HTTP/2 仍然是新的，只有不到一半的网站使用。
![](https://upload-images.jianshu.io/upload_images/10024246-2a244002530c0303.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
##### HTTP/3：最新版本的 HTTP
HTTP/3或HTTP over QUIC，改变了HTTP很多。HTTP 传统上是通过 TCP（传输控制协议）完成的。但是，TCP于1974年互联网开始发展。当 TCP 最初创建时，它的作者无法预测网络的增长。由于 TCP 已过时，因此 TCP 在一段时间内限制了 HTTP 的速度和安全性。现在，由于 HTTP/3，HTTP 不再受限制。HTTP/3 没有使用 TCP，而是使用了一种由 Google 于 2012 年开发的新协议，称为 QUIC（发音为“quick”）。这为 HTTP 引入了许多新功能。
`HTTP 和 QUIC RFC 的关系如图：`
![](https://upload-images.jianshu.io/upload_images/10024246-7d9e38d9ffd2aaff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
`HTTP 协议之间的关系和组成图`
![](https://upload-images.jianshu.io/upload_images/10024246-8ff3e5e370dc35df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## HTTP3优点
###### 更快的请求多路复用
HTTP/2 和 HTTP/3 之间的主要区别在于它们使用的传输协议。
在 HTTP/2 之前，浏览器一次只能向服务器发送一个请求。这使得网站加载速度明显变慢，因为浏览器一次只加载一项资源，如 CSS 或 JavaScript。HTTP/2 引入了一次加载多个资源的能力，但 TCP 并非为此而生。如果请求之一失败，TCP将使浏览器重做所有请求。
HTTP/3 使用了 QUIC 新协议来代替 TCP 协议，使用 HTTP/3，浏览器只需要重做失败的请求。因此，HTTP/3 更快、更可靠。
同时QUIC 基于 UDP 开发, 和 TCP 不一样是, UDP 并不需要三次握手, 结合 TLS1.3, 也为 0-RTT 加密传输带来了可能, HTTP/3 还带来了新的头部压缩算法QPACK。
![](https://upload-images.jianshu.io/upload_images/10024246-88475233d1ce437f.gif?imageMogr2/auto-orient/strip)
##### 更快的加密
HTTP/3 优化了允许浏览器 HTTP 请求被加密的“握手”。QUIC 将初始连接与 TLS 握手相结合，使其默认安全且速度更快。
QUIC一如既往是安全的，它没有明文版本，想要建立一个QUIC连接，就必须通过TLS 1.3来进行加密保证安全。加密可以避免协议僵化等拦截和特殊处理。这也使QUIC具有了Web用户所期望的所有HTTPS安全特性。
QUIC在加密协商前，只有很少的初始握手报文会以明文形式发送。

## 标准化
IETF（互联网工程任务组）[宣布](https://www.rfc-editor.org/info/rfc9114)了 HTTP/3 标准，编号为**[ RFC 9114](https://www.rfc-editor.org/info/rfc9114)**。RFC Editor 页面显示，目前 RFC 9114 处于 “提案标准 (PROPOSED STANDARD)” 状态，尚未成为正式标准。

![](https://upload-images.jianshu.io/upload_images/10024246-e3d969a1ae1a8966.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 浏览器支持
目前，由于谷歌创建了 QUIC 协议和 HTTP over QUIC 的提议，Chrome 默认支持 HTTP/3。Firefox 也支持 88+ 版本中没有标志的协议。Safari 14支持HTTP/3，但前提是启用了实验性功能标志。

![](https://upload-images.jianshu.io/upload_images/10024246-6cd1acc3dfc75732.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**Serverless/CDN 支持**

到目前为止，只有部分服务器支持 HTTP/3，但它们的份额正在增长。Cloudflare 是除 Google 之外最早支持 HTTP/3 的公司之一，因此它们的无服务器功能和 CDN 符合 HTTP/3 标准。此外，Google Cloud 和 Fastly 符合 HTTP/3 标准。不幸的是，Microsoft Azure CDN 和 AWS CloudFront 目前似乎不支持 HTTP/3。如果您想尝试 HTTP/3，QUIC.Cloud是一种在您的服务器前设置缓存 HTTP/3 CDN 的有趣（虽然是实验性的）方法。Cloudflare、Fastly 和 Google Cloud 也有良好的 HTTP/3 支持，并且更适合生产。



