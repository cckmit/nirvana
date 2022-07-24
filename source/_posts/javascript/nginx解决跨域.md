---
title: nginx解决跨域
date: 2021-06-10 16:28:03
categories: 前端
---
在项目开发中，我们很常见的一个问题是，本地是`localhost`开发的，接口一般是给`IP`地址，例如`http://192.168.13.14/api`, 这样直接在项目中调用接口 浏览器会报**跨域错误**， 因为`localhost`和`http://192.168.13.14`并不是同源的。

一般我们的解决方法是在`webpack`中设置代理，配置`devServer`:

```
module.exports = {
    devServer: {
        proxy: {
            '/api': {
                target: 'http://192.168.13.14/api',
                changeOrigin : true
            }
        }
    }
}
```

设置以后，在接口访问 假设为`http://localhost:8080/api`时，会转发到`http://192.168.13.14/api`去.

所以，如果不是通过`devServer`的方式，该怎么做呢？

![](https://upload-images.jianshu.io/upload_images/10024246-0bb8f431d7e4de41.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

接手的项目就没有配置`webpack devServer`，仔细看是设置了`Access-Control-Allow-Origin: http://localhost:8054`来解决接口跨域的，但是，这个项目的鉴权方式是`cookie`, 这里地址`http://localhost:8054`和接口地址==不同源导致`cookie`设置失败==

那么现在的想法要让**浏览器地址与接口地址同源**，那么cookie自然能够设置成功了！

可以通过配置`nginx`来实现。

配置：

```
server {
    listen 8055 {
        // 步骤1 
        location / {
            proxy_pass: http://localhost:8054/;
        }
        // 步骤2
        location /api {
            proxy_pass: http://192.169.13.14;
        }
    }
}
```

我访问的地址本来是`http://localhost:8054`, 这里我监听8055端口。

*   步骤1 做地址的代理，当访问`http://localhost:8055`时，会映射到`http://localhost:8054`
*   步骤2 做接口的代理，当访问`http://localhost:8055/api/xxxx`时，会映射到`http://192.169.13.14/api/xxxx`
*   还有一步，在项目中，要动态配置接口地址：

```
const apiAddress = window.location.protocol + '//' + window.location.host + '/api';
```
这样，浏览器地址与接口地址就都是`localhost:8055`开头的，不存在跨域的问题，`cookie`就能成功设置
