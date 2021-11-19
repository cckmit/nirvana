---
title: Chrome 新功能 - 录制小视频
date: 2021-11-15 16:13:30
category: javascript
---

Chrome 97 推出了一个预览功能 - Recorder。它允许你录制 Web 页面的操作并支持**回放，编辑，测量性能** 等诸多功能。

## 它长什么样

你可以直接在 chrome devtool 中看到一个 Recorder 面板，点击它就可以体验。

> 如果没有找到，可以尝试 cmd + shift + p 调出命令面板搜索 Recorder。当然如果该功能未发布是搜不到的

![](https://upload-images.jianshu.io/upload_images/10024246-ef6dcda0a83f5a08.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 它有什么用？

通过它，你可以实现一些有趣的功能。

比如：

*   测试同学录制一段“视频”， 然后发送给开发，开发根据这段视频定位问题。
*   测试某一个业务流程在各种不同的网络和硬件环境下的表现，甚至你可以看其在不同平台的表现（比如 PC，手机，平板等）。

![](https://upload-images.jianshu.io/upload_images/10024246-871ee10a4f69af27.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   自动化测试。你可以录制一段视频，然后通过修改其中部分参数的形式来自动化生成很多测试用例。

![](https://upload-images.jianshu.io/upload_images/10024246-71427d1840828d76.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   。。。

由于是预览版，因此最终是什么样可能还不确定。

## 大招

对于我来说，我想要到一个比较有意思的功能。

我们知道 Chrome 的 devtool frontend（就是你看到的开发者工具） 是开源的，代码托管在 Github：[https://github.com/ChromeDevT...](https://developer.chrome.com/docs/devtools/recorder/)

因此你可以直接集成它到你的项目中。比如你可以开发一个调试工具，这个工具 fork 一下 devtool frontend，然后修改 Recoreder 部分的源码，使得用户可以手动上报自己的录像，然后你将用户的录像数据，网络数据等其他数据发送到你的后端进行分析。

如果你的公司有做用户错误上报或者信息收集的需求，不妨考虑一下是否可以为你所用。
