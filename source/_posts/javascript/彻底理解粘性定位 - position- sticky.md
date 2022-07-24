---
title: 彻底理解粘性定位 - position:sticky
date: 2022-06-05 16:28:03
categories: 前端
---
粘性定位可以被认为是相对定位(position: relative)和固定定位(position: fixed)的混合。元素在跨越特定阈值前为相对定位，之后为固定定位。例如:

```
.sticky-header {  position: sticky;  top: 10px; }
```

在 视口滚动到元素 top 距离小于 10px 之前，元素为相对定位。之后，元素将固定在与顶部距离 10px 的位置，直到视口回滚到阈值以下。

粘性定位常作用在导航和概览信息(标题，表头，操作栏，底部评论等)上。这样，用户在浏览详细信息时，也能看到信息的概览和做一些操作，给用户带来便捷的使用体验。

![](https://upload-images.jianshu.io/upload_images/10024246-468dd13257541ce5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

粘性定位看着很简单，但也很容易出现不生效的情况。为帮助大家彻底理解粘性定位，本文会从 3 个方面来介绍：

*   粘性定位的原理。
*   不生效的情况。
*   具体的例子。

## 原理

为便于理解粘性定位，这里引入四个元素：视口元素，容器元素，粘性约束元素 和 sticky 元素。它们的关系如下：
![](https://upload-images.jianshu.io/upload_images/10024246-7ea2ff91ac089425.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

视口元素：显示内容的区域。会设置宽，高。一般会设置 `overflow:hidden`。
容器元素：离 sticky 元素最近的能滚动的祖先元素。
粘性约束元素：粘性定位的父元素。有时，也会出现粘性约束元素就是容器元素的情况。
sticky 元素：设置了 `position: sticky;` 的元素。

滚动时，sticky 元素设置的 left, right, top, bottom 的值相对的是容器元素。当粘性约束元素滚出视口时，sticky 元素也会滚出视口。

## 不生效的情况

### 情况1: 未指定 top, right, top 和 bottom 中的任何一个值

此时，设置 `position: sticky` 相当于设置 `position: relative`。

要生效，要指定 top, right, top 或 bottom 中的任何一个值。

### 情况2: 垂直滚动时，粘性约束元素高度小于等于 sticky 元素高度

不生效的原因：当粘性约束元素滚出视口时，sticky 元素也会滚出视口。粘性约束元素比 sticky 元素还小，sticky 元素没有显示固定定位状态的机会。

同样的，水平滚动时，粘性约束元素宽度小于等于 sticky 元素宽度时，也不会生效。

### 情况3: 粘性约束元素和容器元素之间存在 overflow: hidden 的元素

该情况的示例代码:

```
<div class="viewport">
  <div class="container"> <!-- 容器元素 -->
    <div style="overflow: hidden">
      <div> <!-- 粘性约束元素 -->
        <div class="stick-elem">...</div>  <!-- sticky 元素 -->
        ...
      </div>
    </div>
  </div>
</div>
```

要生效，要把 `overflow: hidden` 的元素移除。

## 具体的例子

### 例子1: 页面滚动

这个例子，我们来实现页面滚动到文章内容时，文章标题吸顶的效果。

![](https://upload-images.jianshu.io/upload_images/10024246-cc924c2270de6b0c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

HTML 结构如下：

```
<div class="header">网站头部</div>
<!-- 粘性约束元素 -->
<div class="article">
  <!-- sticky 元素 -->
  <h2 class="title">彻底理解粘性定位 - position: sticky</h2>
  <div class="content">...</div>
</div>
<div class="footer">网站底部</div>
```

在这个例子中，视口元素和容器元素都是 `body`。sticky 元素是 `.title`，因此只要在 sticky 元素上设置如下样式即可:

```
.title {
  position: sticky;
  top: 0;
  background-color: #fff;
}
```

### 例子2: 文章列表

这个例子，我们来实现一块区域下有多篇文章，区域滚动到文章内容时，对应的标题和操作按钮吸顶的效果。
![](https://upload-images.jianshu.io/upload_images/10024246-dd20d04937c7d2a4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

HTML 结构如下：

```
<!-- 视口元素 -->
<div class="viewport">
  <!-- 容器元素 -->
  <div class="container">
    <!-- 文章:粘性约束元素 -->
    <div class="article">
      <div class="sticky-header">
        <h2>彻底理解粘性定位 - position: sticky</h2>
        <div class="options">
          <button>转发</button>
          <button>点赞</button>
        </div>
      </div>
      <div class="article-content">...</div>
  </div>
  <!-- 文章 -->
  <div class="article">...</div>
  <div class="article">...</div>
</div>

```

在这个例子中，视口元素是 `.viewport`，容器元素是 `.container`。sticky 元素是 `.sticky-header`。核心样式如下:

```
/* 视口元素 */
.viewport {
  width: 50%;
  overflow: hidden;
  height: 200px;
}
/* 容器元素 */
.container {
  overflow: auto;
  height: 100%;
}
/* 粘性约束元素 */
.article {
  margin-bottom: 10px;
}
/* sticky 元素 */
.sticky-header {
  position: sticky;
  top: 0;
  padding: 5px 0;
  background-color:#fff;
}
```

### 例子3: 甘特图

最后，我们来做个复杂点的例子：甘特图。如下图所示：

![](https://upload-images.jianshu.io/upload_images/10024246-be2f7ce02e4164f6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

需要实现：

*   左侧事项菜单总在最左侧。
*   菜单的头部和时间轴吸顶。
*   时间轴的年总在月的最左边。

实现代码有点多，就不在这里贴了。获取完整源码，关注公众号: 前端GOGOGO，回复: 粘性定位。

## 最后

粘性定位的浏览器兼容性也很好：95.76% 的浏览器支持[1]。大家可以放心的使用~

推荐阅读:

*   position:sticky 粘性定位的几种巧妙应用[2]

### 参考资料

[1]95.76% 的浏览器支持: *https://caniuse.com/css-sticky*
[2]position:sticky 粘性定位的几种巧妙应用: *https://segmentfault.com/a/1190000039858711*
>原文：https://jishuin.proginn.com/p/763bfbd62bc0
