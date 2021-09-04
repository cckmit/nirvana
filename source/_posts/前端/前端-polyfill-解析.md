---
title: 前端-polyfill-解析
date: 2021-06-10 16:28:03
category: javascript
---
## 什么是 Polyfilll

Pollfill 一词最早是在 [nodemon](https://github.com/remy/nodemon) 的作者 [Remy Sharp](https://remysharp.com/) 于 2010 年10 月 8 日发表的博客文章 [What is a Polyfill?](https://remysharp.com/2010/10/08/what-is-a-polyfill) 中首次提到，他对 polyfill 的定义是：

> A shim that mimics a future API providing fallback functionality to older browsers.

翻译过来就是：

> polyfill 就是一个垫片/填充/补丁程序，用于抹平浏览器之间的 API 差异，在旧的浏览器上支持新的特性。

无论在早期或者现代前端工程中，常常有以下几种方式来打补丁：

1.  手动

    > 手动将补丁代码引入到项目

2.  自动

    > 借助工具如 [core-js](https://github.com/zloirock/core-js)、[babel](https://babeljs.io/)、[Polyfill.io](https://polyfill.io/v3/) 等工具自动化打补丁，并且可以做到根据目标运行环境按需打补丁或动态打补丁。

## 手动打补丁

在前端工具还不繁荣的早期，我们需要手动引入所需要的补丁，比如以 ES6 的 [Object.assign](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) 为例，在 IE11 上仍然会报错：
![](https://upload-images.jianshu.io/upload_images/10024246-669c897e0875f3cf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


所以需要手动引入补丁代码，一种是直接安装第三方包，另外一种是引入来自 MDN 的补丁代码：

```
// 安装 object-assign (https://github.com/sindresorhus/object-assign)
npm i object-assign

// 引入
Object.assign = require('object-assign')
```

或者在需要的地方放置来自 [MDN 的补丁代码](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)：

```
if (typeof Object.assign !== 'function') {
  // Must be writable: true, enumerable: false, configurable: true
  Object.defineProperty(Object, "assign", {
    value: function assign(target, varArgs) { // .length of function is 2
      'use strict';
      if (target === null || target === undefined) {
        throw new TypeError('Cannot convert undefined or null to object');
      }

      var to = Object(target);

      for (var index = 1; index < arguments.length; index++) {
        var nextSource = arguments[index];

        if (nextSource !== null && nextSource !== undefined) {
          for (var nextKey in nextSource) {
            // Avoid bugs when hasOwnProperty is shadowed
            if (Object.prototype.hasOwnProperty.call(nextSource, nextKey)) {
              to[nextKey] = nextSource[nextKey];
            }
          }
        }
      }
      return to;
    },
    writable: true,
    configurable: true
  });
}
```

很明显，手动引入补丁代码既有优势和劣势：

*   优势：最小化引入，不会有额外的冗余代码，保证加载性能
*   劣势：手动引入，不易维护和管理；对于支持多端的应用和多样化的 polyfill 维护成本过大

## 自动打补丁

### 按需打补丁

[babel](https://babeljs.io/) 和 [browerslist](https://github.com/browserslist/browserslist) 的出现，彻底解决了手动打补丁的劣势问题，通过简单的配置就可以实现自动注入补丁代码。

比如有这样一段代码：

```
// index.js
Promise.resolve().finally();
```

在 Windows10 的 Edge 17 中不能运行，因为其不支持 `Promise.prototype.finally` 方法，借助 babel，运行以下命令：

```npx babel index.js --out-file compiled.js```

并且存在这样一份配置文件：

```
// babel.config.js
module.exports = {
  presets: [
    [
      "@babel/env",
      {
        targets: {
          edge: "17",
        },
        useBuiltIns: "usage",
        corejs: "2"
      },
    ],
  ],
};
```

然后就会输入这样的内容：

```
"use strict";

require("core-js/modules/es7.promise.finally.js");

Promise.resolve().finally();
```

可以看到，babel 已经自动的在代码的顶部注入了来自 JavaScript 标准库 [core-js](https://github.com/zloirock/core-js) 中 `Promise.prototype.finally` 实现代码。大概的过程是这样的：

1.  `npx babel` 调用*@babel/cli* 提供的终端命令 `babel` 来运行 *@babel/core*
2.  *@babel/core* 开始读取配置、预设，调度各个组件执行编译流程

    1.  调用 *@babel/parser* 生成源码的 AST
    2.  调用 *@babel/traverse* 提供给插件转换代码的能力(*@babel/polyfill* 将在这一阶段介入)
    3.  调用 *@babel/generator* 生成代码
3.  写入代码到文件

而这个过程涉及到的依赖只有 *@babe/core*、*@babel/preset-env* 和 *@babel/polyfill*。

##### *@babel/core*

*@babel/core* 是 *babel* 的核心库，包含了 *@babel/preser*、 *@babel/traverse*、*@babel/generator* 等解析、转换和生成代码的库。它从 *@babel/cli* 接受参数并调度各个组件执行编译流程，是整个流程的调度者和组织者。

##### *@babel/preset-env*

*@babel/preset-env* 提供了一组默认插件的预设，官方提供的预设有四个：

*   [@babel/preset-env](https://babeljs.io/docs/en/babel-preset-env)
*   [@babel/preset-flow](https://babeljs.io/docs/en/babel-preset-flow)
*   [@babel/preset-react](https://babeljs.io/docs/en/babel-preset-react)
*   [@babel/preset-typescript](https://babeljs.io/docs/en/babel-preset-typescript)

而这些预设最终会被解析成一组插件，以 *@babel/preset-react* 为例，它最终会生成以下插件列表：

*   [@babel/plugin-syntax-jsx](https://babeljs.io/docs/en/babel-plugin-syntax-jsx)
*   [@babel/plugin-transform-react-jsx](https://babeljs.io/docs/en/babel-plugin-transform-react-jsx)
*   [@babel/plugin-transform-react-display-name](https://babeljs.io/docs/en/babel-plugin-transform-react-display-name)

这些插件则被用来支持 react 和装换 jsx 语法。

##### *@babel/polyfill*

自 Babel7.4.0 开始，*@babel/polyfill* 已经拆分成了两个包： [core-js](https://github.com/zloirock/core-js) 和 [regenerator runtime](https://github.com/facebook/regenerator/tree/master/packages/runtime)，*core-js* 用于支持 ECMAScript 的新特性，*regenerator-runtime* 用于支持 generator 函数。这个插件也默认被包含在 *@babel/preset-env* 预设中。

Babel 将检查所有的代码，以查找目标环境中缺少的功能，并且利用 *@babel/polyfill* 注入所需的 polyfill。

### 动态打补丁

动态打补丁仍然有一个最大的缺陷：补丁冗余。以 `Object.assign` 为例，在支持这个特性的浏览器上就没必要引入这个补丁，所以就会造成补丁的冗余。而社区就出现了根据浏览器特性动态打补丁的方案。

[Polyfill.io](https://polyfill.io/v3/) 就是这样一个服务，它会根据浏览器 user-agent 的不同，返回不同的补丁代码。比如想加载 `Promise` 的补丁代码，可以直接引入：

```<script src="https://polyfill.io/v3/polyfill.js?features=Promise"></script>```

在较高版本的 Chrome(89) 浏览器上，就会返回空的内容：

![](https://upload-images.jianshu.io/upload_images/10024246-dd6256bb27949f19.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将 User-agent 改为 IE11 后，就会返回完整的 polyfill 代码：

![](https://upload-images.jianshu.io/upload_images/10024246-abdb3baef95c0c26.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这个服务还提供了丰富的 URL 查询参数来定制 polyfill，具体可以查阅文档 [url-builder](https://polyfill.io/v3/url-builder/)。

同时，如果对 [Polyfill.io](https://polyfill.io/v3/) 的稳定性和安全性有要求，可以借助开源的 [polyfill service](https://link.zhihu.com/%3Ftarget%3Dhttps%253A//github.com/Financial-Times/polyfill-service) 搭建自己的服务。

### 最佳实践

考虑目前社区的这些方案，并结合具体的业务场景，如果对加载资源有着极致的性能要求，可以考虑基于 [Polyfill.io](https://polyfill.io/v3/) 自建 polyfill 服务，动态注入 polyfill。而对于一些对加载性能不那么敏感的项目，则完全可以使用 [babel](https://babeljs.io/) 来自动注入 polyfill。当然也可以两者结合使用，但是需要平衡维护成本和收益。
