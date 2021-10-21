---
title: Nginx 缓存机制详解
date: 2021-10-11 16:28:03
category: linux
---
Nginx 缓存作为性能优化的一个重要手段，可以极大减轻后端服务器的负载。下面我们将介绍 Nginx 缓存配置的相关指令以及 http 缓存机制，以及 Nginx 缓存实践案例分析。

![](https://upload-images.jianshu.io/upload_images/10024246-ded1b4975842106b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## Nginx 缓存示例

实例演示，缓存是怎么出现的，怎么查看！

当我们代开某个网站，如 baidu.com，我们可以看到 size 这一列有一些 js 标识为 disk cache，这里就是应用到了缓存。

![](https://upload-images.jianshu.io/upload_images/10024246-43c5894bb0f0e854.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-e45534f515956b4f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## HTTP 缓存机制

HTTP 的缓存流程如下图所示

缓存，可以分为**强制缓存和对比缓存**。

![](https://upload-images.jianshu.io/upload_images/10024246-cc9e727b78500b19.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## Nginx 强制缓存

介绍强制缓存是什么？以及可能造成这个原因的配置参数！

浏览器不会向服务器发送任何请求，直接从本地缓存中读取缓存数据并返回 200 状态码，如下图所示。如果缓存过期再找服务器，其过程如下：

![](https://upload-images.jianshu.io/upload_images/10024246-62f21177b09824fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-28d5945527424a0b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以造成强制缓存的字段，有如下几个：

###### Expires

位置: HTTP Response Header

说明: Expires 是服务端返回的到期时间。如果下一次请求如果小于服务端返回的过期时间，则直接使用缓存数据。Expires 是 HTTP1.0 的东西，现在浏览器默认都是使用 HTTP1.1。而且由于该值是有服务端生成，而客户端的时间和服务端的时间有可能不一致，导致存在一定误差。所以 HTTP1.1 使用 Cache-Control 替代。

```
# 示例
Expires: Mon, 22 Jul 2019 11:08:59 GMT
```

###### Cache-Control

位置: HTTP Response Header

说明: 缓存策略定义

*   max-age: 标识资源能够被缓存的最大时间
*   public: 表示该响应任何中间人，包括客户端和代理服务器都可以缓存
*   private: 表示该响应只能用于浏览器私有缓存中，中间人（代理服务器）不能缓存此响应
*   no-cache: 需要使用对比缓存（Last-Modified/If-Modified-Since/Etag/If-None-Match）来验证缓存数据
*   no-store: 所有内容都不会缓存，强制缓存和对比缓存都不会触发

## Nginx 对比缓存

介绍使用缓存和不使用缓存的区别和对比！

浏览器在第一次请求数据时，服务器会将缓存的标识与数据一起返回给浏览器，浏览器将这两个缓存到本地缓存数据库中。

再次请求数据时，就会在请求 header 中带上缓存的标识发送给服务器，服务器根据缓存标识对比，如果发生变化，则返回 200 状态码，返回完整的响应数据给浏览器，如果未发生更新，则返回 304 状态码告诉浏览器继续使用缓存数据。

![](https://upload-images.jianshu.io/upload_images/10024246-e0a4f4da9a3889d8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如下图比较所示，在第一次请求时，没有使用缓存。而在第二次使用缓存时，可以明显看到两者请求的时间不一样，后者时间少很多。这是因为服务端如果进行缓存比较后发现未更新，只返回 header 部分，并返回 304 状态码通知客户端使用本地缓存，没有将报文的 body 部分返回给浏览器，所以请求时间和报文大小才明显优化。别小看这几十毫秒，当访问量很大时，这里就优化了很多时间、减少了很多服务器压力。

###### 第一次访问，未使用缓存：

![](https://upload-images.jianshu.io/upload_images/10024246-77505633a996b571.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###### 第二次访问，使用缓存：

![](https://upload-images.jianshu.io/upload_images/10024246-7510e62d16afca93.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

HTTP 请求和响应报文结构如下图所示：

![image.png](https://upload-images.jianshu.io/upload_images/10024246-b88478a94585e571.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


会造成对比缓存的字段如下：

*   Last-Modified

    *   位置: HTTP Response Header
    *   说明: 第一次请求时，服务器会在响应头里设置该参数，告诉浏览器该资源的最后修改时间。
```
        # 示例
        Last-Modified: Tue, 12 Jan 2010 13:48:00 GMT
```

*   If-Modified-Since

    *   位置: HTTP Request Header
    *   说明: 再次（注意不是第一次）请求服务器时，客户端浏览器通过此字段通知服务器上次请求时，服务器返回的资源最后修改时间。服务器收到请求后，发现 header 中有 If-Modified-Since 字段，则与被请求资源的最后修改时间进行对比。若资源的最后修改时间大于 If-Modified-Since，则说明资源被修改过，则响应返回完整的内容，返回状态码 200。 若资源的最后修改时间小于或等于 If-Modified-Since，则说明资源未修改，则返回 304 状态码，告诉浏览器继续使用所保存的缓存数据。
*   Etag
*   位置: HTTP Response Header

    *   说明: 服务器响应请求时，告诉浏览器当前资源在服务器的唯一标识（由服务端生成）。
    *   优先级: 高于 Last-Modified 与 If-Modified-Since

![](https://upload-images.jianshu.io/upload_images/10024246-179efa2c4269a211.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


*   If-None-Match

    *   位置: HTTP Request Header
    *   说明: 再次请求服务器时，通过此字段通知服务器客户端缓存的资源的唯一标识。服务器收到请求 header 周发现有 If-None-Match 字段，则与被请求资源的唯一标识进行对比。 如果不一样，说明资源被修改过，则返回完整的响应，状态码 200。 如果一样，说明资源未被修改过，则返回 304 状态码，告诉浏览器继续使用缓存的数据。

## Nginx 缓存实践

实际配置和演示一下，使用缓存的效果！

配置文件的内容，如下所示：

```
nginx
user  nginx;
pid /run/nginx.pid;
worker_processes  auto;
worker_rlimit_nofile 100000;

events {
    worker_connections  2048;
    multi_accept on;
}

http {
    sendfile on;

    access_log off;
    error_log  /data/log/nginx-1.0/error.log  error;

    proxy_cache_path /data/nginx-1.0/cache levels=1:2 keys_zone=cache_zone:10m inactive=60m;

    server {
        listen 80;
        server_name localhost;
        root /usr/local/services/nginx-1.0/html/;

        location / {

        }

        location ~.*\.(gif|jpg|png|css|js)(.*) {
            proxy_cache cache_zone;
            proxy_cache_valid 200 302 24h;
            expires 1d;
            add_header X-Proxy-Cache $upstream_cache_status;
        }
    }
}
```

实际的测试情况，如下所示：

```
[root@VM_16_4_centos conf]# curl -I http://localhost/test.js
HTTP/1.1 200 OK
Server: nginx/1.14.0
Date: Sun, 21 Jul 2019 12:35:06 GMT
Content-Type: text/plain
Content-Length: 12
Last-Modified: Sun, 21 Jul 2019 12:33:32 GMT
Connection: keep-alive
ETag: "5d345b9c-c"
Expires: Mon, 22 Jul 2019 12:35:06 GMT
Cache-Control: max-age=86400
Accept-Ranges: bytes
```

我们再以图片为例，当我们第一次请求 [http://localhost/google_logo.jpg](https://link.segmentfault.com/?enc=30aueDZxSuZnbJrnNci4NQ%3D%3D.oi6awxTtfSBOjl6JyCRu6DJ8%2F6vTmiGEdbLiHHHjxq7280U6bbFuZCR36CZQe%2FX8)，服务端返回了该资源的唯一标识 Etag 给我们。

![](https://upload-images.jianshu.io/upload_images/10024246-1427ee88f2028ca7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-f244419f249f9350.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们第二次请求时，可以发现 http 报文的体积和响应实践大大缩减，说明我们的缓存发回了作用。

![](https://upload-images.jianshu.io/upload_images/10024246-3f60f097b649d67e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/10024246-227315ed72a0f8f2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


>作者: Escape
链接: escapelife.site/posts/b167e14a.html
