---
title: Express Koa2 Egg 对比
date: 2022-03-28 16:28:03
categories: 前端
tags: [node]
---
目前比较流行的nodejs框架有express、koa、egg.js，还有就是和ts相关的框架nest.js。

无论是哪种框架，其核心都是基于中间件来实现的，而中间件执行的方式都跟洋葱模型有关，它们的差别主要也是在洋葱模型的执行方式上。
## 什么是洋葱模型？

洋葱模型，就像洋葱一样，一层包裹一层，而nodejs框架的执行就像是中间穿过洋葱的一条线，而每一层洋葱皮就代表一个中间件，进入时穿过多少层，出来时还得穿出多少层，具有先进后出（栈）的特点。

![](//upload-images.jianshu.io/upload_images/12188741-b52627362cdc4a26.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)
## 框架间关系
关系如下：
```
Midway.js ---|> Egg.js ---|> Koa.js,
               Nest.js ---|> Express.js
```
- 1、Express.js 是 Node.JS 诞生之初，最早出现的一款框架，现在仍然很流行，作者是TJ。
- 2.随着ECMAScript的发展，推出了generator yield 语法，JS向同步方式写异步代码迈出了一步，作为回应，TJ大神推出了Koa.js。
- 3、Koa.js是一款微型Web框架，写一个hello world很简单，但web应用离不开session，视图模板，路由，文件上传，日志管理。这些 Koa 都不提供，需要自行去官方的 Middleware 寻找。然而，100个人可能找出100种搭配。
- 4、Egg.js是基于Koa.js，解决了上述问题，将社区最佳实践整合进了Koa.js，另取名叫Egg.js，并且将多进程启动，开发时的热更新等问题一并解决了。这对开发者很友好，开箱即用，开箱即是最(较)佳配置。Egg.js发展期间，ECMAScript又推出了 async await，相比yield的语法async写起来更直观。当然，Koa.js也同步进行了跟进,Egg.js低层是Koa.js，自然也进行了跟进。 现在TypeScript大热，可以在编码期间，提供类型检查，更智能的代码提示。Egg.js不支持TypeScript，此时淘宝团队在Egg.js基础上，引入了TypeScript支持，取名叫 MidwayJS 。
- 5.基于Express.js的全功能框架 Nest.js，他是在Express.js上封装的，充分利用了TypeScript的特性；Nest.js的优点是社区活跃，涨势喜人。缺点是，如果从来没有接触过TS，刚开始学习曲线有点陡峭。

## Express和KOA的差异
- Express 封装、内置了很多中间件，比如 connect和 router，而 KOA 则比较轻量，开发者可以根据自身需求定制框架；
- Express 是基于 callback 来处理中间件的，而 KOA 则是基于 await/async；
在异步执行中间件时，Express 并非严格按照洋葱模型执行中间件，而 KOA 则是严格遵循的。
- Express 使用 callback捕获异常，对于深层次的异常捕获不了，Koa 使用 try catch，能更好地解决异常捕获。


