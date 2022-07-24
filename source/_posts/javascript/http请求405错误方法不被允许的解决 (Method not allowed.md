---
title: http请求405错误方法不被允许的解决 (Method not allowed)
date: 2021-09-18 16:28:03
categories: 前端
---

- 1.首先看到的页面是nginx返回的页面，得知错误要从nginx上来解决
```
<html>
<head><title>405 Not Allowed</title></head>
<body bgcolor="white">
<center><h1>405 Not Allowed</h1></center>
<hr><center>nginx/1.0.11</center>
</body>
</html>
```
- 2.上网查资料，原来因为这里请求的静态文件采用的是post方法，nginx是不允许post访问静态资源。题话外，试着post访问了下www.baidu.com发现页面也是报错，可以试着用get方式访问
- 3.现贴出三种解决方式
  1.将405错误指向成功(我采用的这种方法解决的问题)

静态server下的location加入error_page 405 =200 $uri;
```
location ~ ^/better/.*\.(htm|html|gif|jpg|jpeg|png|ico|rar|css|js|zip|txt|flv|swf|doc|ppt|xls|pdf|json|ico|htc)$ {
<span style="white-space:pre">	</span>root D:/code/BetterjrWeb;
<span style="white-space:pre">	</span>error_page 405 =200 $uri;
｝
```
2.修改nginx下src/http/modules/ngx_http_static_module.c文件
```
if (r->method & NGX_HTTP_POST) {
     return NGX_HTTP_NOT_ALLOWED;
}
```
这一段注释掉，重新编译，不要make install编译生成的nginx文件复制到sbin下  重启nginx

3.修改错误界面指向(网上多流传这种方式，但是没有改变请求方法，所以行不通，所以采用以下方法)
```
upstream static_backend {
    server localhost:80;
}
 
server {
    listen 80;
    # ...
    error_page 405 =200 @405;
    location @405 {
        root /srv/http;
        proxy_method GET;
        proxy_pass http://static_backend;
    }
}
```