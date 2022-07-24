---
title: Webpack 配置 proxy 代理解决跨域问题
date: 2022-04-25 21:12:40
categories: webpack
---
## 问题
[JS跨域及解决方案](https://www.jianshu.com/p/0e9a368ffb74)介绍了跨域的基本概念和解决方案，但是在开发中，我们就需要采用新的技术去实现。
![](https://upload-images.jianshu.io/upload_images/10024246-6be3c1fbb6407675.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
一般情况我们可以通过修改请求中的headers去解决，但是在开发环境中服务端的headers是固定的，前端传过去后端没有采用。或者直接找后端同学修改`Access-Control-Allow-Origin`为*，这种直接放开所有域名不太好。
## 解决方案
浏览器发送请求到非同源服务器，会导致跨域问题，而服务器与服务器之间的通信是不存在跨域问题的，因此我们在本地 `npm run serve `搭建的服务器，先接收到我们的请求，之后由本地服务器转发请求到目标服务器即可获取资源，再将资源从本地服务器发送给浏览器，这是 `Webpack`中配置` devServer `解决跨域的思路。
1、在webpack配置文件的devServer里面配置如下信息
```
//proxy中，本地服务器拦截浏览器请求，由本地服务器转发给target值【目的服务器】。
devServer: {
    host: 'localhost',
    port: '8083',
    proxy: {
      '/api': {  // /api 表示拦截以/api开头的请求路径
        target: 'http:127.0.0.1:3000', // 跨域的域名
        changeOrigin: true, // 是否开启跨域
      }
    }
  }
```
2、2. 配置中的主要参数说明
```
2.1 “/api”
捕获API的标志，如果API中有这个字符串，那么就开始匹配代理，比如API请求/api/store/get, 会被代理到请求 http://www.baidu.com/api/store/get 。

2.2 target
代理的API地址，就是需要跨域的API地址。
地址可以是域名,如：http://www.baidu.com
也可以是IP地址：http://127.0.0.1:3000
如果是域名需要额外添加一个参数changeOrigin: true，否则会代理失败。

2.3 pathRewrite
路径重写，也就是说会修改最终请求的API路径。
比如访问的API路径：/api/store/get,设置pathRewrite: {'^/api' : ''},后，最终代理访问的路径：http://www.baidu.com/store/get，这个参数的目的是给代理命名后，在访问时把命名删除掉。

2.4 changeOrigin
这个参数可以让target参数是域名。

2.5 secure
secure: false,不检查安全问题。
设置后，可以接受运行在 HTTPS 上，可以使用无效证书的后端服务器
```