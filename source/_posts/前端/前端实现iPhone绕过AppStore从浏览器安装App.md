---
title: 前端实现iPhone绕过AppStore从浏览器安装App
date: 2021-10-27 16:13:30
category: javascript
---
## 背景

都知道 `iPhone` 苹果手机应用只能通过 `AppStore` 进行安装，测试包只能通过官方提供的 `TestFlight` 等工具安装，而且通常有较长的审核流程，无法及时更新安装包，非常不方便。本文主要介绍前端实现对签名成功的 `App`直接通过浏览器下载安装，开发者可以及时提供测试 `App`。

## 主要流程

*   前提条件，苹果 `App` 必须签名成功，这一步由 `iOS` 应用开发者完成。
*   上传到服务器，获得信息和下载地址，得到两个文件，一个是 `plist` 文件和 `ipa` 文件，及 `app` 图标。
*   通过访问 `plist` 文件来达到下载 `ipa` 文件和图片的目的，使用了苹果 `safari` 浏览器自带协议，用a标签或者 `window.open` 方式打开 `plist` 地址。
*   信任设备并安装。

```itms-services:///?action=download-manifest&url=一个https地址```

下面是几个过程的具体实现

## 具体实现

### 上传资源到服务器

公司文件可部署到公司服务器，自己测试文件可以使用 `github` 等免费提供文件地址的服务。

*   `ipa`：需要安装的苹果 `App` 打包文件，由 `iOS` 客户端提供；
*   `logo`：图片格式的 `App` 图标；
*   `plist`：`App` 下载配置文件。

<br/>

### 由客户端生成 `plist` 文件

`📃` `app.plist`：由客户端配置或更改下面 `ipa` 下载地址、`App` 图标地址及 `App` 描述信息。

```
<?xml version="1.0" encoding="UTF-8"?>
<! DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>items</key>
  <array>
    <dict>
      <key>assets</key>
      <array>
        <dict>
          <key>kind</key>
          <string>software-package</string>
          <key>url</key>
          <string>https://ipa 下载地址</string>
        </dict>
        <dict>
          <key>kind</key>
          <string>display-image</string>
          <key>needs-shine</key>
          <true/>
          <key>url</key>
          <string>https://app 图标地址</string>
        </dict>
      </array>
      <key>metadata</key>
      <dict>
        <key>bundle-identifier</key>
        <string>com.xxxx.xxxx.xxxx</string>
        <key>bundle-version</key>
        <string>0.1.0</string>
        <key>kind</key>
        <string>software</string>
        <key>title</key>
        <string>APP名称</string>
        <key>subtitle</key>
        <string>App描述</string>
      </dict>
    </dict>
  </array>
</dict>
</plist>
```

### 下载页面

`📃` `install.html`：提供给用户的下载 `html` 页面，具体 `样式` 和 `功能`可根据自己的需求调整。

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
</head>
<body>
  <button id="button">下载</button>
  <script> document.getElementById('button').addEventListener('click', function() {
      window.open('itms-services:///?action=download-manifest&url=https://pan.xchjw.cn/download/app/CorpPrivateInstall.plist', '_self')
    })</script>
</body>
</html>
```

## 实现效果

将下载地址提供给需要的人，点击下载按钮即可实现 `App` 安装。

![](https://upload-images.jianshu.io/upload_images/10024246-c6136411cce93542.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 市场上很多的分发平台，如蒲公英就是这么做的。

## 注意：

*   只可在苹果 `safari浏览器` 中实现下载，其他浏览器中打开可做一些引导提示。
*   需要注意的是从 `ios7.1` 开始，`http` 推送 `plist` 已经不能用了，只能使用 `https` 推送，因此访问这个文件的地址必须是 `https` 开头的。你可以配置自己的服务器支持 `https` 服务，也可以借助第三方工具。

## 其他第三方app托管下载服务

我们不必这么麻烦自己部署这么多文件，完全可以借助第三方应用内测分发平台，比较出名的有下面几个：

*   [fir.im](https://www.betaqr.com/)：免费应用内测托管平台，`iOS` 应用 `Beta测试` 分发，`Android` 应用内测分发
*   [蒲公英](https://www.pgyer.com/)：免费的应用托管平台，`App` 应用众测分发。
*   [TestFlight Beta Testing](https://developer.apple.com/testflight/)：苹果官方测试平台工具。

> 文章地址：[https://segmentfault.com/a/11...](https://segmentfault.com/a/1190000040846993) 作者：dragonir
