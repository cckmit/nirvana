---
title: React-Profiler-React性能审查工具
date: 2021-06-10 16:28:03
category: javascript
---
## React Profiler 定位 Render 过程瓶颈

React Profiler 是 React 官方提供的性能审查工具，本文只介绍笔者的使用心得，详细的使用手册请移步官网文档。

Note：react-dom 16.5+ 在 DEV 模式下才支持 Profiling，同时生产环境下也可以通过一个 profiling bundle react-dom/profiling 来支持。请在 fb.me/react-profi… 上查看如何使用这个 bundle。

“Profiler” 的面板在刚开始的时候是空的。你可以点击 record 按钮来启动 profile：

![](https://upload-images.jianshu.io/upload_images/10024246-638e6549ab4827ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## Profiler 只记录了 Render 过程耗时

不要通过 Profiler 定位非 Render 过程的性能瓶颈问题

通过 React Profiler，开发者可以查看组件 Render 过程耗时，但无法知晓提交阶段的耗时。

尽管 Profiler 面板中有 Committed at 字段，但这个字段是相对于录制开始时间，根本没有意义。

通过在 React v16 版本上进行实验，同时开启 Chrome 的 Performance 和 React Profiler 统计。

如下图，在 Performance 面板中，Reconciliation和Commit阶段耗时分别为 642ms 和 300ms，而 Profiler 面板中只显示了 642ms：

![](https://upload-images.jianshu.io/upload_images/10024246-ccc366e84ff1e1fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 开启「记录组件更新原因」

点击面板上的齿轮，然后勾选「Record why each component rendered while profiling.」，如下图：

![](https://upload-images.jianshu.io/upload_images/10024246-98b7330fe60eb3c5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


然后点击面板中的虚拟 DOM 节点，右侧便会展示该组件重新 Render 的原因。

## 定位产生本次 Render 过程原因

由于 React 的批量更新（Batch Update）机制，产生一次 Render 过程可能涉及到很多个组件的状态更新。那么如何定位是哪些组件状态更新导致的呢？

![](https://upload-images.jianshu.io/upload_images/10024246-6f5297d444f47fff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


在 Profiler 面板左侧的虚拟 DOM 树结构中，从上到下审查每个发生了渲染的（不会灰色的）组件。

如果组件是由于 State 或 Hook 改变触发了 Render 过程，那它就是我们要找的组件，如下图：

![](https://upload-images.jianshu.io/upload_images/10024246-26bf8917e397a0b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

--------
[Optimizing Performance React](https://react.docschina.org/docs/optimizing-performance.html) 官方文档，最好的教程, 利用好 React 的性能分析工具。
