---
title: >-
  关于 webpack-cli4.0 及以上版本 和 webpack-dev-server 之间的依赖报connot find module
  “webpack-cli/bin/config-yargs”
date: 2021-03-15 13:15:56
categories: webpack
---
今天在执行webpack-dev-server命令时，一直报Error: Cannot find module ‘webpack-cli/bin/config-yargs’
在执行npm run dev命令时报：
```
connot find module “webpack-cli/bin/config-yargs”
```
![](https://upload-images.jianshu.io/upload_images/10024246-6c0ce76392782d11.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 方案一
可以通过对webpack-cli进行降级处理，成功解决问题。
步骤：
一：
```
npm uninstall webpack-cli   //这是进行局部删除webpack-cli脚手架
```
二;
```
npm install webpack-cli@3.3.11    //@后面的数字指定不同的版本，版本应该低于4.0
```
## 方案二
![](https://upload-images.jianshu.io/upload_images/10024246-8e6a60a3309046c2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在 webpack 官网上可以看到依赖的下载与设置方法没有改变，启动命令发生了改变。
直接 webpack serve --open，就可以了。

依赖关系

|依赖|版本|新版本|
|--|--|--|
|webpack|	4	|5|
|webpack-cli	|3	|4|
|webpack-dev-server|	3|	3|

之前的依赖关系是 webpack-dev-server 依赖于 webpack-cli 内部的 config-yargs，cli 升级到 4.0 以后，统一了 webpack 命令的入口文件，反过来依赖 webpack-dev-server 来实现 webpack serve --open 的命令。

原文链接：https://blog.csdn.net/weixin_40599109/article/details/109582365

