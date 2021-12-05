---
title: 用阿里云的免费 SSL 证书让网站从 HTTP 换成 HTTPS
date: 2021-11-22 16:28:03
category: linux
---

>原文：https://ninghao.net/blog/4449

HTTP 协议是不加密传输数据的，也就是用户跟你的网站之间传递数据有可能在途中被截获，破解传递的真实内容，所以使用不加密的 HTTP 的网站是不太安全的。所以， Google 的 Chrome 浏览器将在 2017 年 1 月开始，标记使用不加密的 HTTP 协议的网站为 Not Secure，不安全。

![](https://upload-images.jianshu.io/upload_images/10024246-d33a092312fbb0d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在你要做的就是让网站支持 HTTPS，并不难，而且现在可以免费做到。要使用 HTTPS，你需要安全机构颁发的安全证书，然后配置服务器，去使用这个证书。下面介绍一下在阿里云免费申请安全证书，还有配置一般的 NGINX 服务器支持 HTTPS 的方法。

## 申请证书

1.  登录：阿里云控制台，产品与服务，证书服务，购买证书。
2.  购买：证书类型选择 免费型DV SSL，然后完成购买。
3.  补全：在 我的证书 控制台，找到购买的证书，在操作栏里选择 补全。填写证书相关信息。
4.  域名验证：可以选择 DNS，如果域名用了阿里云的 DNS 服务，再勾选一下 证书绑定的域名在 阿里云的云解析。
5.  上传：系统生成 CSR，点一下 创建。
6.  提交审核。

如果一切正常，10 分钟左右，申请的证书就会审核通过。

![image](https://upload-images.jianshu.io/upload_images/10024246-8215c23db8ce60a0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 2018-01-04：您也可以[使用 Let's Encrypt 签发的证书。](https://ninghao.net/blog/5592)

申请证书要注意的是验证域名，就是你要验证你想绑定证书的域名是你自己的，如果选择使用 DNS 验证，你需要在域名的管理里，添加一条特定的 DNS 记录，这样就可以证名这个域名是你自己的。使用了阿里云的云解析服务，这个步骤可以自动完成，会自动为你添加一条 DNS 验证的记录。

输入证书要绑定的域名：

![image](https://upload-images.jianshu.io/upload_images/10024246-2188f1df27b282ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

填写个人信息：

![image](https://upload-images.jianshu.io/upload_images/10024246-1663f595ee374615.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在域名的管理里，因为我用了阿里云的 DNS 解析服务，所以会自动添加一条 CNAME 记录，这条记录就是验证域名所有权用的：

![image](https://upload-images.jianshu.io/upload_images/10024246-4d7b1c6ca1e97020.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 下载证书

在阿里云的证书管理那里，如果申请的证书审核通过，你就可以下载了，点击 下载，可以选择不同的类型，可以选择 NGINX，或 Apache 之类的服务器。根据自己网站的 Web 服务器类型，下载对应的证书。解压以后，你会得到两个文件一个是 *.key，一个是 *.pem。

![image](https://upload-images.jianshu.io/upload_images/10024246-ac5ec4c5c7340a6c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 配置 NGINX 的 HTTPS

有了证书，就可以去配置 Web 服务器去使用这个证书了，不同的 Web 服务器地配置方法都不太一样。下面用 NGINX 服务器作为演示。我的域名是 ninghao.org，出现这个文字的地方你可以根据自己的实际情况去替换一下。

### 下载并上传证书

创建一个存储证书的目录：

`sudo mkdir -p /etc/nginx/ssl/ninghao.org`

把申请并下载下来的证书，上传到上面创建的目录的下面。我的证书的实际位置是：

```
/etc/nginx/ssl/ninghao.org/213985317020706.pem
/etc/nginx/ssl/ninghao.org/213985317020706.key
```

### NGINX 配置文件

你的网站可以同时支持 HTTP 与 HTTPS，HTTP 默认的端口号是 80，HTTPS 的默认端口号是 443。也就是如果你的网站要使用 HTTPS，你需要配置网站服务器，让它监听 443 端口，就是用户使用 HTTPS 发出的请求。

下面是一个基本的监听 443 端口，使用了 SSL 证书的 NGINX 配置文件，创建一个配置文件：

`touch /etc/nginx/ssl.ninghao.org.conf`

把下面的代码粘贴进去：

```
server {
  listen       443;
  server_name  ninghao.org;
  ssl          on;
  root /mnt/www/ninghao.org;
  index index.html; 

  ssl_certificate   /etc/nginx/ssl/ninghao.org/213985317020706.pem;
  ssl_certificate_key  /etc/nginx/ssl/ninghao.org/213985317020706.key;
  ssl_session_timeout 5m;
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
  ssl_ciphers AESGCM:ALL:!DH:!EXPORT:!RC4:+HIGH:!MEDIUM:!LOW:!aNULL:!eNULL;
  ssl_prefer_server_ciphers on;
}
```

上面的配置里，ssl_certificate 与 ssl_certificate_key 这两个指令指定使用了两个文件，就是你下载的证书，解压之后看到的那两个文件，一个是 *.pem，一个是 *.key。你要把这两个文件上传到服务器上的某个目录的下面。

重新加载 NGINX 服务：

```>sudo service nginx reload```

或：

```sudo systemctl reload nginx```

### 验证配置

在浏览器上输入带 https 的网站地址：[https://ninghao.org](https://ninghao.org/)

如果正确的配置了让服务器使用 SSL 证书，会在地址栏上显示一个绿色的小锁头图标。

![image](https://upload-images.jianshu.io/upload_images/10024246-9dc43eb4aaab327c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点开那个小锁头，会显示安全连接，再打开 详细信息。

![image](https://upload-images.jianshu.io/upload_images/10024246-bc2f9c2934716306.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

提示：

> This page is secure (valid HTTPS).

![image](https://upload-images.jianshu.io/upload_images/10024246-c569439e68c417b4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

打开 View certificate，会显示证书的相关信息。

![image](https://upload-images.jianshu.io/upload_images/10024246-b0a08c301005d867.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 搜索引擎优化

2015 年 5 月 25 日，[百度发公告声明](http://zhanzhang.baidu.com/wiki/392)已全面支持 HTTPS 网页的收录，使用 HTTPS 的网页被认为更安全，所以在排名上会被优先。 百度还推荐使用 301 重定向，把网站的 HTTP 重定向到 HTTPS 。

前几天我让[宁皓网](https://ninghao.net/)支持 HTTPS，并观察了搜索量，并未受到影响。所以，至少不用担心换成 HTTPS 以后，搜索量会下降。不过百度的反应一般都比较慢，需要给他更长的时间。换成 HTTPS 的后两天，谷歌已经收录了 HTTPS 版本的首页，但是百度至今还没有反应。不管怎么样，使用 HTTPS 都是迟早要做的事情。

NGINX 配置使用 301 重定向：

```
server {
  listen        80;
  server_name   ninghao.org;
  return 301    https://$host$request_uri;
}
```

上面的配置会让对 HTTP 网页的请求，重定向到 HTTPS 版本的网页上。
