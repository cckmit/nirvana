---
title: Android WebView的 onPause()和 pauseTimers()
date: 2021-12-21 21:12:40
categories: android
---
onPause()和pauseTimers()在Android平台的浏览器应用中，应该经常用到。与其想对应的还有onResume()和resumeTimers()。

场景：两个H5页面，H1,H2，H2里面有一个按钮与JS交互，第一种情况：先进入H1页面，返回后在进入H2页面，点击按钮无响应，第二种情况：先进如H2页面，点击按钮正常，返回进入H1，在重新进入H2，点击无反应。

针对这种情况，发现导致以上这两种情况的原因是WebView属性设置错误导致的。

- `**webView.onResume()**`：//激活webview为活跃状态，能正常执行网页的响应
- `**webView.onPause()**`://当页面被失去焦点被切换到后台不可见时，需要执行onPause(),通知内核暂停所有的动作，比如DOM的解析、plugin的执行、JS的执行
- `**webView.onpauseTimers()**`://当应用程序(存在webview)被切换到后台时，这个方法不仅仅针对当前页面内的webview而是整个应用程序中所有页面的webview,它会暂停所有webview的layout，parsing，javascripttimer。降低CPU功耗
- `**webView.resumeTimers()**`://恢复pauseTimers状态

看了这几个属性的注释，恍然大悟，赶紧检查代码，果真如此，在H1页面的onPause()方法中设置了webView.onpauseTimers(),导致只要进入H1在返回就会调用这个属性，导致全局的WebView都无法响应。
