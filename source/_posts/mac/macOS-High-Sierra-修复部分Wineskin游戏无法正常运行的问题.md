---
title: macOS-High-Sierra-修复部分Wineskin游戏无法正常运行的问题
date: 2021-03-10 21:12:40
categories: mac
---
之前，有很多朋友留言小子因为系统升级到了最新macOS High Sierra而无法打开博客下载的Wineskin移植游戏了。
相信所有升级到macOS High Sierra的朋友运行wineskin wrapper移植的游戏都会遇到这个问题。不过，小子经过一番搜索，终于找到了解决办法，测试有效，现在分享给大家。
![](https://upload-images.jianshu.io/upload_images/10024246-d046d690278a285e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
小子简单说明下具体步骤（这里以“太阁立志传 V”为例）：
1.右键“太阁立志传 V.app”显示包内容，双击打开“Wineskin.app”，点击Advanced—Tools；
2.先点击“kill Wineskin Processes”, 关闭所有进程；
![](https://upload-images.jianshu.io/upload_images/10024246-1e2e98550a078858.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3.点击最下方中间的“Set Screen Options”,将“Use Mac Driver instead of X11”勾上 将“Auto Detect GPU infor for Direct3D”的勾选去掉，点击“done”；
![](https://upload-images.jianshu.io/upload_images/10024246-1c32a5f16dd885e3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

4.回到tools菜单，点击“Winetricks”，搜索关键字“video”，展开“settings”，勾选第二个“videomemorysize=2048”，点击“Run”确认wrapper已经更改设置，关闭Wine；
![](https://upload-images.jianshu.io/upload_images/10024246-c0701d46eaa676bb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
5.Have done！
