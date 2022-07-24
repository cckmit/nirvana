---
title: 修复image在hexo自建博客显示403
date: 2021-03-11 21:12:40
categories: 前端
---

使用hexo自建个人博客，本地localhost Debug的时候完全可以显示，但是将网站部署到服务器后就会图片全部无法显示，遇到这样的错误
打开google开发者工具
可以看到，加载图片时显示403
![](https://upload-images.jianshu.io/upload_images/10024246-21238a83cbe055a6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

解决方案：利用html中的meta标签关闭浏览器的UrlReferer
访问
```
themes/next(主题)/layout/_partials/head/head.njk
```
在里面的<head></head>中添加下面代码即可
```
 <meta name="referrer" content="no-referrer"/>
```
