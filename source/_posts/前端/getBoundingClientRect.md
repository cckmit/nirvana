---
title: getBoundingClientRect
date: 2021-11-28 21:12:40
category: javascript
---
getBoundingClientRect() 方法返回元素的大小及其相对于视口的位置。

#### API

`let DOMRect = object.getBoundingClientRect()`

它的返回值是一个 DOMRect 对象，这个对象是由该元素的 getClientRects() 方法返回的一组矩形的集合，就是该元素的 CSS 边框大小。返回的结果是包含完整元素的最小矩形，并且拥有 left, top, right, bottom, x, y, width, 和 height 这几个以像素为单位的只读属性用于描述整个边框。除了 width 和 height 以外的属性是相对于视图窗口的左上角来计算的。
![](https://upload-images.jianshu.io/upload_images/10024246-b71bf6b9f5ccaa33.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 应用场景

1、获取 dom 元素相对于网页左上角定位的距离

以前的写法是通过 offsetParent 找到元素到定位父级元素，直至递归到顶级元素 body 或 html。

```
// 获取dom元素相对于网页左上角定位的距离
function offset(el) {
  var top = 0
  var left = 0
  do {
    top += el.offsetTop
    left += el.offsetLeft
  } while ((el = el.offsetParent)) // 存在兼容性问题，需要兼容
  return {
    top: top,
    left: left
  }
}

var odiv = document.getElementsByClassName('markdown-body')
offset(a[0]) // {top: 271, left: 136}
```

现在根据 getBoundingClientRect 这个 api，可以写成这样：

```
var positionX = this.getBoundingClientRect().left + document.documentElement.scrollLeft
var positionY = this.getBoundingClientRect().top + document.documentElement.scrollTop
```

2、判断元素是否在可视区域内

```
function isElView(el) {
  var top = el.getBoundingClientRect().top // 元素顶端到可见区域顶端的距离
  var bottom = el.getBoundingClientRect().bottom // 元素底部端到可见区域顶端的距离
  var se = document.documentElement.clientHeight // 浏览器可见区域高度。
  if (top < se && bottom > 0) {
    return true
  } else if (top >= se || bottom <= 0) {
    // 不可见
  }
  return false
}
```
