---
title: 为什么-Promise-比setTimeout()-快？
date: 2021-06-10 16:28:03
categories: 前端
---
### 1.实验

我们来做个实验。哪个执行得更快:立即解决的 Promise 还是立即`setTimeout`(也就是0毫秒的setTimeout)?

```
Promise.resolve(1).then(function resolve() {
  console.log('Resolved!');
});

setTimeout(function timeout() {
  console.log('Timed out!');
}, 0);

// 'Resolved!'
// 'Timed out!'
```

`promise.resolve(1)`是一个静态函数，它返回一个立即解析的`promise`。`setTimeout(callback, 0)`以`0毫秒`的延迟执行回调函数。

我们可以看到先打印`'Resolved!'`，再打印`Timeout completed!`，立即解决的 promise 比立即`setTimeout`更快。

是因为`Promise.resolve(true).then(...)`在`setTimeout(..., 0)`之前被调用了，所以 Promise 过程会更快吗？ 公平的问题。

所以，我们稍微更改一下实验条件，然后先调用`setTimeout(..., 0)`：

```
setTimeout(function timeout() {
  console.log('Timed out!');
}, 0);

Promise.resolve(1).then(function resolve() {
  console.log('Resolved!');
});

// 'Resolved!'
// 'Timed out!'
```

`setTimeout(..., 0)`在`Promise.resolve(true).then(...)`之前被调用。但，还是先打印`Resolved!`在打印`'Timed out!'`。

这是为啥呢？

### 2.事件循环

与异步 JS 相关的问题可以通过研究事件循环来回答。我们回顾一下异步 JS 工作方式的主要组成部分。

![](https://upload-images.jianshu.io/upload_images/10024246-bdd30b5a8e150e74.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**调用堆栈**是一个LIFO(后进先出)结构，它存储在代码执行期间创建的执行上下文。简单地说，调用堆栈执行这些函数。

Web api是异步操作(fetch 请求、promise、计时器)及其回调等待完成的地方。

**task queue （任务队列）**是一个**FIFO(先进先出)**结构，它保存准备执行的异步操作的回调。例如，超时的`setTimeout()`的回调函数或准备执行的单击按钮事件处理程序都在任务队列中排队。

**job queue （作业队列）**是一个FIFO(先入先出)结构，它保存准备执行的`promise` 的回调。例如，已完成的承诺的`resolve`或`reject`回调被排在作业队列中。

最后，事件循环永久监听调用堆栈是否为空。如果调用堆栈为空，则事件循环查看作业队列或任务队列，并将准备执行的任何回调分派到调用堆栈中。

### 3.作业队列与任务队列

我们从事件循环的角度来看这个实验，我将对代码执行进行一步一步的分析。

A）调用堆栈执行`setTimeout(..., 0)`并计划一个计时器， `timeout()`回调存储在Web API中：

![](https://upload-images.jianshu.io/upload_images/10024246-ca506539625cb931.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/10024246-b8e64c7d0979fbc5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


B）调用堆栈执行 `Promise.resolve(true).then(resolve)`并安排一个 `promise` 解决方案。 `resolved()`回调存储在Web API中：

![](https://upload-images.jianshu.io/upload_images/10024246-ed54bf0de5592b0a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/10024246-4ee396b1fbd3abd2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


C)promise 立即被解析，同时计时器也立即执行。这样，定时器回调`timeout()`进入任务队列，`promise`回调`resolve()`进入作业队列

![](https://upload-images.jianshu.io/upload_images/10024246-64adac3cde08ca3b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

D）现在是有趣的部分：作业队列(微任务)优先级高于任务队列(宏任务)。 事件循环从作业队列中取出promise回调`resolve()`并将其放入调用堆栈中。 然后，调用堆栈执行promise回调`resolve()`：

![](https://upload-images.jianshu.io/upload_images/10024246-e48ec1e1277ee5ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


E）最后，事件循环将计时器回调`timeout()`从任务队列中出队到调用堆栈中。 然后，调用堆栈执行计时器回调`timeout()`：

![](https://upload-images.jianshu.io/upload_images/10024246-4a06eded3f3da72e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

调用堆栈为空，已完成脚本的执行。

### 总结

为什么立即解决的 promise 比立即执行定时器处理得更快？

由于事件循环优先级的存在，因此与任务队列（存储超时的`setTimeout()`回调）相比，作业队列（用于存储已实现的`Promise`回调）的优先级更高。


>原文： [https://dmitripavlutin.com/ja...](https://dmitripavlutin.com/javascript-promises-settimeout/)
