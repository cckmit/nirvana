---
title: MAC-蓝牙问题解决加强版(-Airdrop-找不到,-蓝牙找不到等-)
date: 2021-03-10 21:12:40
categories: mac
---
如果你电脑出现了比如经常丢链接, 或者Hand off 不工作, Airdrop 找不到的话, 先升级到10.11.2 系统, 应该能解决很多已存的Bug.

如果依然没有解决的话, 首先你可以先去删除bluetooth的配置文件. 
在

/Library/Preferences/

目录里. 

![](http://upload-images.jianshu.io/upload_images/10024246-5917f0bfaef81518.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "正在搜索“Preferences”.jpg")


按 Command-shift-G , 找到后搜索 blue 就行了. 

删除后重启, 蓝牙配置就会重置了.  也可以尝试重启的时候按住 Command-Option-p-r 三声后松手, 也就是俗称的PR大法. 清理PR缓存. 

--
