---
title: Bun 会是 Webpack 之后的下一件大事吗？
date: 2022-07-21 16:33:52
categories: 前端
---
JavaScript 工具的未来将离 JavaScript 越来越远，一些工具（如 Webpack 和 Babel）的重要性正在日益下降。为什么？

目前已经证明一些语言（如 Rust、Go 甚至 Zig）在捆绑、转译和编译方面比 JavaScript 具有更好的性能。它们不是单线程的，这在处理大量文件方面具有优势。

是什么原因导致一定要用 JavaScript 开发生态系统的工具？毕竟这些工具主要运行在开发人员的机器上，而不是在浏览器上。此外，JavaScript 开发人员不需要调试这些工具的内部代码。

SWC 是最早摆脱 JavaScript 的工具项目之一，不久之后，Esbuild 发布了，很多人为之兴奋不已，因为在性能方面表现出色，它们成了真正的游戏规则改变者。目前，Vite 2.0 正在底层使用 Esbuild 来提供高性能的构建体验。

最近，JavaScript 工具生态系统中出现了一个新成员——Bun。它的目标是让整个 JavaScript 开发过程更加快速，这是一个全能的工具，它不仅加快了编译和解析的速度，还提供了自己的依赖项管理器工具和捆绑。

这个工具还没有为在生产环境中使用做好准备，但它的未来看起来很光明。本文将介绍这个新工具，以及它与 NPM、Esbuild、Babel 和 Webpack 之间的对比。

## 概览

与其他使用 Rust 或 Go 开发的工具不同，Bun 是用 Zig 开发的。Zig 是一种通用的编程语言和工具链，用于开发和维护健壮、优化和可重用的软件。

尽管它是从头开始开发的，但开发者采用了与 Esbuild 项目类似的开发方式。

Bun 支持一些开箱即用的复杂特性，如 TypeScript、CSS in Js、JSX，不过还缺少一些基本功能，如源映射、Minifier、摇树优化等。

Bun 的一个显著特性是它提供了自己的 Node 模块解析器实现，这是最值得关注的优化之一。

与 NPM 和 Yarn 一样，Bun 也会创建锁文件，叫作 bun.lockb。这里需要注意的是，它生成的是二进制文件，而不是纯文本文件。为什么是二进制文件？主要是出于性能的考虑。坏处是不便于我们检查 PR 的变化。

检查锁文件的唯一方法是使用下面的命令：

```
bun install -y
```

Bun 目前支持下面这些加载器：

