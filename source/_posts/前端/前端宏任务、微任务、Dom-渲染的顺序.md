---
title: 前端宏任务、微任务、Dom-渲染的顺序
date: 2021-06-10 16:28:03
category: javascript
---
## 1.浏览器包含多个进程
 -  1.主进程
协调控制其他子进程（创建、销毁）
- 2.第三方插件进程
每种类型的插件对应一个进程，仅当使用该插件时才创建
- 3.GPU 进程
用于 3D 绘制等
- 4.渲染进程，就是我们说的浏览器内核（最重要）
负责页面渲染，脚本执行，事件处理等
每个 tab 页一个渲染进程
## 2.渲染进程包含了多个线程：
- 1.JS 引擎线程
负责处理解析和执行 javascript 脚本程序
只有一个 JS 引擎线程（单线程）
与 GUI 渲染线程互斥，防止渲染结果不可预期
- 2.GUI 渲染线程
负责渲染页面，布局和绘制
页面需要重绘和回流时，该线程就会执行
与 js 引擎线程互斥，防止渲染结果不可预期
- 3.http 请求线程
浏览器有一个单独的线程用于处理 AJAX 请求
- 4.事件处理线程（鼠标点击、ajax 等）
用来控制事件循环（鼠标点击、setTimeout、ajax 等）
- 5.定时器触发线程
setInterval 与 setTimeout 所在的线程
## 3. 为什么 JS 引擎线程和 GUI 渲染线程是互斥的？
JavaScript 是可操纵 DOM 的，如果在修改这些元素属性同时渲染界面,那么渲染线程前后获得的元素数据就可能不一致了。因此为了防止渲染出现不可预期的结果，浏览器设置 GUI 渲染线程与 JS 引擎为互斥的关系，当 JS 引擎执行时 GUI 线程会被挂起，GUI 更新则会被保存在一个队列中等到 JS 引擎线程空闲时立即被执行。
## 4. 为什么 JS 会阻塞页面加载？
从上面的互斥关系可以推导出，JS 如果执行时间过长就会阻塞页面。譬如，假设 JS 引擎正在进行巨量的计算，此时就算 GUI 有更新，也会被保存到队列中，等待 JS 引擎空闲后执行。然后，由于巨量计算，所以 JS 引擎很可能很久很久后才能空闲，自然会感觉到巨卡无比。所以，要尽量避免 JS 执行时间过长，这样就会造成页面的渲染不连贯，导致页面渲染加载阻塞的感觉。
## 5. JS 引擎线程和 GUI 渲染线程是互斥的，那先后顺序呢？
把下面三段代码放到浏览器的控制台执行：
```
document.body.style = "background:black";
document.body.style = "background:red";
document.body.style = "background:blue";
document.body.style = "background:grey";
```
- 结果：背景直接变成灰色
- 分析：Call Stack 清空的时候，执行，执行到了 document.body.style = 'background:grey';这时，前面的代码都被覆盖了，此时 dom 渲染，背景色是灰色
```
document.body.style = "background:blue";
console.log(1);
Promise.resolve().then(function () {
  console.log(2);
  document.body.style = "background:black";
});
console.log(3);
```
- 结果：背景直接变成黑色
- 分析：document.body.style = 'background:blue'是同步代码，document.body.style = 'background:black'是微任务，此时微任务执行完，才会进行 dom 渲染，所以背景色是黑色
```
document.body.style = "background:blue";
setTimeout(function () {
  document.body.style = "background:black";
}, 0);
```
- 结果：背景先一闪而过蓝色，然后变成黑色
- 分析：document.body.style = 'background:blue';是同步代码，document.body.style = 'background:black'是宏任务，所以 dom 在同步代码执行完，宏任务执行之前会渲染一次。然后宏任务执行完又会渲染一次。2 次渲染，所以才会呈现背景先一闪而过蓝色，然后变成黑色，这种效果。

总结：
>1.先把Call Stack清空
2.然后执行当前的微任务
3.接下来DOM渲染
微任务在dom渲染`之前`执行，宏任务在dom渲染`之后`执行。
