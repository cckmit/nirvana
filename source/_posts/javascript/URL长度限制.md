---
title: URL长度限制
date: 2022-03-11 16:28:03
categories: 前端
---
最近在做的一个项目遇到了URL超长，服务端返回41X拒绝请求的错误，经过仔细排查才发现原来url长度超过了限制，通过缩短url和数据走接口的形式才解决问题，现在我们就来讨论下url的长度是那边来限制的。

## http协议不限制
其实http 1.1 协议中对url的长度是不受限制的，协议原文：
       The HTTP protocol does not place any a priori limit on the length of a URI. Servers MUST be able to handle the URI of any resource they serve, and SHOULD be able to handle URIs of unbounded length if they provide GET-based forms that could generate such URIs. A server SHOULD return 414 (Request-URI Too Long) status if a URI is longer than the server can handle (see section 10.4.15).
  >Note: Servers ought to be cautious about depending on URI lengths above 255 bytes, because some older client or proxyimplementations might not properly support these lengths.

翻译：
  
  HTTP协议不对URI的长度作事先的限制，服务器必须能够处理任何他们提供资源的URI，并且应该能够处理无限长度的URIs，这种无效长度的URL可能会在客户端以基于GET方式的请求时产生。如果服务器不能处理太长的URI的时候，服务器应该返回414状态码（此状态码代表Request-URI太长）。

  > 注:服务器在依赖大于255字节的URI时应谨慎，因为一些旧的客户或代理实现可能不支持这些长度。

具体参见[协议](http://www.ietf.org/rfc/rfc2616.txt) 中的3.2.1
虽然协议中未明确对url进行长度限制，但在真正实现中，url的长度还是受到限制的，一是服务器端的限制，二就是浏览器览器端的限制。

## 服务器限制
现在项目中使用的是`nginx`，它的设置参数：
- `large_client_header_buffers`
 该参数对`nginx`服务器接受客户端请求的头信息时所分配的最大缓冲区的大小做了限制，也就是`nginx`服务器一次接受一个客户端请求可就收的最大头信息大小。这个头不仅包含 `request-line`，还包括通用信息头、请求头域、响应头域的长度总和。这也相当程度的限制了url的长度。

nginx服务器默认的限制是`4K`或者`8K`，这是根据服务器的硬件配置有关的，一般为内存一页的大小，目前大部分为4K，即`4096`字节。
- `client_header_buffer_size` 默认值：`1k`

## 浏览器限制

浏览器的种类繁多，并且对URL的长度限制是有所差异的，具体如下：

|浏览器|	最大长度（字符数）    |      	备注|
|--|--|--|
| Internet Explorer  |	2083  |   	如果超过这个数字，提交按钮没有任何反应|
| Firefox|	65,536	 ||
 |chrome|	8182	 ||
| Safari |	80,000	 ||
| Opera|	190,000	 ||
|curl（linux下指令）|	8167	| |