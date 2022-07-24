---
title: requestAnimationFrame
date: 2021-11-25 21:12:40
categories: 前端
---
window.requestAnimationFrame() 告诉浏览器——你希望执行一个动画，并且要求浏览器在下次重绘之前调用指定的回调函数更新动画。

## API
该方法需要传入一个回调函数作为参数，该回调函数会在浏览器下一次重绘之前执行。
```
window.requestAnimationFrame(callback)
```
兼容性处理
```
window._requestAnimationFrame = (function () {
  return (
    window.requestAnimationFrame ||
    window.webkitRequestAnimationFrame ||
    window.mozRequestAnimationFrame ||
    function (callback) {
      window.setTimeout(callback, 1000 / 60)
    }
  )
})()
```
结束动画
```
var globalID
function animate() {
  // done(); 一直运行
  globalID = requestAnimationFrame(animate) // Do something animate
}
globalID = requestAnimationFrame(animate) //开始
cancelAnimationFrame(globalID) //结束
```
与 setTimeout 相比，requestAnimationFrame 最大的优势是由系统来决定回调函数的执行时机。具体一点讲，如果屏幕刷新率是 60Hz,那么回调函数就每 16.7ms 被执行一次，如果刷新率是 75Hz，那么这个时间间隔就变成了 1000/75=13.3ms，换句话说就是，requestAnimationFrame 的步伐跟着系统的刷新步伐走。它能保证回调函数在屏幕每一次的刷新间隔中只被执行一次，这样就不会引起丢帧现象，也不会导致动画出现卡顿的问题。这个 API 的调用很简单，如下所示：
```
var progress = 0
//回调函数
function render() {
  progress += 1 //修改图像的位置
  if (progress < 100) {
    //在动画没有结束前，递归渲染
    window.requestAnimationFrame(render)
  }
}
//第一帧渲染
window.requestAnimationFrame(render)
```
## 优点：
- **CPU 节能：**使用 setTimeout 实现的动画，当页面被隐藏或最小化时，setTimeout 仍然在后台执行动画任务，由于此时页面处于不可见或不可用状态，刷新动画是没有意义的，完全是浪费 CPU 资源。而 requestAnimationFrame 则完全不同，当页面处理未激活的状态下，该页面的屏幕刷新任务也会被系统暂停，因此跟着系统步伐走的 requestAnimationFrame 也会停止渲染，当页面被激活时，动画就从上次停留的地方继续执行，有效节省了 CPU 开销。
- **函数节流：**在高频率事件(resize,scroll 等)中，为了防止在一个刷新间隔内发生多次函数执行，使用 requestAnimationFrame 可保证每个刷新间隔内，函数只被执行一次，这样既能保证流畅性，也能更好的节省函数执行的开销。一个刷新间隔内函数执行多次时没有意义的，因为显示器每 16.7ms 刷新一次，多次绘制并不会在屏幕上体现出来。
## 应用场景
- 1、监听 scroll 函数

页面滚动事件（scroll）的监听函数，就很适合用这个 api，推迟到下一次重新渲染。
```
$(window).on('scroll', function () {
  window.requestAnimationFrame(scrollHandler)
})
```
平滑滚动到页面顶部
```
const scrollToTop = () => {
  const c = document.documentElement.scrollTop || document.body.scrollTop
  if (c > 0) {
    window.requestAnimationFrame(scrollToTop)
    window.scrollTo(0, c - c / 8)
  }
}

scrollToTop()
```
2、大量数据渲染

比如对十万条数据进行渲染，主要由以下几种方法：

（1）使用定时器
```
//需要插入的容器
let ul = document.getElementById('container')
// 插入十万条数据
let total = 100000
// 一次插入 20 条
let once = 20
//总页数
let page = total / once
//每条记录的索引
let index = 0
//循环加载数据
function loop(curTotal, curIndex) {
  if (curTotal <= 0) {
    return false
  }
  //每页多少条
  let pageCount = Math.min(curTotal, once)
  setTimeout(() => {
    for (let i = 0; i < pageCount; i++) {
      let li = document.createElement('li')
      li.innerText = curIndex + i + ' : ' + ~~(Math.random() * total)
      ul.appendChild(li)
    }
    loop(curTotal - pageCount, curIndex + pageCount)
  }, 0)
}
loop(total, index)
```
（2）使用 requestAnimationFrame
```
//需要插入的容器
let ul = document.getElementById('container')
// 插入十万条数据
let total = 100000
// 一次插入 20 条
let once = 20
//总页数
let page = total / once
//每条记录的索引
let index = 0
//循环加载数据
function loop(curTotal, curIndex) {
  if (curTotal <= 0) {
    return false
  }
  //每页多少条
  let pageCount = Math.min(curTotal, once)
  window.requestAnimationFrame(function () {
    for (let i = 0; i < pageCount; i++) {
      let li = document.createElement('li')
      li.innerText = curIndex + i + ' : ' + ~~(Math.random() * total)
      ul.appendChild(li)
    }
    loop(curTotal - pageCount, curIndex + pageCount)
  })
}
loop(total, index)
```
## 监控卡顿方法
每秒中计算一次网页的 FPS，获得一列数据，然后分析。通俗地解释就是，通过 requestAnimationFrame API 来定时执行一些 JS 代码，如果浏览器卡顿，无法很好地保证渲染的频率，1s 中 frame 无法达到 60 帧，即可间接地反映浏览器的渲染帧率。
```
var lastTime = performance.now()
var frame = 0
var lastFameTime = performance.now()
var loop = function (time) {
  var now = performance.now()
  var fs = now - lastFameTime
  lastFameTime = now
  var fps = Math.round(1000 / fs)
  frame++
  if (now > 1000 + lastTime) {
    var fps = Math.round((frame * 1000) / (now - lastTime))
    frame = 0
    lastTime = now
  }
  window.requestAnimationFrame(loop)
}
```
我们可以定义一些边界值，比如连续出现 3 个低于 20 的 FPS 即可认为网页存在卡顿。