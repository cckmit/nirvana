---
title: CSS matrix函数
date: 2022-04-29 16:28:03
categories: 前端
tags: [CSS]
---
[`matrix()`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/transform-function/matrix)是CSS的`transform`的一个基础属性，用它可以实现很多高级、复杂的效果，实际上`transfrom`的`translate`、`rotate`等都是在`matrix`的基础上实现的简化版的语法。

## 线性代数基础

了解和使用必须熟悉线性代数的向量和矩阵知识，当初学习的线性代数的课程早就还给老师了，因为不知道有什么用，如果知道今天会用到，当初一定会好好学习线性代数、高数等课程。

（1）向量

向量用来描述方向和大小，一般使用笛卡尔坐标系来进行向量的描述，比如向量`(2, 1)`和`(3, 3)`在坐标系中表示如下：

![](https://upload-images.jianshu.io/upload_images/10024246-e99ff1c39730de23.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

向量的加法、减法和乘法都是将对应位置的坐标进行运算：
```
(8, 2) + (2, 6) = (8 + 2, 2 + 6) = (10, 8)

(8, 2) - (2, 6) = (8 - 2, 2 - 6) = (6, -4)

(8, 2) * (2, 6) = (8 * 2, 2 * 6) = (16, 12)

```

（2）矩阵

矩阵是高等代数学中的常见工具，将数字按照长方阵列进行排列，便于进行统计分析等高等数学运算，一个`2 X 3`的矩阵就是说这个矩阵有2行3列

当相同规模的矩阵之间进行相加时，是对应的位置两两相加：

![](https://upload-images.jianshu.io/upload_images/10024246-9b5831ce10c33fcd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

矩阵相乘时，会将前一个矩阵每行对应的位置与后一个矩阵每行对应位置分别相乘，然后将结果相加，得到的矩阵的行数等于左边矩阵的行数，列数等于右边矩阵的列数，例如

![](https://upload-images.jianshu.io/upload_images/10024246-5c368887657c42df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## `matrix`方法

CSS3中的`transfrom`的`matrix()`方法写法如下：
```
transform: matrix(a,b,c,d,e,f);
```

一共有六个参数，它对应了一个`3*3`的矩阵，书写方向是竖向的：

![](https://upload-images.jianshu.io/upload_images/10024246-ef9f697cff3d5649.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


在进行坐标变换时（2D坐标），假设目标点的坐标为`(x, y)`，那么转换公式如下：

![](https://upload-images.jianshu.io/upload_images/10024246-998d9eb4fe7cf30f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

得到的新的矩阵的第一行和第二行就是转换后的横纵坐标。

要注意的是`transfrom-origin`这个属性是变形的原点，它会影响到点的坐标的计算，我们一帮都会将它设置为`0 0`

## 例子

假设有这样一个元素，CSS属性如下：
```
#transformedObject {
  position: absolute;
  left: 0px;
  top: 0px;
  width: 200px;
  height: 80px;
}
```
此时页面效果如下：

![](https://upload-images.jianshu.io/upload_images/10024246-5623a5240718fe75.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们对这个元素进行`transform: matrix()`的变换：
```
#transformedObject {
  position: absolute;
  left: 0px;
  top: 0px;
  width: 200px;
  height: 80px;
  transform: matrix(0.9, -0.05, -0.375, 1.375, 220, 20);
  transform-origin: 0 0;
}
```

> 注意我们设置了`transform-origin`

进行`matrix`运算的原则就和上面提到的一样，以`(200, 80)`的运算过程为例：

![](https://upload-images.jianshu.io/upload_images/10024246-8d487db419d1b6f7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

应用变换后的效果如下：

![](https://upload-images.jianshu.io/upload_images/10024246-a9e4ca8734e45806.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

增加个在线编辑的链接：https://www.useragentman.com/matrix/#

## `matrix`与其他属性的关系

`matrix`是最基本的变化，它有六个参数，这六个参数给定特殊的值，就可以实现`translate`/`rotate`等特殊的效果：

![](https://upload-images.jianshu.io/upload_images/10024246-cc95e90e989cb714.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

以`translate`距离，当坐标点为`(10px, 10px)`，进行`translate(50px 50px)`的变化时，结果的坐标会是`(60px, 60px)`

如果使用`matrix()`进行变化，按照上图，需要给出的参数就是`matrix(1, 0, 0, 1, 50, 50)`，进行运算的过程：
```
1 0 50     10     1 * 10 + 0 * 10 + 50 * 1     60
0 1 50  *  10  =  0 * 10 + 1 * 10 + 50 * 1  =  60
0 0 1      1      0 * 10 + 0 * 10 + 1  * 1     1
```
结果是相同的。

对`matrix`有了了解之后，也就可以使用`matrix`同时进行复杂的、复合的变化，从而能够实现更复杂的动画。

## 参考

*   [matrix()@MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/transform-function/matrix)
*   [【css基础】如何理解transform的matrix()用法@掘金](https://juejin.im/post/5d0ba96df265da1ba252659b)
*   [理解CSS3 transform中的Matrix(矩阵)@张鑫旭的个人主页](https://www.zhangxinxu.com/wordpress/2012/06/css3-transform-matrix-%E7%9F%A9%E9%98%B5/)
