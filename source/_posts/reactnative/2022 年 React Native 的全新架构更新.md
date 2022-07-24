---
title: 2022 年 React Native 的全新架构更新
date: 2022-02-21 21:12:40
categories: 
    - [react-native]
    - [资讯]
---
> *内容参考： [medium.com/coox-tech/d…](https://medium.com/coox-tech/deep-dive-into-react-natives-new-architecture-fb67ae615ccd)*

随着 RN 团队关于 [深入了解 React Native 的新架构](https://medium.com/coox-tech/deep-dive-into-react-natives-new-architecture-fb67ae615ccd) 文章的发布，这次新架构带来的调整主要在于以下四点：

*   1.  JavaScript Interface(JSI)
*   2.  Fabric
*   3.  Turbo Modules
*   4.  CodeGen

在 RN App 里，所有的 JS 代码都会打包成一个 JS Bundle 文件保存在本地运行，当 RN App 运行时，一般会有三个线程：

*   1、 JavaScript 线程：属于 JS 引擎，用于运行 JS Bundle ；
*   2、 Native/UI 线程：运行 Native Modules 和处理 UI 渲染、用户手势等操作；
*   3、 Shadow 线程：在渲染之前计算元素的布局；

![](https://upload-images.jianshu.io/upload_images/10024246-a538c1d6730fe2d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在 RN 里 JS 线程和 Native 线程之前是通过 `bridge` 来交互，而交互的数据必须被转化为 JSON，而这个桥只能处理异步通信。

> JavaScriptCore：*JavaScript 引擎，React Native 用它执行 JS 代码；*
> 
> Yoga：*布局引擎，计算UI位置；*

## 一、JavaScript Interface (JSI)

目前 RN 使用 Bridge Module 来让 JS 和 Native 线程进行通信，每次利用 Bridge 发送数据时，都需要转换为 JSON， 而收到数据时也需要进行解码。

**这就意味着 JavaScript 和 Native 直接是隔离的，也就是 JS 线程不能直接调用 Native 线程上的方法**。

另一个就是；**通过 Bridge 发送的消息本质上是异步的**，如果需要 JS 代码和 Naitve 同步执行在之前是无法实现。

> 例如，如果 JS 线程需要访问 native modules（例如蓝牙），它就需要向 native 线程发送消息，JS 线程就会通过 Bridge 发送一个 JSON 消息，然后消息在 native 线程上进行解码，最终将执行所需的 native 代码。

而在全新架构中，**Bridge 将被一个名为 JavaScript Interface 的模块所代替，它是一个轻量级的*通用层***，用 C++ 编写，JavaScript Engine 可以使用它直接执行或者调用 native。

> *通用层*代表着：JSI 让 JavaScript 接口将与 Engine 分离，这意味着新架构支持 **RN 直接使用其他 JavaScript 引擎，比如 `Chakra`、`v8`、`Hermes` 等等**。

### 那 JSI 如何让 JavaScript 直接调用到原生方法？

**在 JSI 里 Native 方法会通过 C++ Host Objects 暴露给 JS， 而 JS 可以持有对这些对象的引用，并且使用这些引用直接调用对应的方法**。

这就类似于 Web 里 JS 代码可以保存对任何 DOM 元素的引用，并在它上面调用方法：

```
const container = document.createElement(‘div’);

```

> 举个例子，在这里的 `container` 会包含一些在 C++ 中初始化的 DOM 元素的引用，这时候如果我们调用 `container` 上的任何方法，它就会调用 DOM 元素上的方法。

JSI 就是以类似的方式运行，JSI 将允许 JS 代码保存对 Native Modules 的引用，并且 JS 可以直接通过引用去调用 Native 上的方法。

![](https://upload-images.jianshu.io/upload_images/10024246-2aed5909ed647449.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


总结起来就是：

*   **JSI 将支持其他 JS 引擎**；
*   **JSI 允许线程之间的同步相互执行，不需要 JSON 序列号等耗费性能的操作**；
*   **JSI 是用 C++ 编写，以后如果针对电视、手表等其他系统，也可以很方便地移植**；

## 二、Fabric

Fabric 是新的渲染系统，它将取代当前的 UI Manager。

在 Fabric 之前，当 App 运行时，React 会执行你的代码并在 JS 中创建一个 `ReactElementTree` ，基于这棵树渲染器会在 C++ 中创建一个 `ReactShadowTree`。

> UI Manager 会使用 `Shadow Tree` 来计算 UI 元素的位置，而一旦 Layout 完成，`Shadow Tree` 就会被转换为由 Native Elements 组成的 `HostViewTree`（例如：RN 里的 会变成 Android 中的 `ViewGroup` 和 iOS 中的 `UIView`）。

![](https://upload-images.jianshu.io/upload_images/10024246-8dbc7b25bcc73159.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

而之前线程之间的通信都发生在 Bridge 上，这就意味着需要在传输和数据复制上耗费时间。

> 例如如果一个 `ReactElementTree` 节点恰好是一个 `<Image/>`，那么 `ReactShadowTree` 的节点也会是一个图像，但是这些数据必须被复制并分别存储在两个节点中。

另外由于 JS 和 UI 线程不同步，因此在某些情况下 App 可能会因为丢帧而显得卡顿（例如滚动有大量数据的 `FlatList` ）

**而得益于前面的 JSI， JS 可以直接调用 Native 方法，其实就包括了 UI 方法，所以 JS 和 UI 线程可以同步执行从而提高列表、跳转、手势处理等的性能**。

使用新的 Fabric 渲染，用户交互（如滚动、手势等）可以优先在主线程或 Native 线程中同步执行，而 API 请求等其他任务使用异步执行。

**另外新的 `Shadow Tree` 将成为 immutable，它会在 JS 和 UI 线程之间共享，以两端进行直接交互**。

> 在以前 RN 必须维护两个层次结构的 DOM 节点，但因为现在 `Shadow Tree` 可以共享，在减少内存消耗的部分也会得到相应的优化。

## 三、Turbo Modules

在之前的架构中 JS 使用的所有 Native Modules（例如蓝牙、地理位置、文件存储等）都必须在应用程序打开之前进行初始化，这意味着即使用户不需要某些模块，但是它仍然必须在启动时进行初始化。

Turbo Modules 基本上是对这些旧的 Native 模块的增强，正如在前面介绍的那样，**现在 JS 将能够持有这些模块的引用，所以 JS 代码可以仅在需要时才加载对应模块，这样可以将显着缩短 RN 应用的启动时间**。

## 四、Codegen

**Codegen 主要是用于保证 JS 代码和 C++ 的 JSI 可以正常通信的静态类型检查器**，通过使用类型化的 JS 作为参考来源，CodeGen 将定义可以被 Turbo 模块和 Fabric 使用的接口，另外 Codegen 会在构建时生成 Native 代码，减少运行时的开支。

![](https://upload-images.jianshu.io/upload_images/10024246-b5b5ea8b5441fd9b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**从上面四点可以看到 2022 年 RN 将迎来性能和体验上的跃迁，本次即将到来的全新架构将解决 RN 多年以后被人诟病的各种根本上的设计问题**。

# Skia

另外还要介绍的内容就是 `react-native-skia` ，目前它还处于 alpha release 的阶段，但是它也给 RN 带来的新的可能。

> 众所周知，Flutter 跨平台的性能提升和解耦来自于直接使用 Skia 渲染而非系统控件，而如今 RN 也有类似的支持。

`react-native-skia` 需要 `react-native@>=0.66` 的支持，而目前它上面的操作都还是十分原始的 `canvas` 行为，例如通过 `Circle` 绘制圆形，通过 `blendMode` 配置重叠模式等。

```
import {Canvas, Circle, Group} from "@shopify/react-native-skia";

export const HelloWorld = () => {
  const width = 256;
  const height = 256;
  const r = 215;
  return (
    <Canvas style={{ flex: 1 }}>
      <Group blendMode="multiply">
        <Circle cx={r} cy={r} r={r} color="cyan" />
        <Circle cx={width - r} cy={r} r={r} color="magenta" />
        <Circle
          cx={width/2}
          cy={height - r}
          r={r}
          color="yellow"
        />
      </Group>
    </Canvas>
  );
};

```

![](https://upload-images.jianshu.io/upload_images/10024246-e4152affd72d07ff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当然它也支持直接使用 `SkiaView` ，然后通过 `canvas` 来绘制你需要的图形：

```
import {Skia, BlendMode, SkiaView, useDrawCallback} from "@shopify/react-native-skia";

const paint = Skia.Paint();
paint.setAntiAlias(true);
paint.setBlendMode(BlendMode.Multiply);

export const HelloWorld = () => {
  const width = 256;
  const height = 256;
  const r = 215;
  const onDraw = useDrawCallback((canvas) => {
    // Cyan Circle
    const cyan = paint.copy();
    cyan.setColor(Skia.Color("cyan"));
    canvas.drawCircle(r, r, r, cyan);
    // Magenta Circle
    const magenta = paint.copy();
    magenta.setColor(Skia.Color("magenta"));
    canvas.drawCircle(width - r, r, r, magenta);
    // Yellow Circle
    const yellow = paint.copy();
    yellow.setColor(Skia.Color("yellow"));
    canvas.drawCircle(width/2, height - r, r, yellow);
  });
  return (
    <SkiaView style={{ flex: 1 }} onDraw={onDraw} />
  );
};

```

目前该库支持 Image、Text、Shader、Effects、Shapes、Animations 等操作，而事实上该库的实现和 Flutter 很是相似，比如：

> 在 Android 上的 `SkiaDrawView` 其实就是 `TextureView` ，绘制逻辑是作者自己写的 `reactskia` 上，这里只是借助了 `TextureView` 的 `surface` 和 `TouchEvent` 等的支持。

如下图所示，是关于使用 `react-native-skia` 实现的一段 Demo ，详细可见： [shopify.github.io/react-nativ…](https://shopify.github.io/react-native-skia/)

![image.png](https://upload-images.jianshu.io/upload_images/10024246-630699084d087e23.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以预见目前的 `react-native-skia` 还有不少问题需要解决，但是它让 RN 可以更高效地使用丰富的 `Canvas` 能力，对于 RN 的未来而言不免是一次不错的尝试。

>作者：恋猫de小郭
链接：https://juejin.cn/post/7063738658913779743
