---
title: Chrome-DevTools：CSS-Overview
date: 2021-06-10 16:28:03
category: javascript
---
## CSS Overview 介绍
在 chrome 的管理面板中，开启 CSS Overview 面板后，就可以查看当前网站的样式信息，包括：颜色信息、字体信息、媒体查询等，对于开发页面来说，非常好用。用来看看别人的优秀的网站样式信息，也很方便！下面是 CSS Overview 面板显示的网页样式信息截图：
![](https://upload-images.jianshu.io/upload_images/10024246-0c9a047af53b60d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 如何使用此功能
---
启用此功能：
- 从`“DevTools实验（Experiments pane）”`窗格中启用`CSS Overview`（Cmd + Shift + P>Show Experiments）
- 从`“DevTools”`Command Menu中选择`“Show CSS Overview”`（Cmd + Shift + P）
![](https://upload-images.jianshu.io/upload_images/10024246-1a7d237996867335.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


在CSS Overview面板中，您可以导航到不同的部分：

- **概述摘要（Overview Summary）**-CSS上有趣的指标，例如元素数量，样式表，类vs ID选择器，复杂选择器等等。
- **颜色（Colors）**-可视化预览背景色、文字色、填充色和边框色。颜色本身是可以点击的，所以你可以准确地查看哪些元素使用了这些颜色。
- **字体信息（Font info）** -衡量字体的使用情况以及它们在样式表中出现的频率。包括字体重量和行高指标。可以选择字体指标来显示受影响的元素。
- **未使用的声明（Unused declarations）**-未使用的CSS声明，可以照常单击。
- **媒体查询（Media queries）**-CSS媒体查询的细节（如最小/最大宽度值）以及它们在样式表中出现的频率。你可以点击这些来直接跳到源面板。如果你启用了源映射，你将能够看到原始样式，例如Sass。
## 何时使用此功能
当`重构`你的代码，或规范各页面的品牌风格时，请使用此功能。例如，如果你注意到一种『颜色』的轻微变化散布在你的CSS中，概览面板中的这个颜色面板（Colors pane）是识别这种东西的好地方。

您还可以使用CSS概览面板中的媒体查询面板来检查您是否针对预期的媒体查询断点集，并确保您的页面在各种屏幕尺寸上看起来都很好。

`未使用`的声明面板可能会通过告知您可以删除哪些CSS来帮助改善网络和渲染性能。

最后，你可以使用CSS概览面板向你的前端团队的其他成员，特别是新入职者传达CSS代码的状态信息，包括可能需要重点关注的领域。

CSS概览面板可以提供关于CSS的有价值的指标，而Lighthouse面板则提供整个网站的指标，包括JavaScript。
