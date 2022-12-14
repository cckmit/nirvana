---
title: React18正式版发布，未来发展趋势如何？
date: 2022-04-01 16:28:03
categories: 资讯
tags: [news,react]
---
>作者：卡颂

从v16开始，React团队就在普及并发的概念。在v18的迭代过程中(alpha、Beta、RC)，也一直在科普并发特性，所以正式版发布时，已经没有什么新鲜特性。
2022年3月29号，React18正式版发布。

从v16开始，React团队就在普及并发的概念。在v18的迭代过程中(alpha、Beta、RC)，也一直在科普并发特性，所以正式版发布时，已经没有什么新鲜特性。

本文主要讲解v18发布日志中透露的一些未来发展趋势。

开发者可能并不会接触到并发特性

React对增加API是很慎重的。从13年诞生至今，触发更新的方式都是this.setState。

而引入并发概念后，光是与并发相关的API就有好几个，比如：

*   useTransition。
*   useDeferredValue。

甚至出现了为并发兜底的API(即并发情况下，不使用这些API可能会出bug)，比如：

*   useSyncExternalStore。
*   useInsertionEffect。

一下多出这么多API，还不是像useState这种不使用不行的API，况且，并发这一特性对于多数前端开发者都有些陌生。

你可以代入自己的业务想想，让开发者上手使用并发特性有多难。

所以，在未来用v18开发的应用，「开发者可能并不会接触到并发特性」。这些特性更可能是由各种库封装好的。

**比如：**startTransition可以让用户在不同视图间切换的同时，不阻塞用户输入。

这一API很可能会由各种Router实现，再作为一个配置项开放给开发者。

万物皆可Suspense

对于React来说，有两类瓶颈需要解决：

*   CPU的瓶颈，如大计算量的操作导致页面卡顿。
*   IO的瓶颈，如请求服务端数据时的等待时间。

其中CPU的瓶颈通过并发特性的优先级中断机制解决。

IO的瓶颈则交给Suspense解决。

所以，未来一切与IO相关的操作，都会收敛到Suspense这一解决方案内。

从最初的React.lazy到如今仍在开发中的Server Components，最终万物皆可Suspense。

这其中有些逻辑是很复杂的，比如：

*   Server Components。
*   新的服务端渲染方案。

所以，这些操作不大可能是直接面向开发者的。

这又回到了上一条，这些操作会交由各种库实现。如果复杂度更高，则会交由基于React封装的框架实现，比如Next.js、Remix。

这也是为什么React团队核心人物Sebastian会加入Next.js。

可以说，React未来的定位是：一个前端底层操作系统，足够复杂，一般开发者慎用。

而开发者使用的是「基于该操作系统实现的各种上层应用」。

##### 总结

如果说v16之前各种React Like库还能靠体积、性能优势分走React部分蛋糕，那未来两者走的完全是两条赛道，因为两者的生态不再兼容。

未来不再会有React全家桶的概念，桶里的各个部件最终会沦为更大的框架中的一个小模块。

当前你们业务里是直接使用React呢，还是使用各种框架(比如Next.js)?
