---
title: 尤雨溪：Vite 2.0 正式发布！
date: 2021-06-10 16:48:55
categories: 资讯
---
## **Vite是什么**

`Vite` (法语单词“ fast”，发音为/vit/)是一种新型的前端构建工具，它可以显著改善前端的开发体验。它由两个主要部分组成:

*   一个通过本机 ES 模块提供源文件的开发服务器，具有丰富的内置特性和快得惊人的热模块替换(HMR)。
*   一个构建命令，它将代码与 Rollup 捆绑在一起，后者预先配置为输出用于生产的高度优化的静态资产

此外，`Vite` 通过其插件 `API` 和 `JavaScript API` 具有高度的扩展性，并提供全面的类型支持

### **有多快？**

为了了解 `Vite` 的速度有多快，这里有一个视频比较了使用 Vite vs. create-React-app (CRA) 应用程序的过程:

[视频地址](https://mp.weixin.qq.com/mp/readtemplate?t=pages/video_player_tmpl&auto=0&vid=wxv_1744059798674849795)

## **关于Vite2**

这实际上是 `Vite` 的第一个稳定版本。也就是说，`Vite 2.0`比之前的版本有了很大的改进：

**多框架支持**

`Vite 2.0`采用了一个更健壮的内部架构从头开始重新设计。现在它完全与框架无关，所有特定于框架的支持都委托给了插件。现在有 `Vue`、 `React`、 `Preact`、 `Lit Element` 和正在进行的 `Svelte` 的官方模板

### ****全新插件机制和 API****

受到 `WMR` 的启发，新的插件系统扩展了 `Rollup` 的插件界面，并兼容许多 `Rollup` 插件。插件可以使用 `rollup` 兼容的钩子，以及额外的 `vite` 特定钩子和属性来调整 `vite` 专用行为(例如区分 dev 和 build 或 HMR 的自定义处理)

### ****基于 esbuild 的依赖预打包****

因为 `Vite` 是一个本地的 `ESM` dev 服务器，使用`Pre-Bundling`以减少浏览器请求的数量并处理 `CommonJS` 到 `ESM` 的转换

在之前，`Vite` 使用 `Rollup` 完成这项工作，而在2.0中，它现在使用 `esbuild`，从而使依赖性预绑定的速度提高了**「10-100」**倍

作为参考，`React Meterial` 用户界面以前需要28秒，现在需要约1.5秒

### ****更好的 CSS 支持****

将 `CSS` 视为`module`的`First-class`，并支持以下开箱即用的功能:

*   Resolver enhancement 增强了 CSS 中的路径，以尊重别名和 npm 依赖
*   URL rebasing 不管文件从哪里导入，路径都会自动重新设置
*   CSS code splitting

### ****服务端渲染 (SSR) 支持****

`Vite` 提供了 `api`，以便在开发过程中有效地在 `Node.js` 中加载和更新基于 `esm` 的源代码(就像服务器端 HMR 一样) ，并自动外部化 `commonjs` 兼容的依赖关系，以提高开发和 `SSR` 构建速度。生产服务器可以与 `Vite` 完全解耦

`Vite SSR` 是作为一个低层次的特性提供的，我们期望看到更高层次的框架在引擎盖下利用它

### ****旧浏览器支持****

`Vite` 的目标是默认支持原生 `ESM` 的现代浏览器，你也可以通过官方的`@vitejs/plugin-legacy` 选择支持传统的浏览器

这个插件会自动生成现代/旧版两个包，并且基于浏览器/特征提取，提供正确的包，确保在支持它们的现代浏览器中有更高效的代码
