---
title: Navigator.sendBeacon-页面关闭也能发送请求
date: 2022-04-04 16:28:03
categories: 前端
---
## 背景
---
最近在需求中有一个这样的场景：`需要在页面关闭的时候，用户不需要操作，主动关闭当前订单`
当时考虑的方案：`在页面关闭的时候，向后端发送一个请求，将这个资源释放掉；`
定下方案时，觉得也不是什么难事，觉得谷歌浏览器应该会提供页面关闭的 API 供开发者使用。
经过查找，找到了这么两个 API ：` beforeunload` 和 `unload`
##beforeunload

>当浏览器窗口关闭或者刷新时，会触发 beforeunload 事件。当前页面不会直接关闭，可以点击确定按钮关闭或刷新，也可以取消关闭或刷新。
```
window.addEventListener('beforeunload', function (event) {
  // Cancel the event as stated by the standard.
  event.preventDefault();
  // Chrome requires returnValue to be set.
  event.returnValue = '';
});

```
该事件会使网页在`离开或者刷新`的时候弹出一个对话框，给用户一个提示。在这个弹框出现时，该页面是做不了任何操作的，除非把这个弹框关闭。其他页面也只能进行简单的`点击浏览操作`，键盘是操作不了的。

这个不符合用户无感知的条件

## unload

当文档或一个子资源正在被卸载时, 触发 unload 事件。

unload 事件在 beforeunload 事件后触发，这时候文档处于一个什么状态呢？
所有资源都存在，像图片，iframe的等，但是这些资源对于用户来说均不可见，界面上的交互也是无效的.
使用方式和 beforeunload 相同,但是 unload 事件中不能使用确认框，毕竟都已经在卸载了
```
window.addEventListener('unload', function(event) {
  console.log('unload');
});
```
## 问题
- 用户无论刷新还是关闭了页面，因为这两种操作都会调用`beforeunload `和 `unload`。
- 异步请求会被 cancel 掉，导致请求无法发送成功，由于使用的axios进行请求，浏览器有几率关闭异步请求，造成请求无法发出，可以尝试换成同步。
在事件的回调中使用同步的 AJAX 请求。
```
window.addEventListener('unload', function (event) {
  let xhr = new XMLHttpRequest();
  xhr.open('post', '/log', false);
  xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
  xhr.send('foo=bar');
});
```
但是谷歌浏览器已经不允许页面关闭期间进行同步的 `XMLHTTPRequest()`，这条规则适用于 `beforeunload`、`unload`、`pagehide`和`visibilitychange`这些 `API`;
为了确保页面在卸载时讲数据发送到服务器，官方建议使用 `sendBeacon() `或者 `Fetch keep-alive`
## [Navigator.sendBeacon](https://developer.mozilla.org/zh-CN/docs/Web/API/Navigator/sendBeacon)
>这个方法主要用于满足统计和诊断代码的需要，这些代码通常尝试在卸载（unload）文档之前向web服务器发送数据。
Beacon API 有以下这样几个特点：
- 通过 HTTP POST 将少量数据异步传输，可靠性好
- 这个请求不需要响应，保证在页面的 unload 状态从发起到完成之前被发送。
- 不会阻塞页面卸载,也就不会影响下一导航的载入
- 支持跨域
- 不支持自定义请求头
用法如下：

```
navigator.sendBeacon(url, data);

```

url 就是上报地址，data 可以是 `ArrayBufferView`，`Blob`，`DOMString` 或 `Formdata`，根据官方规范，需要 request header 为 [CORS-safelisted-request-header](https://fetch.spec.whatwg.org/#cors-safelisted-request-header)，在这里则需要保证 `Content-Type` 为以下三种之一：

*   `application/x-www-form-urlencoded`
*   `multipart/form-data`
*   `text/plain`

我们一般会用到 `DOMString` , `Blob` 和 `Formdata` 这三种对象作为数据发送到后端，下面以这三种方式为例进行说明。

*   DOMString

如果数据类型是 `string`，则可以直接上报，此时该请求会自动设置请求头的 `Content-Type` 为 `text/plain`。

```
const reportData = (url, data) => {
  navigator.sendBeacon(url, data);
};

```

*   Blob

如果用 `Blob` 发送数据，这时需要我们手动设置 `Blob` 的 MIME type，一般设置为 `application/x-www-form-urlencoded`。

```
const reportData = (url, data) => {
  const blob = new Blob([JSON.stringify(data), {
    type: 'application/x-www-form-urlencoded',
  }]);
  navigator.sendBeacon(url, blob);
};

```

*   Formdata

可以直接创建一个新的 `Formdata`，此时该请求会自动设置请求头的 `Content-Type` 为 `multipart/form-data`。

```
const reportData = (url, data) => {
  const formData = new FormData();
  Object.keys(data).forEach((key) => {
    let value = data[key];
    if (typeof value !== 'string') {
      // formData只能append string 或 Blob
      value = JSON.stringify(value);
    }
    formData.append(key, value);
  });
  navigator.sendBeacon(url, formData);
};

```

注意这里的 `JSON.stringify` 操作，服务端需要将数据进行 parse 才能得到正确的数据。
## Fetch keep-alive

当使用` fetch()` 方法时，如果把` keeplive` 设置为` true`，即便页面被终止请求也会保持连接。
```
window.onunload = function() {
  fetch('/analytics', {
    method: 'POST',
    body: "statistics",
    keepalive: true
  });
};
```
