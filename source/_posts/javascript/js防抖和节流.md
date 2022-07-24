---
title: js防抖和节流
date: 2021-03-11 21:12:40
categories: 前端
---

防抖和节流是针对响应跟不上触发频率这类问题的两种解决方案。应用场景有很多，输入框持续输入，将输入内容远程校验、多次触发点击事件、onScroll等等。 处理不当或者放任不管就容易引起浏览器卡死。
针对快速连续触发和不可控的高频触发问题， 给出debounce 和 throttling了两种解决策略；
## 防抖(debounce)---
策略是当事件被触发时，设定一个周期延迟执行动作，若期间又被触发，则重新设定周期，直到周期结束，执行动作。 这是debounce的基本思想，在后期又扩展了前缘debounce，即执行动作在前，然后设定周期，周期内有事件被触发，不执行动作，且周期重新设定。

`延迟debounce`

![延迟debounce](https://upload-images.jianshu.io/upload_images/10024246-e3337fe492ae59fc?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`前缘debounce`

![前缘debounce](https://upload-images.jianshu.io/upload_images/10024246-759614d16bd50daa?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

debounce的特点是当事件快速连续不断触发时，动作只会执行一次。 延迟debounce，是在周期结束时执行，前缘debounce，是在周期开始时执行。但当触发有间断，且间断大于我们设定的时间间隔时，动作就会有多次执行。

**效果：**如果短时间内大量触发同一事件，只会执行一次函数。

**实现：**既然前面都提到了计时，那实现的关键就在于setTimeout这个函数，由于还需要一个变量来保存计时，考虑维护全局纯净，可以借助闭包来实现：
```
/*
* fn [function] 需要防抖的函数
* delay [number] 毫秒，防抖期限值
*/
function debounce(fn,delay){
    let timer = null //借助闭包
    return function() {
        if(timer){
            clearTimeout(timer) //进入该分支语句，说明当前正在一个计时过程中，并且又触发了相同事件。所以要取消当前的计时，重新开始计时
            timer = setTimeout(fn,delay) 
        }else{
            timer = setTimeout(fn,delay) // 进入该分支说明当前并没有在计时，那么就开始一个计时
        }
    }
}
```
稍微简化下：
```
/*****************************简化后的分割线 ******************************/
function debounce(fn,delay){
    let timer = null //借助闭包
    return function() {
        if(timer){
            clearTimeout(timer) 
        }
        timer = setTimeout(fn,delay) // 简化写法
    }
}
// 然后是旧代码
function showTop  () {
    var scrollTop = document.body.scrollTop || document.documentElement.scrollTop;
　　console.log('滚动条位置：' + scrollTop);
}
window.onscroll = debounce(showTop,1000) // 为了方便观察效果我们取个大点的间断值，实际使用根据需要来配置
````
此时会发现，必须在停止滚动1秒以后，才会打印出滚动条位置。

## 节流(throttle)---
策略是，让函数执行一次后，在某个时间段内暂时失效，过了这段时间后再重新激活（类似于技能冷却时间）。 节流策略也分前缘和延迟两种。与debounce类似，延迟是指 周期结束后执行动作，前缘是指执行动作后再开始周期。

`延迟throttling`：

![延迟throttling](https://upload-images.jianshu.io/upload_images/10024246-170a946907e707c2?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

前缘throttling ：

![前缘throttling ](https://upload-images.jianshu.io/upload_images/10024246-1f3724b815810fcf?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

throttling的特点在连续高频触发事件时，动作会被定期执行，响应平滑。
**效果：**如果短时间内大量触发同一事件，那么在函数执行一次之后，该函数在指定的时间期限内不再工作，直至过了这段时间才重新生效。

**实现：** 这里借助setTimeout来做一个简单的实现，加上一个状态位valid来表示当前函数是否处于工作状态。
```
function throttle(fn,delay){
    let run = true
    return function () {
      if (!run) {
        return  // 如果开关关闭了，那就直接不执行下边的代码
      }
      run = false // 持续触发的话，run一直是false，就会停在上边的判断那里
      setTimeout(() => {
        func.apply(this, arguments)
        run = true // 定时器到时间之后，会把开关打开，我们的函数就会被执行
      }, delay)
    }
}
/* 请注意，节流函数并不止上面这种实现方案,
   例如可以完全不借助setTimeout，可以把状态位换成时间戳，然后利用时间戳差值是否大于指定间隔时间来做判定。
   也可以直接将setTimeout的返回的标记当做判断条件-判断当前定时器是否存在，如果存在表示还在冷却，并且在执行fn之后消除定时器表示激活，原理都一样
    */
// 以下照旧
function showTop  () {
    var scrollTop = document.body.scrollTop || document.documentElement.scrollTop;
　　console.log('滚动条位置：' + scrollTop);
}
window.onscroll = throttle(showTop,1000) 
```
## 总结
防抖和节流巧妙地用了setTimeout，来控制函数执行的时机，优点很明显，可以节约性能，不至于多次触发复杂的业务逻辑而造成页面卡顿。复杂情况可参考underscope.js的[throttle](https://underscorejs.net/#throttle)和[debounce](https://underscorejs.net/#debounce)
