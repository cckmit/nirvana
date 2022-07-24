---
title: canvas前端图片合成-Fabric.js
date: 2021-06-10 16:33:52
categories: 前端
---
Fabric.js是一个可以简化Canvas程序编写的库,官方文档地址[http://fabricjs.com/docs/](http://fabricjs.com/docs/)

暂时只支持纯图片的编辑合成

###### 支持设置画布的大小

![](https://upload-images.jianshu.io/upload_images/10024246-e7ecd000a4d55435.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


点击上传背景图片按钮 上传背景图

使用new FileReader()方法，把本地图片转换成base64图片，并且push到data数据中

点击添加画布按钮，push一条新的画布数据，数据是一个对象，包含canvas字段，用来储存Fabric实例，支持图片拖拽的形式把图片添加到画布中

拖动图片的时候使用dragend监听拖拽结束后的事件，在画布中使用dragenter监听鼠标是否进入，如果进入并且拖拽动作结束，获取图片的base64地址并且通过new fabric.Image.fromURL方法添加图片，并且捕获鼠标的坐标减去鼠标距离图片顶部和左边的距离，就得到图片在canvas中具体的位置，通过设置top和left

通过.on()方法监听图片旋转，缩放，方法接受2个参数，第一个为事件名，第二个为回调函数

| 方法名 | 说明 |
| --- | --- |
| "mouse:down" | 鼠标按下时 |
| "mouse:up" | 鼠标抬起时 |
| "mouse:move" | 鼠标移动时 |
| "object:modified" | 对象修改后 |
| "object:moving" | 对象移动 |
| "object:added" | 对象被加入 |
| "object:selected" | 对象被选中 |
| "object:rotating" | 对象被旋转 |
| "object:scaling" | 对象缩放 |

#### 判断是否选中对象，使用以下方法调整图片层级

| 方法名 | 说明 |
| --- | --- |
| bringForward() | 图层上移 |
| sendBackwards() | 图层下移 |
| bringToFront() | 图层置顶 |
| sendToBack() | 图层置底 |

#### 编辑图片，下面小画布列表也会同时等比例变化

先获取大画布和小画布的比例，按照画布比例缩放移动小画布图片
