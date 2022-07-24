---
title: Mac应用程序无法打开或文件损坏的处理方法
date: 2021-03-11 21:12:40
categories: 前端
---

Mac应用程序无法打开或文件损坏的处理方法

## 方法一

大多数用户在安装 Mac 应用时，经常会遇到提示 “ XXX.app 已损坏，打不开。您应该将它移到废纸篓 ” 或 “ 打不开 XXX.app，因为它来自身份不明的开发者 ” ，如下图所示：

![](http://upload-images.jianshu.io/upload_images/10024246-acb847a54e1d04dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "1516261454857950.png")

![](http://upload-images.jianshu.io/upload_images/10024246-6dc056c4a2b86b69.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "1516261464940638.png")

  遇到这种情况，解决的方法很简单，步骤如下：

**1\. 打开系统偏好设置界面，进入安全性与隐私**

![屏幕快照 2018-01-18 下午2.54.11.jpg](http://upload-images.jianshu.io/upload_images/10024246-11dc656bdb44769d.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "1516261331253960.jpeg")

**2\. 点按左下角的锁头图标，解锁更改权限**

**3\. 将允许从以下位置下载的应用，更改为 “ 任何来源 ” ，然后再打开应用即可**

![](http://upload-images.jianshu.io/upload_images/10024246-d9101881c093b84a.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "1516261394740817.jpeg")

若没有“任何来源”这个选项（macOS Sierra 10.12及以上的用户可能会遇到），按以下步骤执行：

打开终端（Terminal.app）

拷贝粘贴 sudo spctl --master-disable，按回车键

输入你的账户密码，按回车键确认执行（该密码不会显示，直接输入即可）

![](http://upload-images.jianshu.io/upload_images/10024246-0b13724e32c58228.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 方法二

如安装应用时，出现 “ 打不开 XXX.app，因为它来自身份不明的开发者 ” ，并提示你的软件在什么位置什么时间下载了磁盘影像。如图所示：

![](http://upload-images.jianshu.io/upload_images/10024246-f6ffe536b22e1916.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "1522042275384344.png")

  遇到这种情况，方法如下：

**1、将软件放入“应用程序”后，点击软件 右键-打开 ，如图所示：**

![](http://upload-images.jianshu.io/upload_images/10024246-8a9f9c4ab31e0aeb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "1522044010422080.png")

**2、弹出以下对话框后，你打开即可。**

![](http://upload-images.jianshu.io/upload_images/10024246-1f7894dba90e9353.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "1522043712276473.png")
