---
title: 简书图片403(hexo中简书图片迁移到阿里云oss)
date: 2022-03-29 16:28:03
categories: 前端
---
由于博客图片所有都是使用简书上的，因此可能访问的人一多或者怎样简书把个人域名拉入黑名单了，因此致使全部的图片都403了web

考虑到后面全部图片都会不能访问到，那么我就有迁移的想法了。

首先我要下载图片，下载图片就要获取全部文章中图片的连接，这个只须要cat和grep就能够作到，由于我全部的图片都是单独一行的，因此就少了不少乱七八糟的事情bash
```
cat ./* |grep upload-images.jianshu.io > image.txt工具
```
里面的内容相似阿里云
```
![](http://upload-images.jianshu.io/upload_images/3778244-21333b3b435f1d4f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/720)
![](http://upload-images.jianshu.io/upload_images/3778244-d01842b492115cc2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/720)
![](http://upload-images.jianshu.io/upload_images/3778244-ec7f62804aacc303.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/720)
![](http://upload-images.jianshu.io/upload_images/3778244-6cbc9cc1725d1c1b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/720)
![](http://upload-images.jianshu.io/upload_images/3778244-78ab82ccb6dcafd7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/720)
![](http://upload-images.jianshu.io/upload_images/3778244-469e30d2c22323d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/720)
![](https://upload-images.jianshu.io/upload_images/3778244-7f2077bd1e8a0123.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
这样image.txt中就有个人图片连接了。以后就是使用visual studio code的查找替换功能去掉头和尾部，接着使用wget下载图片spa
```
mkdir img3d

cd imgcode

wget -i ../image.txtcdn
```
下载完成以后全部的图片使用阿里云oss的上传工具oss browser去上传到oss上，oss新建bucket什么的这里就不讲了blog

接着就是连接的替换了，进入博客目录，输入下面的命令进行替换
```
sed -i "s/upload-images.jianshu.io\/upload_images\//bboysoul-web.oss-cn-hangzhou.aliyuncs.com\//" ./*
```
接着去掉后缀
```
sed -i "s/\?imageMogr2\/auto-orient\/strip\%7CimageView2\/2\/w\/720//" ./*

sed -i "s/\?imageMogr2\/auto-orient\/strip\%7CimageView2\/2\/w\/1240//" ./*
```
完成

以前使用简书写文章是由于它提供了相似图床的功能，很好，并且文章能够下载，仍是一个博客发布平台，今天居然出了这件事，因此各位博主要警戒第三方图床带来的危害，仍是使用本身的oss比较安全，最后推荐picgo这个工具，这个工具能够帮你自动上传图片到oss上，很方便