---
title: Chrome浏览器出现“由贵单位管理”原因及解决去除方法
date: 2021-06-10 16:28:03
categories: 前端
---
最近有很多 `Chrome` 浏览器用户突然发现设置选项提示“`由贵单位管理`”，并且还可能在操作中心里弹出这个通知，或者在“**下载**”页面中出现“`您的浏览器由所属组织管理`”，或者在“**关于 Google Chrome（G）**”页面中出现“`您的浏览器受管理`”。如果是企业用户遇到这个通知可能还能理解但不少个人用户也遇到这种情况，使用的并非谷歌浏览器企业版。

如下图：
[![](https://upload-images.jianshu.io/upload_images/10024246-f4673d19e7b62e69.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://www.mobibrw.com/wp-content/uploads/2019/07/ChromeBeManaged.png) 

中文版 `Chrome` 如下图最后出现的选项，遇到这个问题的不仅仅是国内网友而是全球网友都遇到了，引起各种咨询后谷歌官方已经发布声明解释。

###### **谷歌GOOGLE在声明里表示：**

`由贵单位管理` 指的是由设备或者账户管理员例如企业管理器可以用来强制更改谷歌浏览器配置的企业级策略。例如可以直接通过远程方式向所有受控用户添加书签，当管理员有进行这类操作时那么就会显示由单位管理。

而家庭用户遇到这个问题**主要是第三方软件导致的**，这些第三方软件可能会修改谷歌浏览器企业策略也提示。但是这里本身弹出的通知应该是由您的组织管理，只是由于某些问题所有用户看到的提示都是由贵单位管理

###### **手动发现问题元凶**

既然谷歌官方说了家庭用户是因为第三方软件导致的，那么就来找出来！！

谷歌 `Chrome` 浏览器地址栏输入 `Chrome://policy` ，即可看到调用企业策略的第三方软件或者是用户主动安装的第三方扩展等。[![image](https://upload-images.jianshu.io/upload_images/10024246-2f6bb8012cf34555.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://www.mobibrw.com/wp-content/uploads/2019/07/ChromePolicy.png) 

AliWangWang Plug-In.......从名字上看很明显了，是阿里旺旺开启了这功能，除非这些软件明确告知用户设置的是什么企业策略以及用于什么目的，对于未说明就调用的不应该赋予权限。
>注意： 通过设置 chrome://flags/#show-managed-ui 为 Disabled 的做法属于掩耳盗铃，这个只是不显示而已，并不能解决问题。
