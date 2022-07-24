---
title: CSS-实现切角效果
date: 2022-06-11 21:12:40
categories: 前端
tags: [CSS]
---
就是一个正常的矩形，然后被“切”了一块，**而且是沿着右上角切的**。那么，这种布局如何实现呢？

## 一、自适应方式

这种布局一般有两种自适应方式，当然具体需要哪种可以根据实际设计师需求

### 1\. 固定距离

无论宽高怎么变化，切角距离顶部的**距离**是固定的，如下

![](https://upload-images.jianshu.io/upload_images/10024246-5284b4bc977f195d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 2\. 固定角度

无论宽高怎么变化，切角与顶部的**夹角**是固定的，如下

![](https://upload-images.jianshu.io/upload_images/10024246-8fa0a3febea31142.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


下面具体来看这两种布局的实现

## 二、固定距离的切角

固定距离的比较好实现，只需要借助 [clip-path](https://link.segmentfault.com/?enc=RS9U8FJ0D5lZREGAl7mXJA%3D%3D.ayYuNuHrwb2I7H3JktiBm4eTurRqTuaQyZc3xVPM9ha71jiTLba%2Bdr3%2Fz0lfScEdZpmD%2FhO0LIn6ueOB%2BQxwSQ%3D%3D)就可以了。假设距离顶部的距离是`20px`，那么四个点的坐标是

![](https://upload-images.jianshu.io/upload_images/10024246-92e4d952dfa84335.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


代码实现就是

```
div{
  clip-path: polygon(0 20px, 100% 0, 100% 100%, 0 100%);
}
```

这样就得到了一个固定距离的切角

## 三、固定角度的切角

这个稍微复杂一点。起初，我以为简单的线性渐变就能实现，比如

```
div{
  background: linear-gradient(-30deg, #B89DFF 80%, transparent 0);
}
```

可以看到，角度虽然是固定的，但是切角不会紧贴右上角，原因是线性渐变的起始点是沿着角度与之垂直的最远距离，如下所示（截图取自 MDN 官网）

![image.png](https://upload-images.jianshu.io/upload_images/10024246-130d0f55b3c4ff82.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


所以并不能保证切角的固定相交位置，比较适合那种小切角场景。

那还有其他方式吗？当然也是有的

提到角度，除了线性渐变，还能想到锥形渐变[conic-gradient](https://link.segmentfault.com/?enc=f3tfdwtF4M2Nrtm4n3BYLg%3D%3D.9b%2B92rgXfZutKO0LcM9E0b3x2Vmj4G5QvExCW2P8nj7sjH7y9U%2BtHzDhhJ0P2T1KqYboI14jf2DQOrDpRyhe3XuBQpvt2XlQ7g%2BnarddY%2FI%3D))，可以以某一点绘制锥形图案。假设固定角度是`20度`，示意如下

![](https://upload-images.jianshu.io/upload_images/10024246-c30c0cd41ccd6826.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


那么，锥形渐变的角度就是 250°（270 - 20），代码实现如下

```
div{
  background: conic-gradient(#B89DFF 250deg, transparent 0);
}
```

效果如下

![](https://upload-images.jianshu.io/upload_images/10024246-29aecd4e4c322dc7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


因为锥形渐变默认中心点是容器的中点，我们需要移到右上角，可以通过`at`来指定位置，如下

```
div{
  background: conic-gradient(at 100% 0, #B89DFF 250deg, transparent 0);
}
```

这样就得到了一个固定角度的切角

## 四、总结一下

以上就是这类布局的两种实现方案，主要用到了`clip-path`和`conic-gradient`，下面总结一下

1.  clip-path 可以根据坐标点裁剪矩形
2.  linear-gradient 也能实现切角效果，但并不能紧贴右上角
3.  conic-gradient 可以实现实现任意角度的锥形，同时能指定中心点位置

当然不仅仅局限于此，很多不规则布局都可以朝这个方向思考🤔最后，如果觉得还不错，对你有帮助的话，欢迎点赞、收藏、转发❤❤❤
>原文：https://segmentfault.com/a/1190000041715976



------------------ 原始邮件 ------------------
发件人: "181443866" <lzhw1985@qq.com>;
发送时间: 2022年7月24日(星期天) 晚上10:03
收件人: "181443866"<lzhw1985@qq.com>;
主题: Re:user-10024246-1658658911

## 修改鼠标样式

首先，第一个问题，我们可以看到，上图中，鼠标指针的样式被修改成了一个圆点：

![](https://upload-images.jianshu.io/upload_images/10024246-4e21086bf99f1d88.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


正常而言应该是这样：

![](https://upload-images.jianshu.io/upload_images/10024246-01ae091948bde3dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


当然，这里比较简单，在 CSS 中，我们可以通过 `cursor` 样式，对鼠标指针形状进行修改。

### 利用 `cursor` 修改鼠标样式

cursor [CSS](https://link.segmentfault.com/?enc=4CIlBNfcfzOYxaxSnCTYtw%3D%3D.303UxoPjQJaOuZzYCVh96FvK1RTR%2FImBKIeB3KgkZXVppsGBocMchjoZeVS4LosCDe%2F5i%2BXryCUB%2BG3QmQiqJg%3D%3D) 属性设置鼠标指针的类型，在鼠标指针悬停在元素上时显示相应样式。

```
cursor: auto;
cursor: pointer;
...
cursor: zoom-out;
/* 使用图片 */
cursor: url(hand.cur)
/* 使用图片，并且设置 fallback 兜底 */
cursor: url(hand.cur), pointer;
```

这个大家应该都清楚，通常而言，在不同场景下，选择不同鼠标指针样式，也是一种提升用户体验的手段。

![](https://upload-images.jianshu.io/upload_images/10024246-ded9422056499d99.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/10024246-b110d18e61bb8d2e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


当然，在本交互中，**我们并非要将 cursor 光标设置成任一样式，刚好相反，我们需要将他隐藏**。

### 通过 `cursor: none` 隐藏光标

在这里，我们通过 `cursor: none` 隐藏页面的鼠标指针：

```
{
    cursor: none;
}
```

如此一来，页面上的鼠标指针就消失了：

![](https://upload-images.jianshu.io/upload_images/10024246-acc7c149e2968399.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 通过全局事件监听，模拟鼠标指针

既然，消失了，我们就简单模拟一个鼠标指针。

我们首先实现一个 `10px x 10px` 的圆形 div，设置为基于 `<body>` 绝对定位：

```<div id="g-pointer"></div>```

```
#g-pointer {
    position: absolute;
    top: 0;
    left: 0;
    width: 10px;
    height: 10px;
    background: #000;
    border-radius: 50%;
}
```

那么，在页面上，我们就得到了一个圆形黑点：

![](https://upload-images.jianshu.io/upload_images/10024246-ba6e2e73f33d3024.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


接着，通过事件监听，监听 body 上的 `mousemove`，将小圆形的位置与实时鼠标指针位置重合：

```
const element = document.getElementById("g-pointer");
const body = document.querySelector("body");

function setPosition(x, y) {
  element.style.transform  = `translate(${x}px, ${y}px)`;               
}

body.addEventListener('mousemove', (e) => {
  window.requestAnimationFrame(function(){
    setPosition(e.clientX - 5, e.clientY - 5);
  });
});
```

这样，如果不设置 `cursor: none`，将会是这样一个效果：

![](https://upload-images.jianshu.io/upload_images/10024246-31cb5183acda66b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


再给 body 加上 `cursor: none`，就相当于模拟了一个鼠标指针：
![](https://upload-images.jianshu.io/upload_images/10024246-e6cec1532b8b9b59.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


在这个基础上，由于现在的鼠标指针，实际上是个 `div`，**因此我们可以给它加上任意的交互效果**。

以文章一开头的例子为例，我们只需要借助混合模式 `mix-blend-mode: exclusion`，就能够实现让模拟的鼠标指针能够智能地在不同背景色下改变自己的颜色。

> 对于混合模式这个技巧还有所疑问的，可以看看我的这篇文章：[利用混合模式，让文字智能适配背景颜色](https://link.segmentfault.com/?enc=pync7iJOX3DyuRITHzqaeQ%3D%3D.gl4%2BbmHOChHGRbXxsBGGn%2Fg1NXUhAwEyYz8NSSebRfCSS9ZExmCf1%2FjtBw1Wg2yI)

完整的代码：

```
<p>Lorem ipsum dolor sit amet</p>
<div id="g-pointer-1"></div>
<div id="g-pointer-2"></div>
```

```
body {
    cursor: none;
    background-color: #fff;
}
#g-pointer-1,
#g-pointer-2
{
    position: absolute;
    top: 0;
    left: 0;
    width: 12px;
    height: 12px;
    background: #999;
    border-radius: 50%;
    background-color: #fff;
    mix-blend-mode: exclusion;
    z-index: 1;
}
#g-pointer-2 {
    width: 42px;
    height: 42px;
    background: #222;
    transition: .2s ease-out;
}
```

```
const body = document.querySelector("body");
const element = document.getElementById("g-pointer-1");
const element2 = document.getElementById("g-pointer-2");
const halfAlementWidth = element.offsetWidth / 2;
const halfAlementWidth2 = element2.offsetWidth / 2;

function setPosition(x, y) {
    element.style.transform  = `translate(${x - halfAlementWidth}px, ${y - halfAlementWidth}px)`;       element2.style.transform  = `translate(${x - halfAlementWidth2}px, ${y - halfAlementWidth2}px)`;
}

body.addEventListener('mousemove', (e) => {
  window.requestAnimationFrame(function(){
    setPosition(e.clientX, e.clientY);
  });
});
```

我们就能完美还原出题图的效果：

![](https://upload-images.jianshu.io/upload_images/10024246-9c318d8402a0f6f6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


完整的代码，你可以戳这里：[Mouse Cursor Transition](https://codepen.io/Chokcoco/pen/rNJQXXV)点击预览

### 伪类事件触发

有一点需要注意的是，利用模拟的鼠标指针去 **Hover** 元素，**Click** 元素的时候，会发现这些事件都无法触发。

这是由于，此时被隐藏的指针下面，其实悬浮的我们模拟鼠标指针，因此，所有的 Hover、Click 事件都触发在了这个元素之上。

当然，这个也非常好解决，我们只需要给模拟指针的元素，添加上 `pointer-events: none`，阻止默认的鼠标事件，让事件透传即可：

```
{
    pointer-events: none;
}
```

## 鼠标跟随，不仅于此

当然，这里核心就是一个鼠标跟随动画，配合上 `cursor: none`。

而且，鼠标跟随，我们不一定一定要使用 JavaScript。

我在 [不可思议的纯 CSS 实现鼠标跟随](https://link.segmentfault.com/?enc=N6yp5fFNawN7EoNZjaSF4g%3D%3D.I2jwzrZb1lC7xMb%2FhXqYZb24UlEXok1zVYNxR2JiQhrTOezOF1BmSfPM2AaqeYPz) 一文中，介绍了一种纯 CSS 实现的鼠标跟随效果，感兴趣的也可以看看。

基于纯 CSS 的鼠标跟随，配合 `cursor: none`，也可以制作出一些有意思的动画效果。像是这样：

![](https://upload-images.jianshu.io/upload_images/10024246-6044acabe3fbe368.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


[CodePen Demo -- Cancle transition & cursor none](https://codepen.io/Chokcoco/pen/gOvZoVv)点击预览
>原文：https://segmentfault.com/a/1190000042012558