![image.png](https://upload-images.jianshu.io/upload_images/10024246-da1230c0256274e9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 安装配置

Bun 还不是一个公开项目，你需要加入他们的 Discord 频道并发出邀请请求。在进入 Discord 后找到他们的 #invites 频道，然后在“I want bun”频道上发起邀请请求。

你将获得一个一次性的 jarred-sumner/bun 代码库邀请。

要安装 Bun，你需要执行下面的命令：

```
curl -fsSL https://bun.sh/install | bash
```

检查是否可以正常运行：

```
bun --version
```

你会看到它还没有达到 1.0.0 版本。正如我前面提到的，它还没有为在生产环境中使用做好准备。

使用

它的用法很简单。如果你熟悉 Yarn 或 NPM，你会发现它们几乎是一样的。

安装包：

```
bun install
```

与 Yarn 一样，它将使用已有的 package.json 与锁文件（如果有的话）的组合。

添加或删除包：

```
bun remove react
```

我们可以将 Bun 作为执行器：

```
# instead of `npm run clean`
```

它通过 create 命令提供了与最新的 React 生态系统的一些集成。

我们来创建一个 Next.js 应用：

```
bun create next ./app
```

我们来创建一个 Create-React 应用：

```
bun create react ./app
```

如何生成捆绑文件？

运行`bun bun ./path-to.js`可以生成 node_modules.bun 文件，它包含了所有导入的依赖项。

你可以通过执行`./node_modules.bun > build.js`来查看包的内容。

## 基准测试

让我们通过运行一些基准测试来了解它的速度。当然，这些都是近似的测量值，并且跟运行环境的配置有关。因为这是开发人员的工具，所以我主要关注最常见的开发任务：

*   启动开发服务器；

*   对文件做一些修改；

*   安装包；

*   构建生产发行包；

*   创建一个新的 Web 应用程序。

作为参考，我的笔记本电脑配备了 AMD Raizen 7 CPU 和 16GB 内存，系统是 Ubuntu 20.04。

我使用了一个生成随机 jsx 文件的工具。我生成了 21 个随机的 jsx 文件，我将它们包含在所有测试项目中。

##### Bun 与 Babel

这个对比可能不是很公平，但它确实让我们看到这个工具与传统工具相比是多么的快。

![转译 React 文件对比](https://upload-images.jianshu.io/upload_images/10024246-dba331d1b1750f3d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



##### 创建一个 Create-React 应用

我们可以看到，使用 Bun 和 Webpack+NPM 创建 Create React 应用之间的明显区别。前者几乎没有任何延迟，只需要 4.4 秒就可以完成所有设置。

![创建 CRA 对比](https://upload-images.jianshu.io/upload_images/10024246-80ed730cf604dfe8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



##### 创建一个 Next.js 应用

之前的结果其实并没有那么令人印象深刻，毕竟我们已经习惯了用其他工具痛击 Webpack。我们来进行一场公平的战斗，比较一下 Bun 和 SWC。

![Bun 与 SWC 对比](https://upload-images.jianshu.io/upload_images/10024246-6e6ad43fd526194d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



两者之间的差异非常明显，特别是在处理文件变更方面。在我的笔记本电脑上，Bun 只需要 10 毫秒，而 SWC 需要 70 毫秒。

##### 包管理器

在模块的安装和操作方面，Bun 也有一些优势。其他工具依赖 NPM 或 Yarn 来完成这项工作，Bun 的性能大约比 NPM 快 4 到 100 倍。

我们已经第二步中看到了两者的巨大差异。不过，我们现在来做一个更基本的例子。我们创建一个 package.json 文件，其中的依赖项如下：

*   date-fns@2.28.0——89.5KB

*   jspdf@2.5.1——339.1KB

*   react@17.0.2——6.9KB

然后我们对第一次安装和基于缓存安装进行基准测试。为了让差异更加明显，我选择了一个大型的库（jspdf）。
![Bun 与 NPM 安装包对比](https://upload-images.jianshu.io/upload_images/10024246-fad18522b76d24cd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



时间差异很明显。如果通过网络安装，速度快 4 倍，如果从缓存中解析，速度会快更多。

Bun 与 Vite

Esbuild 是 Bun 真正的竞争对手。对于这个测试，我使用了 Vite，因为它内部使用了 Esbuild。

![](https://upload-images.jianshu.io/upload_images/10024246-3c10659db6efbe0b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### Bun 与 Vite 在开发服务器方面的对比

我还基于之前相同的代码使用三个主要工具生成了捆绑包。需要注意的是，我们不建议在生产环境中使用 Bun，因为它缺少了相当多的特性。尽管基准测试结果令人印象深刻，但我们还是要持谨慎的态度。

在最坏的情况下，最长构建时间是 7 秒。这三个工具在这方面做得很出色，不是没有道理的。

![](https://upload-images.jianshu.io/upload_images/10024246-72d26b94ce8707b0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
Bun、Vite、SWC 构建一个用于生产环境的捆绑包

## 总结

JavaScript 工具从未像现在这么好过，即使这个工具还没有为在生产环境中使用做好准备，但出现了新的竞争对手总是一件好事。Webpack 的未来还不明朗，它在 JavaScript 领域内外都有很多竞争对手。

Bun 并不是万能的工具，它擅长的是构建网站和 Web 应用。如果要构建库，Bun 团队建议使用 Esbuild，甚至 Rollup。

现在，Bun 开发团队的重心仍然不在生产就绪方面，他们专注于开发以及与现有框架和工具的兼容性。

>**原文链接：**
https://betterprogramming.pub/is-bun-the-next-big-thing-after-webpack-d683441f77b9
