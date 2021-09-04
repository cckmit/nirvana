---
title: 5个Chrome调试混合应用的技巧
date: 2021-06-10 16:28:03
category: javascript
---
对前端开发人员来说，Chrome 真是一个必备的开发工具，大到页面展示，小到 BUG 调试/HTTP 抓包等，本文我将和大家分享自己做混合应用开发过程中经常用到的几个调试技巧。

## 一、调试安卓应用

在进行混合应用开发过程中，经常需要在安卓应用中调试 H5 项目的代码，这里我们就需要了解安卓应用如何在 Chrome 上进行调试。
接下来简单介绍一下，希望大家还是能实际进行调试看看：

### 1\. 准备工作

需要准备有一下几个事项：

1.  安卓包必须为可调试包，如果不可以调试，可以找原生的同事提供；
2.  安卓手机通过数据线连接电脑，然后开启“开发者模式”，并启用“USB 调试”选项。

### 2\. Chrome 启动调试页面

在 Chrome 浏览器访问“`chrome://inspect/#devices`”，然后在 WebView 列表中选择你要调试的页面，点击“ `Inspect` ”选项，跟调试 PC 网页一样，使用 Chrome 控制台进行调试。
![](https://upload-images.jianshu.io/upload_images/10024246-e88cd12fba276e5f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后就可以正常进行调试了，操作和平常 Chrome 上面调试页面是一样的。
![](https://upload-images.jianshu.io/upload_images/10024246-52aee71f3e48e090.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 3\. 注意

如果访问 “`chrome://inspect/#devices`” 页面会一直提示 404，可以在翻墙情况下，先在 Chrome 访问 `[https://chrome-devtools-frontend.appspot.com](https://chrome-devtools-frontend.appspot.com)`，然后重新访问“`chrome://inspect/#devices`”即可。

## 二、筛选特定条件的请求

在 Network 面板中，我们可以在 Filter 输入框中，通过各种筛选条件，来查看满足条件的请求。

1.  使用场景：

如只需要查看失败或者符合指定 URL 的请求。

1.  使用方式：

在 Network 面板在 Filter 输入框中，输入各种筛选条件，支持的筛选条件包括：文本、正则表达式、过滤器和资源类型。
这里主要介绍“过滤器”，包括：
![](https://upload-images.jianshu.io/upload_images/10024246-89ef17218a8cd199.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这里输入“-”目的是为了让大家能看到 Chrome 提供哪些高级选项，在使用的时候是不需要输入“-”。
如果输入“-.js -.css”则可以过滤掉“.js”和“.css”类型的文件。
关于过滤器更多用法，可以阅读[《Chrome DevTools: How to Filter Network Requests》](https://www.freecodecamp.org/news/chrome-devtools-network-tab-tricks/)
![](https://upload-images.jianshu.io/upload_images/10024246-955122114a5f6372.gif?imageMogr2/auto-orient/strip)

## 三、快速断点报错信息

在 Sources 面板中，我们可以开启异常自动断点的开关，当我们代码抛出异常，会自动在抛出异常的地方断点，能帮助我们快速定位到错误信息，并提供完整的错误信息的方法调用栈。
![](https://upload-images.jianshu.io/upload_images/10024246-83b5438aa1d331ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


1.  使用场景：

需要调试抛出异常的情况。

1.  使用方式：

在 Sources 面板中，开启异常自动断点的开关。
![](https://upload-images.jianshu.io/upload_images/10024246-0dda76f57e6430ce.gif?imageMogr2/auto-orient/strip)



## 四、断点时修改代码

在 Sources 面板中，我们可以在需要断点的行数右击，选择“Add conditional breakpoint”，然后在输入框中输入表达式（如赋值操作等），后面代码将使用该结果。
![](https://upload-images.jianshu.io/upload_images/10024246-f672b773b57c1f9c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-2593f1db9cc330a0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1.  使用场景：

需要在调试时，方便手动修改数据来完成后续调试的时候。

1.  使用方式：

在 Sources 面板中，在需要断点的行数右击，选择“Add conditional breakpoint”。
![](https://upload-images.jianshu.io/upload_images/10024246-1977e3fd477b5754.gif?imageMogr2/auto-orient/strip)



## 五、自定义断点（事件、请求等）

当我们需要进行自定义断点的时候，比如需要拦截 DOM 事件、网络请求等，就可以在 Source 面板，通过 XHR/fetch Breakpoints 和 Event Listener Breakpoints 来启用对应断点。
![](https://upload-images.jianshu.io/upload_images/10024246-34615f95e0f52238.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


1.  使用场景：

需要在调试时，需要增加自定义断点时（如需要拦截 DOM 事件、网络请求等）。

1.  使用方式：

在 Sources 面板中，通过 XHR/fetch Breakpoints 和 Event Listener Breakpoints 来启用对应断点。
![](https://upload-images.jianshu.io/upload_images/10024246-f1796180f42cf27b.gif?imageMogr2/auto-orient/strip)



## 六、学习资料

1.  [Chrome tips community](https://umaar.com/)
2.  [Chrome 开发者工具中文文档](https://www.css88.com/doc/chrome-devtools/)

>原文：https://segmentfault.com/a/1190000039898591
