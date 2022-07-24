---
title: Html的area标签
date: 2022-05-27 16:28:03
categories: 前端
---
`area` 这个标签也非常有意思，它的作用是为图片提供点击热区，可以自己规定一张图的哪些区域可点击，且点击后跳转的链接，也可以设置成点击下载文件，我们来举个例子：
```
<img src="planets.gif" width="145" height="126" alt="Planets" usemap="#planetmap">

<map name="planetmap">
  <area shape="rect" coords="0,0,82,126" alt="Sun" href="sun.htm">
  <area shape="circle" coords="90,58,3" alt="Mercury" href="mercur.htm">
  <<area shape="poly" coords="100,0,100,126,190,58,100,0" alt="Venus" href="venus.htm">
</map>
```
`area` 一般要搭配 `map` 标签一起使用，每个 `area` 标签表示一个热区，例如上面代码中，我们定义了三个热区，热区形状都为`rect`（矩形），他们的热区分别是：

*   坐标 `(0,0)` 到坐标 `(82,126)` 的一个矩形
*   坐标 `(90,58)`  半径为8的圆形
*   坐标`(100.0),(100,126),(190,58),(100,0)`的一个三角形区域

我们都知道，默认的坐标轴是这样的：

![](https://upload-images.jianshu.io/upload_images/10024246-5b360e14782aecce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

因此，我们划分的三个热区就是:

![](https://upload-images.jianshu.io/upload_images/10024246-90c8c52edd8e3675.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`area`热区属性：
属性值：

- href : 点击区域跳转的链接。alt : 图片无法正常显示时提示的信息。

- shape & coords:

  - 1、距形：(左上角顶点坐标为(x1,y1)，右下角顶点坐标为(x2,y2))

  - 2、圆形：(圆心坐标为(X1,y1)，半径为r)

  - 3、多边形：(各顶点坐标依次为(x1,y1)、(x2,y2)、(x3,y3) ......)