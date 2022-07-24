---
title: Ajax 之战：XMLHttpRequest 与 Fetch API
date: 2022-04-27 16:28:03
categories: 前端
---
![](https://upload-images.jianshu.io/upload_images/10024246-84969a982834239c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

***本文最初发布于 OpenReplay 博客，由 InfoQ 中文站翻译并分享。***

Ajax 是大多数 web 应用程序背后的核心技术，它允许页面向 web 服务发出异步请求，因此数据可以不经过页面往返服务器无刷新显示数据。

术语 Ajax 不是一种技术，相反，它指的是从客户端脚本加载服务器数据的方法。多年来已经引入了几种选择，目前有两种主要方法，大多数 JavaScript 框架使用其中一种或两种。

在本文中，我们将研究早期 XMLHttpRequest 和现代 Fetch 的优缺点，以确定哪种 Ajax API 最适合你的应用。

## XMLHttpRequest

XMLHttpRequest 在 1999 年首次作为非标准的 Internet Explorer 5.0 ActiveX 组件出现，微软开发它是为了支持基于浏览器的 Outlook 版本，XML 是当时最流行（或被宣扬）的数据格式，除此之外，XMLHttpRequest 还支持文本和尚未发明的 JSON。

Jesse James Garrett 在他 2005 年的文章《AJAX: Web 应用程序的新方法》中提出了“AJAX”概念，那时谷歌邮箱和谷歌地图等基于 AJAX 的应用程序已经存在，但是这个术语激励了开发人员，并引起了流畅的 Web 2.0 体验爆炸式增长。

AJAX 是“Asynchronous JavaScript and XML”的缩写，尽管严格地说，开发人员并不需要使用异步方法、JavaScript 或 XML。我们现在将通用的“Ajax”术语表示任何从服务器获取数据、更新 DOM 而无需刷新整个页面的客户端过程。

所有主流浏览器都支持 XMLHttpRequest，并在 2006 年成为官方的 web 标准。下面是一个简单的例子，从你的域 / 服务 / 端点获取数据，然后在控制台将 JSON 结果显示为文本：

```
const xhr = new XMLHttpRequest();
xhr.open("GET", "/service");

// state change event
xhr.onreadystatechange = () => {
  // is request complete?
  if (xhr.readyState !== 4) return;

  if (xhr.status === 200) {
    // request successful
    console.log(JSON.parse(xhr.responseText));
  } else {
    // request not successful
    console.log("HTTP error", xhr.status, xhr.statusText);
  }
};

// start request
xhr.send();
```

onreadystatechange 回调函数在请求的生命周期中运行好几次；XMLHttpRequest 对象的 readyState 属性则返回当前状态：

*   0 (uninitialized） - 请求未初始化

*   1（loading）- 服务器连接建立

*   2（loaded）- 请求收到

*   3（interactive）- 处理请求

*   4（complete）- 请求完成，响应准备就绪

在达到状态 4 之前，几个函数就可以做很多事情。

## Fetch

Fetch 是一个现代基于 promise 的 Ajax 请求 API，首次出现于 2015 年，在大多数浏览器中都得到了支持。它不是基于 XMLHttpRequest 构建的，并且用更简洁的语法提供了更好的一致性。下面的 Promise 链函数与上面的 XMLHttpRequest 例子相同：

```
fetch("/service", { method: "GET" })
  .then((res) => res.json())
  .then((json) => console.log(json))
  .catch((err) => console.error("error:", err));
```

或者你可以使用 async/await：

```
try {
  const res = await fetch("/service", { method: "GET" }),
    json = await res.json();

  console.log(json);
} catch (err) {
  console.error("error:", err);
}
```

Fetch 更清晰、更简洁，并且经常在 Service worker 中使用。


## 第 1 回合：Fetch 获胜

与陈旧的 XMLHttpRequest 相比，Fetch API 除了具有更清晰简洁的语法之外，还有其它几个优势。

##### 头、请求和响应对象

上面简单 fetch() 示例中，使用一个字符串定义 URL 端点，也可以传递一个可配置的 Request 对象，它提供了有关调用的一系列属性：

```
const request = new Request("/service", { method: "POST" });

console.log(request.url);
console.log(request.method);
console.log(request.credentials);

// FormData representation of body
const fd = await request.formData();

// clone request
const req2 = request.clone();

const res = await fetch(request);
```

Response 对象提供了对访问所有详细信息的类似访问：

```
console.log(res.ok); // true/false
console.log(res.status); // HTTP status
console.log(res.url);

const json = await res.json(); // parses body as JSON
const text = await res.text(); // parses body as text
const fd = await res.formData(); // FormData representation of body
```

Headers 对象提供了一个简单的接口来设置请求中的头信息或获取响应中的头信息：

```
// set request headers
const headers = new Headers();
headers.set("X-Requested-With", "ajax");
headers.append("Content-Type", "text/xml");

const request = new Request("/service", {
  method: "POST",
  headers,
});

const res = await fetch(request);

// examine response headers
console.log(res.headers.get("Content-Type"));
```

##### 缓存控制

在 XMLHttpRequest 中管理缓存具有挑战性，你可能会发现有必要附加一个随机查询字符串值来绕过浏览器缓存，Fetch 方法在第二个参数 init 对象中内置了对缓存的支持：

```
const res = await fetch("/service", {
  method: "GET",
  cache: "default",
});
```

缓存可以设置为：

*   'default' —— 如果有一个新的 (未过期的) 匹配，则使用浏览器缓存；如果没有，浏览器会发出一个带条件的请求来检查资源是否已改变，并在必要时会发出新的请求

*   'no-store' —— 绕过浏览器缓存，并且网络响应不会更新它

*   'reload' —— 绕过浏览器缓存，但是网络响应会更新它

*   'no-cache' —— 类似于'default'，除了一个条件请求总是被做

*   'force-cache' —— 如果可能，使用缓存的版本，即使它过时了

*   'only-if-cached' —— 相同的 force-cache，除了没有网络请求

##### 跨域控制

跨域共享资源允许客户端脚本向另一个域发出 Ajax 请求，前提是该服务器允许 Access-Control-Allow-Origin 响应头中的源域；如果没有设置这个参数， fetch() 和 XMLHttpRequest 都会失败。但是，Fetch 提供了一个模式属性，可以在第二个参数的 init 对象中设置‘no-cors’属性。

```
const res = await fetch(
  'https://anotherdomain.com/service', 
  {
    method: 'GET',
    mode: 'no-cors'
  }
);
```

这将返回一个不能读取但可以被其它的 API 使用的响应。例如，你可以使用 Cache API 存储返回再之后使用，可能从 Service Worker 返回一个图像、脚本或 CSS 文件。

##### 凭证控制

XMLHttpRequest 总是发送浏览器 cookie，Fetch API 不会发送 cookie，除非你显式地在第二个参数 init 对象中设置 credentials 属性。

```

const res = await fetch("/service", {
  method: "GET",
  credentials: "same-origin",
});
```

credentials 可以设置为：

*   'omit'  —— 排除 cookie 和 HTTP 认证项 (默认)

*   'same-origin'  —— 包含对同源 url 的请求的凭证

*   'include'  —— 包含所有请求的凭证

请注意，include 是早期 API 实现中的默认值，如果你的用户可能运行旧的浏览器，就得显式地设置 credentials 属性。

##### 重定向控制

默认情况下，fetch() 和 XMLHttpRequest 都遵循服务器重定向。但是，fetch() 在第二个参数 init 对象中提供了替代选项：

```
const res = await fetch("/service", {
  method: "GET",
  redirect: "follow",
});
```

redirect 可以设置为：

*   'follow' —— 遵循所有重定向（默认）

*   'error' —— 发生重定向时中止（拒绝）

*   'manual' —— 返回手动处理的响应

##### 数据流

XMLHttpRequest 将整个响应读入内存缓冲区，但是 fetch() 可以流式传输请求和响应数据，这是一项新技术，流允许你在发送或接收时处理更小的数据块。例如，你可以在完全下载前处理数兆字节文件中的信息，下面的示例将传入的（二进制）数据块转换为文本，并将其输出到控制台。在较慢的连接上，你会看到更小的数据块在较长的时间内到达。

```
const response = await fetch("/service"),
  reader = response.body
    .pipeThrough(new TextDecoderStream())
    .getReader();

while (true) {
  const { value, done } = await reader.read();
  if (done) break;
  console.log(value);
}
```

##### 服务器端支持

Deno 和 Node 18 中完全支持 Fetch，在服务器和客户端使用相同的 API 有助于减少认知成本，还提供了在任何地方运行的同构 JavaScript 库的可能性。

## 第二轮：XMLHttpRequest 获胜

尽管存在缺陷，XMLHttpRequest 还是有一些技巧可以超越 ajax Fetch()。

##### 进度支持

我们可以监控请求的进度，通过将一个处理程序附加到 XMLHttpRequest 对象的进度事件上。这在上传大文件（如照片）时特别有用：

```
const xhr = new XMLHttpRequest();

// progress event
xhr.upload.onprogress = (p) => {
  console.log(Math.round((p.loaded / p.total) * 100) + "%");
};
```

事件处理程序传递的对象有三个属性：

1.  lengthComputable —— 如果进度可以计算，则设置为 true

2.  total —— 消息体的工作总量或内容长度

3.  loaded —— 到目前为止完成的工作或内容的数量

Fetch API 没有提供任何方法来监控上传进度。

##### 超时支持

XMLHttpRequest 对象提供了一个 timeout 属性，可以将其设置为请求自动终止前允许运行的毫秒数；如果超时，就触发一个 timeout 事件来处理：

```
const xhr = new XMLHttpRequest();
xhr.timeout = 5000; // 5-second maximum

```

fetch() 中可以封装一个函数来实现超时功能：

```
function fetchTimeout(url, init, timeout = 5000) {
  return new Promise((resolve, reject) => {
    fetch(url, init).then(resolve).catch(reject);
    setTimeout(reject, timeout);
  });
}
```

或者，你可以使用 Promise.race()：

```
Promise.race([
  fetch("/service", { method: "GET" }),
  new Promise((resolve) => setTimeout(resolve, 5000)),
]).then((res) => console.log(res));
```

这两个方法都不容易使用，另外请求将在后台继续运行。

##### 中止支持

运行中的请求可以通过 XMLHttpRequest 的 abort() 方法取消，如有必要，可以附加一个 abort 事件来处理：

```
const xhr = new XMLHttpRequest();
xhr.open("GET", "/service");
xhr.send();

// ...

xhr.onabort = () => console.log("aborted");
xhr.abort();
```

你可以中止一个 fetch()，但它不是那么直接，需要一个 AbortController 对象：

```
const controller = new AbortController();

fetch("/service", {
  method: "GET",
  signal: controller.signal,
})
  .then((res) => res.json())
  .then((json) => console.log(json))
  .catch((error) => console.error("Error:", error));

// abort request
controller.abort();
```

当 fetch() 中止时，catch() 块执行。

##### 更显式的故障检测

当开发人员第一次使用 fetch() 时，假设一个 HTTP 错误，如 404 Not Found 或 500 Internal Server error 将触发 Promise 拒绝并运行相关的 catch() 块，这似乎是合乎逻辑的，但事实并非如此：Promise 成功地解决了这些响应，只有当网络没有响应或请求被中断时，才会发生拒绝。

fetch() 的 Response 对象提供了 status 和 ok 属性，但并不总是显式地需要检查它们，XMLHttpRequest 更明确，因为单个回调函数处理每一个结果：你应该在每个示例中都看到 stuatus 检查。

##### 浏览器支持

我希望你不必支持 Internet Explorer 或 2015 年之前的浏览器版本，但如果是这样的话，XMLHttpRequest 是你唯一的选择。XMLHttpRequest 也很稳定的，API 不太可能更新。Fetch 比较新，还缺少几个关键特性，虽然更新不太可能破坏代码，但你可以期待一些维护。

## 应该使用哪个 API ?

大多数开发人员都会使用更新的 Fetch API，它的语法更简洁，比 XMLHttpRequest 更有优势；也就是说，这些好处中的许多都有特定的用例，但在大多数应用程序中都不需要它们。只有两种情况下 XMLHttpRequest 仍必不可少：

1.  你正在支持非常老的浏览器——这种需求会随着时间的推移而下降。

2.  你需要显示上传进度条。Fetch 后续将会支持，但可能需要几年的时间。

这两种选择都很有趣，值得详细了解它们！

>**原文链接：**
https://blog.openreplay.com/ajax-battle-xmlhttprequest-vs-the-fetch-api
