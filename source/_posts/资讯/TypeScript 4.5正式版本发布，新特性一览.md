---
title: TypeScript 4.5正式版本发布，新特性一览
date: 2021-12-29 16:28:03
category: news
---
微软正式发布了 TypeScript 4.5 ，如果想要使用TypeScript 4.5，可以使用npm下载。

`npm install typescript`

从Beta、RC版本到正式版本，TypeScript 4.5 经历了很多变化。从Beta版本开始，TypeScript团队决定将推迟对Node.js 12 的 ECMAScript 模块支持，目前仅在nightly 版本中作为实验功能提供。同时，4.5版本还添加了关于JSDoc新特性的注释；在语言编辑方面，引入了更多的代码片段补全； 解决了build模式下过度调用package.json 文件的性能回归问题。

TypeScript 4.5 正式版本亮点特性：

*   新的 `Awaited`类型和 `Promise`改进

*   `node_modules`支持 `lib`

*   模板字符串类型作为判断符

*   `--module es2022`

*   移除Conditional Types尾递归

*   禁用Import Elision

*   `type` Modifiers on Import Names

*   检测object对象是否有私有字段

*   导入断言

*   JSDoc 中的常量断言和默认类型参数

*   通过`realPathSync.native` 加快加载时间

*   代码补全功能

*   编辑器增强了对未解析类型的支持

*   实验性功能：Nightly 版本支持Node.js ECMAScript Module

*   重大变化

