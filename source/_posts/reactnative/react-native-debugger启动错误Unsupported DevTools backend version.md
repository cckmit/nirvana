---
title: react-native-debugger启动错误Unsupported DevTools backend version
date: 2022-02-25 21:12:40
categories: 
    - [react-native]
    - [资讯]
---
![](https://upload-images.jianshu.io/upload_images/10024246-281c6fac9b68e2ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用`react-native-debugger`进行调试时提示这个错误是由于`react-native-debugger`应用中依赖的`react-devtools-core`和当前node_modules中的版本不匹配，针对`react-native>=62`版本解决步骤如下：
## 1、保证react-native app中的依赖升级到`4.14.0`
## 2、在项目的package.json中，添加以下内容：
```
"resolutions": {
     "react-devtools-core": "4.14.0"
  }
```
## 3、使用`yarn install`安装依赖
使用yarn可以保证安装的`react-devtools-core`版本一致，使用npm有时会出现`react-native`里的版本和外部版本不一致。
## 4、全局安装`react-devtools`
```
npm install react-devtools@4.14.0 -g
```
安装`react-devtools`时会同步安装`electron`，有可能会出现网络超时的情况，可以通过设置`.mpmrc`来进行安装
```
electron_mirror=https://npm.taobao.org/mirrors/electron/
```
## 5、重新打开`react-native-debugger`并运行项目