---
title: CSS透明度之rgba和opacity的区别
date: 2022-03-14 16:28:03
categories: 前端
tags: [CSS]
---
在CSS样式中，设置透明度的方式有两种。其一，`opacity`；其二`rgba()`。
`opacity`和`rgba()`在一定程度上是没什么区别。
## 一、怎么使用rgba和opacity
1、opacity

取值在0到1之间，0表示完全透明，1表示完全不透明。
```
.aa{opacity: 0.5;}
```
2、rgba

rgba语法:
- R：红色值。正整数 | 百分数
- G：绿色值。正整数 | 百分数
- B：蓝色值。正整数| 百分数
- A：透明度。alpha 参数是介于 0.0（完全透明）与 1.0（完全不透明）的数字。
```
.aa{background: rgba(255,0,0,0.5);}
```
## 二、rgba和opacity的区别
rgba()和opacity都能实现透明效果，主要区别有以下2点：
- 1、opacity作用于元素，以及元素内的所有内容的透明度，设置的透明度会被子级元素继承 ；rgba()只作用于元素的颜色或其背景色。（设置rgba透明的元素的子元素不会继承透明效果！）
- 2、rgba 可以设置background-color ,  color , border-color , text-shadow , box-shadow

比如，我们写透明的黑色部分都是用opcity（0.3），但这带出来一个问题就是如果你在这一div上写字的话，然后那个字体也会变成透明色。所以我们采取rgba的样式写，前面三个数字分别对应r,g,b,的三种颜色，第四位的数字对应的是透明的系数。
```
opacity:0.3;
```
![](https://upload-images.jianshu.io/upload_images/10024246-c0cf99861e6fd01a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
background: rgba(1%, 50%, 100%, 0.3)
```

![](https://upload-images.jianshu.io/upload_images/10024246-01eb5f842183b38d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
