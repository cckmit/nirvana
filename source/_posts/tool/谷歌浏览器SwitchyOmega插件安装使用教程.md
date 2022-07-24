---
title: 谷歌浏览器SwitchyOmega插件安装使用教程
date: 2021-03-10 21:12:40
categories: 开发工具
---
SwitchyOmega是Chrome浏览器上的代理扩展程序,可以轻松快捷的管理和切换多个代理设置。接管浏览器代理方式，可瞬间切换代理和本地连接方式，配合socks5（等其他代理工具）可实现只代理部分国内无法访问的网站。

![](https://upload-images.jianshu.io/upload_images/10024246-61233ce7d20819e9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 安装方法

1、在线安装

在可以访问外网的环境下，从Chrome应用商店来安装：[https://chrome.google.com/webstore/detail/padekgcemlokbadohgkifijomclgjgif](https://chrome.google.com/webstore/detail/padekgcemlokbadohgkifijomclgjgif)

2、离线安装

去github下载最新版安装包：[https://github.com/FelisCatus/SwitchyOmega/releases](https://github.com/FelisCatus/SwitchyOmega/releases)

> 使用方式

1、新建一个情景模式

![谷歌浏览器SwitchyOmega插件安装使用教程](https://upload-images.jianshu.io/upload_images/10024246-3937342562f3571e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上图中，前两种是最常用的，如果是第一次使用，那就记住前两种即可，第一种是指定代理的方式，每次访问一定会从设置的代理中走，第二种是动态切换的方式，比如说你想让百度走国内的网络，而让谷歌走国外的网络。

2、配置情景模式

![谷歌浏览器SwitchyOmega插件安装使用教程](https://upload-images.jianshu.io/upload_images/10024246-27088b3d66156959.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我这里使用的是SSR的默认代理端口1080做演示，大家根据自己的代理工具来配置即可，注意“代理协议”别选错了。

3、配置自动切换

![谷歌浏览器SwitchyOmega插件安装使用教程](https://upload-images.jianshu.io/upload_images/10024246-312d117ca68260d4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

前两步完成之后，就已经可以了，但是每次使用的时候，需要在浏览器上手动点一下来切换（不是之前提到的浏览器代理设置），而这一步配置了自动切换以后，会自动根据域名来匹配情景模式。
