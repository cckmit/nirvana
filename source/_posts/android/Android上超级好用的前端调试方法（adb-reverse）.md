---
title: Android上超级好用的前端调试方法（adb-reverse）
date: 2021-03-09 21:12:40
categories: android
---
前端开发前端页面，经常需要改两行代码就要看看页面效果。如果是兼容桌面浏览器的页面还好办，在本地启动服务，写两句代码refresh一下就可以看到效果了，但是更多的时候，页面是必须在APP中加载，用到APP提供的JsApi的，桌面浏览器根本没法使用页面的功能。

android手机可硬使用adb reverse，终端中输入执行一下adb，列出的帮助文档中，有这样一段：
![](https://upload-images.jianshu.io/upload_images/10024246-f14f2905f7b87eb0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Android允许我们通过ADB，把Android上的某个端口映射到电脑（adb forward），或者把电脑的某个端口映射到Android系统（adb reverse）。

假设电脑上开启的服务，监听的端口为8000。Android手机通过USB连接电脑后，执行 adb reversetcp:8000 tcp:8000，然后在手机中访问127.0.0.1:8000，就可以访问到电脑上启动的服务了，
