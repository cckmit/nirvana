---
title: Hermes将成React Native默认的JavaScript引擎
date: 2021-11-16 21:12:40
category: reactnative
---

近日，React Native在[官博宣布](https://reactnative.dev/blog/2021/10/26/toward-hermes-being-the-default)Hermes成React Native默认JavaScript引擎。Facebook 工程师[黄玄](https://twitter.com/huxpro)在官博中阐述了Hermes自[2019年发布](https://engineering.fb.com/2019/07/12/android/hermes/)以来，为推动Hermes成为React Native 成为JavaScript引擎取得的一些进展。

目前，Hermes被越来越多的社区所采用。Expo团队正基于React Native应用程序维护一个流行的元框架，并宣布对Hermes进行实验性支持。此外，移动数据库Realm团队也宣布对Hermes提供alpha支持。

## Hermes简介

Hermes是Facebook在[Chain react 2019](https://infinite.red/ChainReactConf) 大会上发布的一个崭新JavaScript引擎 ，小巧轻便，针对在Android上运行React Native 进行了优化。

对于许多应用程序，只需启用 Hermes 即可缩短启动时间、减少内存使用量并缩小应用程序大小。
![](https://upload-images.jianshu.io/upload_images/10024246-01ba2628a3395db6.gif?imageMogr2/auto-orient/strip)

## 专为 React Native 而优化

据 Hermes 的功能定义，它会负责如何提前执行编译工作，这意味着启用 Hermes 的 React Native 应用程序附带了预编译优化的字节码，而不是纯 JavaScript 源码。 这大大减少了用户启动产品所需的工作量。 据Facebook和社区应用程序的测试表明，启用Hermes通常会将产品的 TTI（或交互时间）指标减少近一半。

话虽如此，团队一直致力于在许多其他方面改进Hermes，以使其作为专门用于React Native 的 JavaScript引擎变得更好。

## 为 Fabric 构建一个新的垃圾收集器

随着新React Native架构中Fabric渲染器的到来，它可以在UI线程上同步调用JavaScript，这也意味着如果JavaScript线程执行时间过长，可能会带来UI丢失，并导致用户无法输入。为避免JavaScript任务调度冗长，React Fiber提供的并发渲染机制是将工作拆分成多个块来执行。此外，JavaScript 线程还有另一个常见的延迟来源——垃圾收集（GC）机制。

Hermes 以前的默认垃圾收集器 GenGC 属于单线程分代垃圾收集器。新生代采用典型的半空间复制策略，老一代采用mark-compact策略，使其更擅长积极地将内存返回给操作系统。

由于是单线程执行，GenGC长时间运行会导致GC暂停。在像 Facebook for Android 这样的复杂应用上，平均暂停时长为 200 毫秒，而第 99 百分位暂停则为 1.4 秒。考虑到 Facebook for Android 庞大且多样化的用户群体，最极端的暂停时间甚至会长达 7 秒。

为缓解这种情况，黄玄在博客中表示，他们正在实施一个全新的，执行多并发操作的垃圾回收方案，名为Hades。Hades与GenGC的回收方式完全相同，它可以通过在后台线程中执行大部分工作来缩短GC 暂停时间，并且还不会阻止引擎的主线程执行 JavaScript 代码。从统计数据来看，Hades 在 64 位设备上第 99.9 百分位上的延迟为 48 毫秒（比 GenGC 快 34 倍！），而在 32 位设备上第 99.9 百分位上的延迟约 88 毫秒（单线程增量 GC 的形式运行）。

由于需要更高的写屏障、基于空闲列表的分配机制（与碰撞指针分配器相反）和更多的堆碎片，Hades通过整体吞吐量来换取暂停时间的优化，这被官方认为是正确的权衡，接下来会通过合并和其他内存优化讨论来降低整体的内存消耗。

## 攻破性能痛点

应用程序的启动时长对许多应用程序的成功至关重要，官方团队正在不断提升 React Native 的性能边界。对于Hermes实现的任何新JavaScript功能，他们都会进行仔细监控，观察它们对性能的影响，并确保它们不会拉低指标。

目前，Facebook团队正在为 Metro 中的 Hermes 试验专用的 Babel transforms 配置文件，以用 Hermes 的原生 ESNext 实现来替换是十几种 Babel transforms 。目前已观察到 18-25% 的 TTI 改进和整体字节码大小的减少，希望接下来的 OSS 也能产生类似的结果。

除了优化启动性能，团队还将内存占用作为改进React Native应用的重要机会，尤其是在VR方面。在JavaScript 引擎的底层控制中，通过压缩位（squeezing bits）和字节实现多轮内存优化：

*   以前，所有JavaScript值都表示64位NaN装箱编码标记值，用来表示64位架构上的浮点双精度值和指针。 然而，这在实践中是浪费的，因为大多数数字都是小整数 (SMI)，并且客户端应用程序的 JavaScript 堆通常不会大于 4GiB。 为了解决这个问题，我们引入了一种新的 32 位编码，其中 SMI 和指针以 29 位编码（因为指针是 8 字节对齐的，我们可以假设底部 3 位始终为零），其余的 JS 数字是 装在堆上。 这将 JavaScript 堆大小减少了约 30%。

*   不同类型的 JavaScript 对象在 JavaScript 堆中表示为不同类型的 GC 管理单元。 通过积极优化这些单元格头的内存布局，我们能够将内存使用量再减少约 15%。

团队还决定Hermes不实现即时 (JIT) 编译，对于大多数 React Native 应用程序来说，额外的预热成本、额外的二进制文件和内存占用实际上并不值得。

多年以来，团队在解释器性能与编译器的优化方面投入了大量精力，也让 Hermes 获得了远超其他引擎的 React Native 工作负载吞吐量优势。他们将继续关注广泛存在的性能瓶颈（解释器调度循环、堆栈布局、对象模型、垃圾回收等）以提高吞吐量。请大家静待胜利的消息！

在过去两年，Hermes团队除了上文描述的内容之外，还在垂直整合领域做了不少尝试，例如Hermes 使用 Chrome DevTools Protocol支持 Chrome 调试器在设备上执行 JavaScript 调试；其次，Hermes还就推动整个技术社区生态进行努力；第三，Hermes 正在努力扩展到更多新平台，例如在微软 Build 2020 大会上，软件巨头表示相较于原本的 Chakra 引擎，Hermes 能够将 React Native for Windows 的内存占用量降低 13%。而在最近的一些综合基准测试中，微软发现 Hermes 0.8（包含 Hades 及之前提到的 SMI 与指针压缩优化功能）占用的内存量比其他引擎少 30% 至 40%。

作者在最后也呼吁广大开发者能够多多支持Hermes社区，让Hermes成为大多数React Native应用程序的正确选择，并分享了Hermes社区的发展路线图，从而最终实现Hermes成为React Native 默认JavaScript引擎这一目标。

*   [https://reactnative.dev/blog/2021/10/26/toward-hermes-being-the-default](https://reactnative.dev/blog/2021/10/26/toward-hermes-being-the-default)
