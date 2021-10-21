---
title: nginx 在前端中的简单应用
date: 2021-09-19 16:28:03
category: javascript
---
### 高性能 Web 服务

Web 服务实际上又称静态资源服务，自从前后端分离后，前端的输出趋向于静态资源的形式，什么是静态资源：就是我们通常用webpack构建输出的结果，比如：

![](https://upload-images.jianshu.io/upload_images/10024246-03ab6226829a7097.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


而为了提供文件在互联网中的可访问性，前端还是需要依赖`静态资源服务`；最常用的做法就是Node服务和Nginx服务。

Node服务最常见的，就是WebpackServer, 在前端开发联调时经常用到, 启动后我们就可以通过http://localhost:8907/bundle.05a01f6e.js的形式来访问构建资源；除此之外，我给大家安利另一款Node服务库：[serve](https://www.npmjs.com/package/serve), 它也能快速启动一个静态资源服务。

但在生产环境，我们一般用Nginx来处理，不是Node不好，而是Nginx已经够好了。通常整个大前端会有很多前端项目，我们都将构建结果放在一台服务器上（一般有备份机器）的某个文件夹下,然后通过安装Nginx来创建一个静态资源服务供所有前端资源的访问；比如文件夹static下有A，B，C，D四个前端项目资源, 我们通过nginx配置:

```
server {
        listen       80;
        listen       [::]:80;
        server_name  static.closertb.site;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

       location / {
           alias /home/static/;
           autoindex on;
           if ($request_uri ~ .*\.(css|js|png|jpg)$) {
                #etag on;
                expires 365d;
                #add_header    Cache-Control  max-age=31536000;
           }
        }
    }
```

我们即可通过http://static.closertb.site/A访问A项目，通过http://static.closertb.site/C访问C项目, 从而做到一鸡多吃，这种玩法在HTTPS与HTTP2的时代特别常见。

以上就是Nginx作为Web服务的简单用法，接下来我们了解一下反向代理服务

### 反向代理服务

作为一个开发者，可能经常听到`代理`两字，但很多人区分不清楚正向和反向的区别：

![](https://upload-images.jianshu.io/upload_images/10024246-51233e1a415e0739.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如上图左侧所示，正向代理是用户的主动行为，如我们fq时访问goggle所做的；右侧反向代理是我们访问的服务器行为，作为用户的我们是不能控制也无需关注的。

反向代理在服务部署中，是一种非常常见的技术，比如负载均衡、容灾、缓存。

而对于前端开发来说，反向代理多用于请求转发，来处理`跨域`问题。当我们把前端静态资源服务都指向一个域名（static.closertb.site）时，与服务端请求域名（server.closertb.site）不一致，就会造成跨域。如果服务端不配合的话，那通过nginx，前端也是可以轻松做到的，在前面的配置上，我们添加：

```
server {
        listen       80;
        listen       [::]:80;
        server_name  static.closertb.site;

        location /api/ {
            proxy_pass    http://server.closertb.site;
            proxy_set_header Host      $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }

        location / {
        ....
    }
```

所以当网页发出一个请求：http://static.closertb.site/a时，实际请求地址是：http://server.closertb.site/a，这就简单实现了一个服务代理。其原理与WebpackServer的proxy相似.

以上就是Nginx的web服务和代理服务在前端开发中的两个典型使用场景, 接下来我们说点零碎又有用的

## Nginx 配置精进

知识来源：[http://nginx.org/en/docs/http...](http://nginx.org/en/docs/http/ngx_http_core_module.html)

### root vs alias

```
location ^~ /static {
    root /home/static;
}
```

当请求http://static.closertb.site/site/a/logo.png)时，将会返回服务器上的/home/static/static/a/logo.png文件，即'/home/static'+'/static/a/logo.png',其`拼接的地址是匹配字符串及其以后的`

而对于alias：

```
location ^~ /static {
    root /home/static;
}
```

当请求http://static.closertb.site/static/a/logo.png)时，将会返回服务器上的/home/static/a/logo.png文件，即'/home/static'+'/a/logo.png',其`拼接的地址是除了匹配字符串以后的地址`

### location带'/'与不带'/'

你可能见过A这种：

```
location /api {
    proxy_pass http://server.closertb.site;
}
```

也可能见过B这种

```
location /api/ {
    proxy_pass http://server.closertb.site;
}
```

有什么区别？

两者与root 和 alias有相似之处，只不过这种差别，只适用于：

> 如果location是由以斜杠字符结尾的前缀字符串定义，并且请求由 proxy_pass、fastcgi_pass、uwsgi_pass、scgi_pass、memcached_pass 或 grpc_pass 之一处理，则执行特殊处理。响应具有等于此字符串但没有尾部斜杠的 URI 的请求，带有代码 301 的永久重定向将返回到附加斜杠请求的 URI

所以当收到一个请求：http://static.closertb.site/api/user/get) 时，配置A将会把请求代理到：http://server.closertb.site/api/user/get); 配置B将会把请求代理到：http://server.closertb.site/user/get)

这个知识，在代理配置中真的相当重要

### 请求重定向

当我们下架一个前端服务，或者用户访问了某个根本不存在的页面，我们不希望用户看到的是404，而是将其引导到一个模糊正确的页面，这时候我可以用rewrite服务；反手一个配置，直接就将流量打到了网站首页;

```
location /404.html {
    rewrite  ^.*$ / redirect;
}
```

另一个比较常用的，就是网站开启https，我们需要将所有http请求重定向到https：

```
server {
        listen       80 default_server;
        server_name  closertb.site;

        include /etc/nginx/default.d/*.conf;
        rewrite ^(.*)$ https://$host$1 permanent;
}
```

上面同是rewrite，但还是有不一样的 ，一个是redirect（302）, 另一个是permanent（301），这两个还是有很大区别的；

### 助力web性能体验优化

web 性能优化是前端的一门学问，好的静态资源加载速度，会显著提升用户体验，而nginx作为最常用的静态资源服务器，也是有诸多渠道来帮助我们来提升静态资源加载速度，简单来讲，可以三个方面，直接上配置：

*   开启资源缓存：因为目前资源除.html外，我们文件名都采用了hash方式，所以我们可以对除html文件以外的文件做长久缓存，这样能保证用户刷新页面时资源瞬间加载完成；

``if ($request_uri ~ .*\.(css|js|png|jpg|gif)$) {
    expires 365d;
    add_header  Cache-Control  max-age=31536000;
}</pre>

expires与max-age两种配置方式都可以达到告诉浏览器资源一年以后过期的目的，更多关于http缓存的可以[看这一篇文章](https://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ==&amp;mid=2651229883&amp;idx=1&amp;sn=0a775144fc84fc16c0ce581b80ab2a6c&amp;chksm=bd49573f8a3ede2957d0074c8fe047bdeff7614e6439d9077e15d0864eb90b8ae7e25abf2a1b&amp;mpshare=1&amp;scene=1&amp;srcid=0919YMh4RoejiqOUh0WnJZVI&amp;rd2werd=1#wechat_redirect)；

*   开启http2：随着HTTP2的不断成熟，以及其多路复用特性，可以大大的提升页面多资源依赖的首屏加载速度, 其实开启http2更多的工作量是在于开启https，然后加一个http2标识，网上文章很多，随便搜了一篇：[nginx配置ssl实现https访问](https://zhuanlan.zhihu.com/p/55673646)；

```
# listen  443 ssl default_server; 
listen  443 ssl http2 default_server;
```

*   开启gzip：http2解决了同一时间请求的个数，资源缓存提升了资源二次加载的速度，那么gzip就从根本上，减少了这个资源在网络中传输的大小, 直接在http这一级开启gzip配置即可；

```
http {
    gzip on;
    gzip_disable "msie6";
    gzip_min_length 10k;
    gzip_vary on;
    # gzip_proxied any;
    gzip_comp_level 3;
    gzip_buffers 16 8k;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
}
```
