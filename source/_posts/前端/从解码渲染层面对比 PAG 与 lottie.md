---
title: 从解码渲染层面对比 PAG 与 lottie
date: 2022-01-14 16:28:03
category: javascript
---
##从解码渲染层面对比 PAG 与 lottie
最近由于业务需求，需要调研一下业界的知名动画渲染框架。经过一些时间的调研与探索，我将目光聚焦在两款不错的动画框架上。一款是知名的 lottie，一款是腾讯出品的 PAG。

lottie 相信大部分端上的研发都会或多或少的听过， lottie 是 airbnb 开源的一款业界知名的开源动画框架,通过 AE 制作动画之后，通过附带的插件 bodymovin 导出 动画的 json 文件，端上再通过 json 解析渲染。

lottie 不单单包含端上的渲染 SDK，其实是一套完整的动画制作工作流。

PAG（Portable Animated Graphics）是腾讯自主研发的一套完整的动画工作流解决方案。

我会通过以下几点的调研来评估哪个框架对于我们的业务而言是更好的选择。

## 工作流
我们先来看一下使用 lottie 的简单流程。

![](https://upload-images.jianshu.io/upload_images/10024246-68c811ffbbae6b03.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


PAG 的流程。

![](https://upload-images.jianshu.io/upload_images/10024246-ee52960611b35b49.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


从两个框架的流程图可以看出，工作流大体相似，如

- 由 AE 生成动画

- 附带 AE 插件，可以通过插件导出动画

    - lottie 的 bodymovin

    - PAG 的 PAG Exporter

- 预览

- 导出文件

- 端上的渲染 SDK

## 配置文件
工作流两者大体相似。所以我将注意力放在了两者中很显著的不同点。即 AE 导出的配置文件。

- lottie 选择了 json

- PAG 选择了类似于 `protocolbuff `的自研二进制文件。

本篇文章会着重从配置文件的大小和解析性能等角度来分析两者的性能。

lottie 的 json 配置文件与 PAG 二进制配置文件
先看一个简单动画。
![](https://upload-images.jianshu.io/upload_images/10024246-d7d3aaad0f8639ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


动画大致流程如下：

- 0s 至 3s，scale 属性值从 100% 变到 50%。

- 3s 至 6s，scale 属性值从 50% 变到 100%，完成动画。

**lottie 的 json**
导出的json 文件大致如下。

![](https://upload-images.jianshu.io/upload_images/10024246-d6e2364873ddfa8c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**PAG**
由于 PAG 生成的是二进制文件，所以需要对应的软件 PAGViewer 来查看。

我这里就不显示 PAG 的导出文件了。

**文件尺寸比对**
从刚才同样动画生成的文件来看。

- lottie 导出的 json 文件差不多 5kb。

- PAG 导出的二进制文件只有 2kb。

差不多少了60%的尺寸。

当然，孤例不能充分证明。我通过调研发现有类似的benchmark. 结果如下。

![](https://upload-images.jianshu.io/upload_images/10024246-7ae78f306fb715e3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到在导出的配置文件上，PAG 在文件尺寸和压缩比率上还是有比较大的优势。我思考可能原因如下，PAG 采用了二进制的数据结构来存储动画信息。二进制数据结构能够非常方便的单文件集 成任何资源，在解码速度上比 Lottie 所使用的 JSON 文本数据快几十倍。

而在文件大小上，PAG 通过利用动画文件本身的特点，获得了极高的压缩率。通过跳过大 量默认值的存储，使用动态比特位来紧凑存储，相同动画内容可以比同类型方案平均减少 50%左右的文件大小。

为何 PAG 生成的动画文件如此小
这让我产生了极大地兴趣。阅读了 PAG 相关的文档，大致归纳如下。

 -PAG 的生成的文件是纯二进制文件，解析采用自己的解析引擎

- 由于采取了二进制格式，所以避免了 json 格式无法避免的冗余字段

- PAG 采用动态比特位压缩，所以压缩比极高

- PAG 还支持图片和音频信息编码

所以在动画文件的这一层面，PAG 无疑是更优秀的选择

## 渲染性能
经过我调研了 lottie 和 PAG 的底层渲染机制。他们的区别大致如下。

Lottie和渲染层面的实现依赖平台端接口，因此不同平台会存在支持的AE特性有所差异、渲染效果不一致等问题。PAG渲染层面使用C++实现，所有平台共享同一套实现，平台端只是封装接口调用，提供渲染环境，因此PAG所有平台支持特性一致，渲染效果一致。并且 lottie 的 bodymovin 支持的AE特性不全，也不支持文本，序列帧等。

所以初步来看，PAG 的性能应该会更优秀。但是光从调研角度并不严谨，所以还是需要实际的测试才能论证。

矢量动画渲染性能

![](https://upload-images.jianshu.io/upload_images/10024246-cdb6f85b4a5f1bb6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
可以发现几个重要的指标。

- 文件解码耗时

- lottie 的 json 对比 PAG 的二进制文件有巨大的劣势，解码速度几乎落后 PAG 的 20 倍

- 首帧渲染耗时 lottie 比 PAG 落后 6 倍

- 每帧渲染耗时两者近似

- 总耗时上 lottie 比 PAG 落后差不多 9 倍

- 内存两者接近

### 不同端的渲染效果
不同端的渲染效果指在 iOS 端， android 端或者 web 端，能否通过同一个动画文件得到同样的动画效果。

PAG的整套动画方案是基于C++跨平台架构研发的，一直从最底层的动画插值器，还原到上层的时间轴和图层渲染树系统，虽然开发成本较高，但是所有端共享同一套代码，天然的能保障跨端渲染一致性。

但是 lottie 采用了各端使用 native 语言的研发策略，从而导致，不同端上的渲染效果由于平台的差异性，很难达到一致。所以这里 PAG 的多端一致性更优秀。

### 时间静态区间的不同策略
在动画文件的特效中，其实大部分的动画素材实际上并不是整个时间轴上都在变化，或多或少会存在一些画面静止的区间。那么，此时其实就有比较大的优化空间。这里我调研了两个框架不同的处理方式。

- PAG在刷新时，如果遇到这些静态区间，会直接返回上一帧的动画内容，自动跳过任何重复的绘制。极限情况下，假设有一个一分钟的动画素材，但实际上全程都是静止的。它对PAG来说就相当于一张静态图片，整个刷新的过程中都是0开销。

- Lottie方案中，整个刷新过程都是全量的开销，因为它每帧都会清空屏幕重新刷新。

这也就意味着如果动画中静止区间较多，那么 PAG 会在渲染性能上比 lottie 有更低的系统开销。

### web 端的支持
刨除 iOS 和 Android 端，其实 web 也是很重要的一个平台。熟悉 lottie 的人都知道，其实lottie 在 web 端有很多所谓的优化技巧，其实这些技巧从某些层面反倒是一些 lottie 本来应该规避的地方。

我们可以对比一下两者的不同 web 实现思路。

- web 端，Lottie使用Web的HTML、CSS 和 Javascript 重新实现了一遍。

- PAG 内部C++层通过 OpenGL ES 实现了纹理绘制、抗锯齿、渐变色、blend 等功能，同样也可以应用于 Web 层，不需要用 Web 相关的技术重复实现。具体方案是把 C++ 代码编译成 WebAssembly 运行在 Web 中，这里使用了 Emscripten（https://emscripten.org/ ） 实现编译。先用 CMake 把 libPAG 编译成静态库，再用 emcc 把静态库和胶水层代码连接生成 wasm 文件，这样我们就拥有了一个完整版的 libPAG 库，

可以看出，由于 PAG 将 C++ 代码编译成 WebAssembly运行，所以在 web 层面，PAG 也获得了比较高的性能，但是 lottie 使用了原生的三件套来实现 web 端动画，性能往往会收到限制。

## 总结
在整个工作流中，由于 PAG 在文件生成和解码都使用了 C++ 进行自研，包括利用 C++ 作为底层渲染工具，所以在端上渲染也保持了相对一致的渲染效果。

可以说 PAG 这套系统的工作流，能够在使用体验上获得比 lottie 更优秀的效果，而PAG也将在1月14日对外开源，看到本篇文章后感兴趣的小伙伴也可以亲身尝试一下。

官网：https://pag.io/

>版权声明：本文为CSDN博主「java李杨勇」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/weixin_39709134/article/details/122362886