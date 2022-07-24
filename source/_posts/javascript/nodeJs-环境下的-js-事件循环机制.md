---
title: nodeJs-环境下的-js-事件循环机制
date: 2021-06-10 16:28:03
categories: 前端
---
#### 1.NodeJs 的架构图

![](https://upload-images.jianshu.io/upload_images/10024246-4ec354e2fbd21df0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
解释：

*   1.Node Standard Library：Node.js 标准库，这部分是由 Javascript 编写的。使用过程中直接能调用的 API。例如模块 http、buffer、fs、stream 等
*   2.Node bindings：这里就是 JavaScript 与 C/C++ 连接的桥梁，前者通过 bindings 调用后者，相互交换数据。
*   3.最下面一层是支撑 Node.js 运行的关键，由 C/C++ 实现（比如：V8：Google 开源的高性能 JavaScript 引擎，使用 C++ 开发）

#### 2.libuv 引擎

![](https://upload-images.jianshu.io/upload_images/10024246-9e98b53716e6e88d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### libuv 专注于异步 I/O.是一个基于事件驱动的跨平台抽象层，封装了不同操作系统的一些底层特性，对外提供统一 API，Node.js的Event Loop 是基于libuv实现的

#### 3.node 环境下 js 是怎样运行的？

![](https://upload-images.jianshu.io/upload_images/10024246-d26f1132c8d83035.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   （1）V8 引擎解析 JavaScript 脚本。
*   （2）解析后的代码，调用 Node API。
*   （3）libuv 库负责 Node API 的执行。它将不同的任务分配给不同的线程，形成一个 Event Loop（事件循环），以异步的方式将任务的执行结果返回给 V8 引擎。
*   （4）V8 引擎再将结果返回给用户

#### 4.js 事件循环的阶段

[图片上传失败...(image-27f384-1630489922768)] 

*   1.timer：这个阶段执行 timer（setTimeout、setInterval）的回调
*   2.I/O callbacks 阶段：执行一些系统调用错误，比如网络通信的错误回调（tcp 错误）
*   3.idle，prepare 阶段：仅供 node 内部使用，忽略
*   4.poll 阶段：检索新的 I/O 事件;执行与 I/O 相关的回调（几乎所有情况下，除了关闭的回调函数，那些由计时器和 setImmediate() 调度的之外），其余情况 node 将在适当的时候在此阻塞。
*   5.check：setImmediate() 回调函数在这里执行
*   6.close callbacks：一些关闭的回调函数，如：socket.on('close', ...)

#### 5.process.nextTick 和 microtask 的特别

##### 都是独立于 Event Loop 之外的，它有一个自己的队列，当每个阶段完成后，如果存在 nextTick 队列，就会清空队列中的所有回调函数，并且`优先`于其他 microtask 执行。

#### 6.js 事件循环的顺序

*   ##### 宏任务：script(整体代码)，setInterval()，setTimeout()，setImmediate（Nodejs）， I/O, UI rendering

*   ##### 微任务：process.nextTick（Nodejs），Promises，Object.observe， MutationObserver

```
setTimeout(funciton(){console.log(1)});
setImmediate(function(){console.log(2)});
process.nextTick(function(){console.log(3)});
Promise.resolve().then(function(){console.log(4)});
(function() {console.log(5)})();
```

打印结果：5，3，4，1，2

#### 总结：先执行同步任务，接下来执行 process.nextTick，再接下来 Promise 的微任务，最后是 js 事件循环的 6 个阶段，从上到下顺序执行。

#### ⚠️ 注意：每个阶段都有一个先进先出的回调函数队列。只有一个阶段的回调函数队列`清空`了，该执行的回调函数都执行了，事件循环才会进入`下一个`阶段。

### 四、浏览器和nodeJs 环境下的 js 事件循环机制对比

![](https://upload-images.jianshu.io/upload_images/10024246-818b94f2230c511b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 1\. 浏览器下打印：time1，promise1，time2,promise2

因为执行完2个定时器，回调都进入宏任务队列了。然后开始事件循环，因为宏任务是一个个执行的，所以先把第一个定时器的回调放入调用栈中，执行完time1，把微任务放入微任务队列中。
这是调用栈清空，又开始事件循环，这时候有微任务promise1，和第二个宏任务。因为微任务在宏任务之前执行，所以先执行promise1，
这是调用栈又清空，又开始事件循环。执行第二个宏任务，打印，time2，promise2

#### 2.node环境下打印：time1,timer2,promise1,promise2

因为已经在timer阶段了，所以。先执行完time阶段，time1，time2,然后看到微任务，执行微任务。
