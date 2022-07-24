---
title: ahooks 3.0 来了！高质量可靠的 React Hooks 库
date: 2022-04-08 16:28:03
categories: 
    - [前端]
    - [资讯]
tags: [react]
---
[ahooks](https://github.com/alibaba/hooks) 是一套开源的 React Hooks 库，封装了大量好用的 Hooks。在当前 React 项目研发过程中，一套好用的 React Hooks 库是必不可少的，希望 ahooks 能成为您的选择。 

![](https://upload-images.jianshu.io/upload_images/10024246-ec6dfd0c7f8d018c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

自 2019 年 8 月 ahooks（umi hooks）发布第一个版本，到今天已经历经了 2 年的发展，在国内外社区也获得了很多同学的认可。目前 ahooks 2.0 的成绩主要包括：

*   在阿里内部覆盖了上千个前端应用
*   开发了 60+ Hooks
*   npm & tnpm 周下载量 7w+
*   GitHub star 7.4k

在这两年的发展过程中，随着对 React Hooks 的理解越来越深入，我们能看到 ahooks 2.0 设计中的很多不足。在这个背景下，我们决定开发 3.0 版本。 

ahooks 3.0 的目标是建设**高质量可靠的 **React Hooks 库，我们希望成为像 lodash 一样的稳定的基础依赖。相较于 2.0，具有以下几个优势：

*   **全面支持 SSR**
*   **全新的 useRequest**
*   **所有的输出函数引用是固定的，避免闭包问题**
*   **DOM 类 Hooks 支持 target 动态变化**
*   更合理的 API 设计
*   解决了在严格模式（Strict Mode）下的问题
*   解决了在 react-refresh（HRM）模式下的问题
*   新增了更多 Hooks
*   修复了很多已知问题

## 全面支持 SSR

React Hooks 在 SSR 场景下，普遍会碰到“DOM/BOM 缺失”、“useLayoutEffect 警告”两个问题。ahooks v3.0 彻底解决了这两个问题，你可以安心的将 ahooks 使用到 SSR 场景了。

更多内容可以参考《[React Hooks & SSR](https://ahooks.js.org/zh-CN/guide/blog/ssr/)》

## 全新的 useRequest

useRequest 是 ahooks 使用量最高的 Hook，同时也是 issue 量最多的 Hook。总结 useRequest 之前最大的问题是：

*   代码拆分不合理，所有的功能混合在一个文件中，改动起来非常复杂
*   部分功能上线前没有充分论证，导致功能设计不合理，但是也下不掉
*   融合了 pagination 和 loadMore 的逻辑，导致 ts 类型复杂到难以想象

ahooks v3.0 版本对 useRequest 进行了完全重写：

*   通过插件式组织代码，核心代码极其简单，所有高级功能均是通过插件实现
*   仔细论证了提供的所有的能力，保证上线的特性均是最优解。对存疑的能力，渐进添加
*   所有的参数支持动态变化
*   删除了 pagination 和 loadMore 逻辑，单独拆分出其它 Hooks 提供相应能力
*   避免了 ts 类型重载，可以更方面的基于 useRequest 封装更高级的 Hooks
*   修复了大量遗留问题

更多内容可以参考《[全新的 useRequest](https://ahooks.js.org/zh-CN/guide/upgrade/#%E5%85%A8%E6%96%B0%E7%9A%84-userequest)》

## 函数特殊处理，避免闭包问题

ahooks v3 通过对输入输出函数做特殊处理，尽力帮助大家避免闭包问题。这个能力我觉得是 ahooks 做的比较激进的地方，但确实能对用户提供非常好的使用体验。 

1.  **ahooks 所有的输出函数，引用地址都是不会变化的**

```
const [state, setState] = React.useState();

```

众所周知，`React.useState` 返回的 `setState` 函数引用地址是固定的，使用时不需要考虑奇奇怪怪的问题，不需要把 `setState` 放到各种依赖中。 ahooks v3.0 所有 Hooks 返回的函数，都拥有和 `setState` 一样的特性，引用地址不会变化，放心大胆的使用即可。 ****

**2\. 所有用户输入的函数，永远使用最新的一份**

对于接收的函数，ahooks v3 会做一次特殊处理，保证每次调用的函数永远是最新的。

```
const [state, setState] = useState();

useInterval(() => {
  console.log(state);
}, 1000);

```

比如以上示例，`useInterval` 任何时候调用的函数永远是最新的，也就是 `state` 永远是最新的。 

更多内容可以参考《[ahooks 输入输出函数处理规范](https://ahooks.js.org/zh-CN/guide/blog/function)》

## 更多问题修复

*   DOM 类 Hooks 支持 target 动态变化，相关文档可见《[DOM 类 Hooks 使用规范](https://ahooks.js.org/zh-CN/guide/dom)》
*   v3 修复了在严格模式下的一些问题。参考《[React Hooks & strict mode](https://ahooks.js.org/zh-CN/guide/blog/strict)》
*   v3 修复了在 react-refresh（HRM）模式下的一些问题。参考《[React Hooks & react-refresh（HMR）](https://ahooks.js.org/zh-CN/guide/blog/hmr)》

更多改动参考《[v2 to v3](https://ahooks.js.org/zh-CN/guide/upgrade)》

## 结尾

ahooks v3.0 的 slogan 是“高质量可靠的 React Hooks 库”，这是我们为之奋斗的目标，希望 ahooks 成为每一位开发者的必备基础库之一。 

感谢 ahooks 的共建者和使用者！欢迎试用 v3.0！

```
$ npm install --save ahooks@next
# or
$ yarn add ahooks@next
```

*   文档：[https://ahooks.js.org/zh-CN](https://ahooks.js.org/zh-CN)
*   源码：[https://github.com/alibaba/hooks](https://github.com/alibaba/hooks)
