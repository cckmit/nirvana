---
title: node之nodemon的简单配置和使用
date: 2021-03-11 21:12:40
categories: 前端
---

nodemon是一种工具，通过在检测到目录中的文件更改时自动重新启动节点应用程序来帮助开发基于node.js的应用程序。

nodemon并没有要求任何对你的代码或开发的方法中的额外变化。nodemon是一个替换包装器node，用于在执行脚本时nodemon替换node命令行上。

## 安装

```
npm install -g nodemon

```

nodemon将全局安装到您的系统路径。

您还可以将nodemon安装为开发依赖项：

```
npm install --save-dev nodemon

```

## 用法

```
nodemon app.js

```

使用帮助

```
nodemon -h

```

如果我的应用程序接受主机和端口作为参数，我会这样开始：

```
nodemon ./server.js localhost 8080

```

如果想更多的学习，就去github上仔细看一下
[https://github.com/remy/nodemon](https://github.com/remy/nodemon)
