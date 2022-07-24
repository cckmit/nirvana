---
title: Unable to boot device due to insufficient system resources
date: 2021-03-11 21:12:40
categories: 前端
---

iOS模拟器当启动的模拟器数量过多时，会提示：
![](https://upload-images.jianshu.io/upload_images/10024246-e6efd02dc51166b2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
该问题是模拟器的内存超出最大限额，可以通过修改内存大小来启动更多的模拟器

sudo launchctl limit maxfiles 2000 unlimited
https://help.apple.com/simulator/mac/9.0/index.html#/dev8a5f2aa4e
