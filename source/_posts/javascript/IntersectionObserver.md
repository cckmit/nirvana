---
title: IntersectionObserver
date: 2021-11-29 16:21:13
categories: 前端
---
网页开发时，常常需要了解某个元素是否进入了"视口"（viewport），即用户能不能看到它。

传统的实现方法是，监听到 scroll 事件后，调用目标元素的 getBoundingClientRect()方法，得到它对应于视口左上角的坐标，再判断是否在视口之内。这种方法的缺点是，由于 scroll 事件密集发生，计算量很大，容易造成性能问题。

目前有一个新的 IntersectionObserver API，可以自动"观察"元素是否可见，Chrome 51+ 已经支持。由于可见（visible）的本质是，目标元素与视口产生一个交叉区，所以这个 API 叫做"交叉观察器"。

#### API

IntersectionObserver 是浏览器原生提供的构造函数，接受两个参数：callback 是可见性变化时的回调函数，option 是配置对象（该参数可选）。

```
var io = new IntersectionObserver(callback, option)

// 开始观察
io.observe(document.getElementById('example'))

// 停止观察
io.unobserve(element)

// 关闭观察器
io.disconnect()
```

如果要观察多个节点，就要多次调用这个方法。

```
io.observe(elementA)
io.observe(elementB)
```

目标元素的可见性变化时，就会调用观察器的回调函数 callback。callback 一般会触发两次。一次是目标元素刚刚进入视口（开始可见），另一次是完全离开视口（开始不可见）。

```
var io = new IntersectionObserver((entries) => {
  console.log(entries)
})
```

callback 函数的参数（entries）是一个数组，每个成员都是一个 IntersectionObserverEntry 对象。举例来说，如果同时有两个被观察的对象的可见性发生变化，entries 数组就会有两个成员。

*   time：可见性发生变化的时间，是一个高精度时间戳，单位为毫秒
*   target：被观察的目标元素，是一个 DOM 节点对象
*   isIntersecting: 目标是否可见
*   rootBounds：根元素的矩形区域的信息，getBoundingClientRect()方法的返回值，如果没有根元素（即直接相对于视口滚动），则返回 null
*   boundingClientRect：目标元素的矩形区域的信息
*   intersectionRect：目标元素与视口（或根元素）的交叉区域的信息
*   intersectionRatio：目标元素的可见比例，即 intersectionRect 占 boundingClientRect 的比例，完全可见时为 1，完全不可见时小于等于 0

举个例子

```
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Document</title>
    <style> #div1 {
        position: sticky;
        top: 0;
        height: 50px;
        line-height: 50px;
        text-align: center;
        background: black;
        color: #ffffff;
        font-size: 18px;
      } </style>
  </head>

  <body>
    <div id="div1">首页</div>
    <div style="height: 1000px;"></div>
    <div id="div2" style="height: 100px; background: red;"></div>
    <script> var div2 = document.getElementById('div2')
      let observer = new IntersectionObserver(
        function (entries) {
          entries.forEach(function (element, index) {
            console.log(element)
            if (element.isIntersecting) {
              div1.innerText = '我出来了'
            } else {
              div1.innerText = '首页'
            }
          })
        },
        {
          root: null,
          threshold: [0, 1]
        }
      )

      observer.observe(div2) </script>
  </body>
</html>
```

相比于 getBoundingClientRect，它的优点是不会引起重绘回流。兼容性如下

![](https://upload-images.jianshu.io/upload_images/10024246-32c0a5c9662f1d99.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 图片懒加载

图片懒加载的原理主要是判断当前图片是否到了可视区域这一核心逻辑实现的。这样可以节省带宽，提高网页性能。传统的突破懒加载是通过监听 scroll 事件实现的，但是 scroll 事件会在很短的时间内触发很多次，严重影响页面性能。为提高页面性能，我们可以使用 IntersectionObserver 来实现图片懒加载。

```
const imgs = document.querySelectorAll('img[data-src]')
const config = {
  rootMargin: '0px',
  threshold: 0
}
let observer = new IntersectionObserver((entries, self) => {
  entries.forEach((entry) => {
    if (entry.isIntersecting) {
      let img = entry.target
      let src = img.dataset.src
      if (src) {
        img.src = src
        img.removeAttribute('data-src')
      }
      // 解除观察
      self.unobserve(entry.target)
    }
  })
}, config)

imgs.forEach((image) => {
  observer.observe(image)
})
```

#### 无限滚动

无限滚动（infinite scroll）的实现也很简单。

```
var intersectionObserver = new IntersectionObserver(function (entries) {
  // 如果不可见，就返回
  if (entries[0].intersectionRatio <= 0) return
  loadItems(10)
  console.log('Loaded new items')
})

// 开始观察
intersectionObserver.observe(document.querySelector('.scrollerFooter'))
```
