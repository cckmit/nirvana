---
title: CSS---移动端1px解决
date: 2021-06-10 16:28:03
category: javascript
---
## 1px原理篇

在讲原理之前，先跟大家说一个概念，就是设备像素比DPR(devicePixelRatio)是什么

> DPR = 设备像素 / CSS像素(某一方向上)

这句话看起来很难理解，可以结合下面这张图（1px在各个DPR上面的展示），一般我们h5拿到的设计稿都是750px的，但是如果在DPR为2的屏幕上，手机的最小像素却是要用2 * 2px来进行绘制，这也就导致了为什么1px会比较粗了。

![](https://upload-images.jianshu.io/upload_images/10024246-542ffe9f42b7f7dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 解决方法

解决办法有很多种，在这里帮大家比较下方案：

| 方案 | 优点 | 缺点 |
| --- | --- | --- |
| 使用0.5px实现 | 代码简单，使用css即可 | IOS及Android老设备不支持 |
| 使用border-image实现 | 兼容目前所有机型 | 修改颜色不方便 |
| 通过 viewport + rem 实现 | 一套代码，所有页面 | 和0.5px一样，机型不兼容 |
| 使用伪类 + transform实现 | 兼容所有机型 | 不支持圆角 |
| box-shadow模拟边框实现 | 兼容所有机型 | box-shadow不在盒子模型，需要注意预留位置 |
## 使用伪类 + transform实现
```
// 左边框，如果需要修改边框位置，可以修改元素top，left，right，bottom的值即可
&::before {
    position: absolute;
    top: 0;
    left: 0;
    content: '\0020';
    width: 100%;
    height: 1px;
    border-top: 1px solid #E9E9E9;
    transform-origin: 0 0;
    overflow: hidden;
}

@media (-webkit-min-device-pixel-ratio: 1.5) and (-webkit-max-device-pixel-ratio: 2.49) {
    &::before {
      transform: scaleY(0.5);
    }
}

@media (-webkit-min-device-pixel-ratio: 2.5) {
    &::before {
      transform: scaleY(0.33333);
    }
}
```
