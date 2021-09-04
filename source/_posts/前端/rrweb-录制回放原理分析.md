---
title: rrweb-录制回放原理分析
date: 2021-06-10 16:28:03
category: javascript
---
> 最近在做 web 端录制 和回放的时候遇到一个比较好的库 rrweb, 但是网上的资料相对比较少，撸了遍源码，做个小总结。本文阅读大概会了解到以下几个方面~

*   rrweb 是什么

*   适用场景

*   录制回放实现原理

*   使用 rrweb 应注意的问题

    *   数据量大的问题
*   安全问题

*   rrweb 库组成部分 + 简介

*   rrweb 各个模块对应的技术细节

## 一、rrweb 是什么？

web 端录制回放的一个基础库，即记录页面中的 DOM 结构还有用户操作行为，在远程实现回放。一图胜千言，可看下面的gif 图

![](https://upload-images.jianshu.io/upload_images/10024246-531dc43857e8ac78?imageMogr2/auto-orient/strip)
rrweb 其实实现了 录制页面为用户的操作，回放页面则是根据数据回放用户的操作行为功能的一个库

## 二、适用场景

*   记录⽤户使⽤产品的⽅式并加以分析，进⼀步优化产品。
*   采集⽤户遇到 bug 的操作路径，予以复现。
*   记录 CI 环境中的 E2E 测试的执⾏情况。
*   录制体积更⼩、清晰度⽆损的产品演⽰。

## 三、录制回放基本原理

这一块可以简单地理解为 “快照 + 操作指令”, 一般记录页面状态，我们可以根据时间记录每一时刻页面的 DOM状态，回放的时候根据时间点显示即可，但是一般一个页面DOM 数据是很庞大的，如下图所示，这个还是页面 DOM 比较少的情况
![](https://upload-images.jianshu.io/upload_images/10024246-5f38ab0808f83b54.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
因此 rrweb 采取了另外一种方式，记录初始页面的DOM 状态，或者特定某个时刻的DOM 状态，后续收集的是不同时间点的操作指令 或者 某个时刻 某个DOM 的变化作为一个增量快照，在原先快照的基础上，不断加入根据行为解析的DOM 数据，构建了后续的快照，减少大量数据的存储或传输。

![](https://upload-images.jianshu.io/upload_images/10024246-150dc549dd311f54.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 四、使用 rrweb 应注意的问题

*   数据量大的问题
*   数据安全问题 **关于数据大的问题**，其实rrweb 内部有做了一些处理，比如：
*   根据DOM 变化或者有特定的用户操作行为时才收集数据；
*   数据切片，分片不是单个数据的分片，而是如何把一天的数据或者是连续不刷新页面的数据进行分片，在rrweb里叫snapshot，比如每隔10分钟或者数据超过一定大小之后，进行一次数据分片，可以分开存储，这样在播放的时候，可以获取某一段的数据，不用播放一整天的。
*   节流，对于鼠标移动，页面滚动事件进行了节流
![](https://upload-images.jianshu.io/upload_images/10024246-d488f577813961b6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


除此以外，可自己对收集到的数据进行处理

*   压缩数据，尝试过用 pako.js 进行压缩
*   将数据切片保存在不同的文件存储在云服务器，在播放的时候拉取文件整合数据再播放（这里只是一个想法，还没验证是否可行）

**关于数据安全问题，这个应该加密即可**

## 五、rrweb 组成部分

![](https://upload-images.jianshu.io/upload_images/10024246-56b9987ef66c549a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 六、rrweb 各个模块对应的技术细节

**1、rrweb-snapshot** 提供了 snapshot, resbuid接口，snapshot遍历 页面DOM 返回当前页面 DOM 视图的一个序列化的数据结构, rebuild, 则解析特定的数据还原DOM, 并插入文档中 例如:
![](https://upload-images.jianshu.io/upload_images/10024246-df41aae1a9483478.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**这个模块的功能主要有：** **a) snapshot 方法**

*   为每个节点提供一个id，并在快照完成时返回id的节点映射
*   相对路径的处理，将href，src，CSS中的相对路径设为绝对路径
*   将页面引用的样式变为内联样式，以确保可以使用本地样式
*   将一些DOM状态内联到HTML属性中，例如HTMLInputElement的值
*   将script标记转换为noscript标记，以避免脚本被执行。

**第一点:Snapshot 通过 takeFullSnapShotg构建页面 DOM 树，同时生成了 id -> Node 的映射，即在构建 DOM 树时为每个节点生成一个唯一的id, 同时根据 id 生成一份映射，这个映射只要是为了方便后续的增量快照操作**

![](https://upload-images.jianshu.io/upload_images/10024246-a321dd2713c53e43.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/10024246-6d5e38750a19ff27.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/10024246-a31d57c9aacb7511.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**第二点: 将href，src，CSS中的相对路径设为绝对路径** 将一些脚本，样式，图片等引用的相对路径改为绝对路径 比如：

![](https://upload-images.jianshu.io/upload_images/10024246-d6efbc85d3cefed6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


那经过转换之后，回放时，图片的链接地址已经变为之前域名下的地址
![](https://upload-images.jianshu.io/upload_images/10024246-d9a63c60cc2b74a6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



**第三点: 将页面引用的样式变为内联样式，以确保可以使用本地样式** 将页面引用的样式读取变为内联样，例如

![](https://upload-images.jianshu.io/upload_images/10024246-c3f9f0f9ebe12c45.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-6d28029c2d8a4d4b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


除此以外，还有一种样式逻辑，即通过 CssStyleSheet.sheet.insertRule的形式插入页面的样式：

![](https://upload-images.jianshu.io/upload_images/10024246-3c5c6d5db7128405.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-24b0fe182f9ee94f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**第四点：将一些DOM状态内联到HTML属性中，例如HTMLInputElement的值**

记录没有反映在 HTML 中的视图状态。例如 <input type="checkbox" disabled=""> 输⼊后的值不会反映在其 HTML中，我们需要读取其 value 值并加以记录
![](https://upload-images.jianshu.io/upload_images/10024246-dbb125662a98c2b4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**第五点：将script标记转换为noscript标记，以避免脚本被执行** 在播放录制页面时，页面的脚本是不能够被执行的，需要禁掉

**b) rebuild方法** 通过创建Dom, 设置属性等，并且将对应的DOM 插入文档中
![](https://upload-images.jianshu.io/upload_images/10024246-b27823a13ad7ba88.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**2、Rrweb** 提供了 record 和 replay 功能。 **a) record 方法:** 前面说到增量快照，那增量数据是怎么收集的呢？开始录制之后，会针对当前页面生成一个DOM 快照，然后开始监听用户操作和页面DOM的变化。 监听行为如下：

*   DOM 变动
    *   节点创建、销毁
    *   节点属性变化
    *   文本变化
*   鼠标移动
*   鼠标交互
    *   mouse up、mouse down
    *   click、double click、context menu
    *   focus、blur
    *   touch start、touch move、touch end
*   页面或元素滚动
*   视窗大小改变
*   输入

![](https://upload-images.jianshu.io/upload_images/10024246-a1f96100d7eb6448.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**监听的方式有：** **（1）MutationObserver** 其中，监听 DOM 变化 主要是通过 API --- MutationObserver 来实现。当监视的DOM 发生变动时, MutationObserver 将收到通知并触发预先设定好的回调参数，与 addEventListener 方法 比较相似 例如：

![](https://upload-images.jianshu.io/upload_images/10024246-7a8e19620131f1ce?imageMogr2/auto-orient/strip)


如上图所示，当我们尝试改变页面 DOM 的属性，或者新增 DOM 节点的时候，都会对应生成一条 MutationObserver record, record 记录了一些变动信息~ 在 rrweb 中， 对每一条mutation record 做了几下处理

![](https://upload-images.jianshu.io/upload_images/10024246-ab7cd5036a9fac11.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


针对不同的类型进行处理， characterData 是节点内容或节点文本变动，attributes 是节点属性的变动，childList 是子节点的变动，包括新增子节点，移除子节点，移动子节点等。

**新增节点的逻辑** 在初始记录时，生成了页面快照同时维护一个 id -> Node 的映射， 因此当出现新增节点时，无需重新完整地生成一份快照，而只需要将新节点序列化并加入映射中即可。 由于MutationObserver触发方式为批量异步回调，具体来说就是会在一系列 DOM 变化发生之后将这些变化一次性回调，传出的是一个 mutation 记录数组，那在序列化的时候，会存在重复记录的问题 例如以下例子中

![](https://upload-images.jianshu.io/upload_images/10024246-43c8030c6fae3523.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


以下两种方式都可以生成这种DOM 结构

> 1、创建节点 n1 并 append 在 body 中，再创建节点 n2 并 append 在 n1 中。 2、创建节点 n1、n2，将 n2 append 在 n1 中，再将 n1 append 在 body 中。

MutationObserver 对这两种Dom 操作方式的输出记如下图所示

**第一种方式 record 记录**

![](https://upload-images.jianshu.io/upload_images/10024246-5562ab77162accd0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


这种情况下虽然 n1 append 时还没有子节点，但是由于上述的批量异步回调机制，当我们处理 mutation 记录时获取到的 n1 是已经有子节点 n2 的状态

**第二种方式 record 记录**

![](https://upload-images.jianshu.io/upload_images/10024246-2828716b59a4db50.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在处理序列化的过程中，在处理新增节点时必须遍历其所有子孙节点，才能保证所有新增节点都被记录，但是这一策略应用在第一种情况中就会导致 n2 被作为新增节点记录两次，回放时就会产生与原页面不一致的 DOM 结构，因此，为避免这种情况， rrweb 中采取了‘惰性’新增节点，即在遍历 mutation record 时，不会立马去序列化新增的节点，而是收集新增的节点，在遍历完mutation 所有记录的时候再统一去重，序列化新增节点。

![](https://upload-images.jianshu.io/upload_images/10024246-def42a977d4b06b0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**删除节点的逻辑**

![](https://upload-images.jianshu.io/upload_images/10024246-c5e988bc55da76a1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


1、 设置了 blocked 属性的话，不处理 2、 待移除节点还没被序列化，则说明是在本次 callback 中新增的节点，无需记录，并且从新增节点池中将其移除 3、 如果target在待移动节点集合中但是节点映射中又找不到这个子节点，说明子节点在mutation 回调之前已经被移除，target 在将来被序列化时是没有子节点的 4、 如果父节点已经被移除了，说明子节点也已经被移除了，不需要处理 5、 如果节点在待移动的节点结合中，则说明尚未序列化，从待移动的节点集合中删除即可 6、 以上条件都不符合时将节点添加入待移除节点集合中 7、 从 node 节点映射中去掉该节点的映射

**（2）鼠标移动，鼠标交互，页面滚动，视窗大小这些则通过 事件绑定的形式去监听，如下面页面滚动的监听**

![](https://upload-images.jianshu.io/upload_images/10024246-e3564150d975e8f3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/10024246-cd30cb916b1fc455.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


on 方法也是通过 addEventListener 的形式监听

**b) replay** 解析收集到的events 集合，进行还原 收集到的事件类型有

*   DomContentLoaded
*   Load
*   FullSnapshot
*   IncrementalSnapshot
*   Meta 其中 当事件类型为 FullSnapshot时，会调用rebuild, 根据快照数据生成页面的DOM, 当事件类型为 IncrementalSnapshot 时，则说明是增量快照，即收集的数据只是DOM 的变化数据或者对应的用户行为数据，根据不同的数据类型做对应的节点插入，删除，节点属性的更改等

![](https://upload-images.jianshu.io/upload_images/10024246-43238f94909ae8b3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 七、写在最后

第一次阅读源码，写分析，可能比较粗糙，若有错误之处，欢迎指正。也欢迎一起交流，相关的问题也可以到github 上向作者提问哦，作者响应问题速度也是挺快的。

## 八、参考资料

[zhuanlan.zhihu.com/p/60639266](https://link.juejin.cn/?target=https%3A%2F%2Fzhuanlan.zhihu.com%2Fp%2F60639266 "https://zhuanlan.zhihu.com/p/60639266") [github.com/rrweb-io](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Frrweb-io "https://github.com/rrweb-io")

>作者：默翁
链接：https://juejin.cn/post/6844903925213036552
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
