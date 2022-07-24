---
title: fastclick解析与ios11 3相关bug原因分析
date: 2021-03-11 21:12:40
categories: 前端
---

最近发现升级到ios11.3之后，输入框点击变得不灵敏，第二次点击页面中的输入框需要长按一会才能正常唤起键盘输入。排查后，怀疑是fastclick出现了问题，上github看了issues，果不其然很多人也出现相同问题（[https://github.com/ftlabs/fas...](https://github.com/ftlabs/fastclick/issues/548) ）。按照issues上的解决方法，也顺利地解决了问题，不过，究竟为何会出现这么奇怪的bug？我们还需要继续深入寻找答案。

## 首先，fastclick究竟用来干嘛？

简而言之，它是用来**解决300ms延迟**和**点击穿透**这两个问题。
在移动设备上点击按钮后，浏览器将会等待300ms，继续监听点击动作来判断是否为双击事件，这就是300ms延迟问题。
为了解决这300ms的延迟问题，一种解决方案是将touch系列事件绑定在document上，通过计算touch事件触发的时间位置等来判断是否为移动设备的点击，如zepto.js中自定义的tap事件；另一种方案，也是fastclick中的实现方案，当检测到touchend事件的时候，会通过DOM自定义事件立即出发模拟一个click事件，并用preventDefault阻止300ms之后真正的click事件。

那么什么是点击穿透问题？
点击穿透问题是当两个元素重叠在同一个位置，上层元素绑定touch事件，下层元素绑定click事件，当上层元素触发touch事件后，可能会触发下层div的click事件。

## 其次，fastclick都做了哪些工作？

fastclick的主要工作可见参考文献[2]中的图，如下：
![](https://upload-images.jianshu.io/upload_images/10024246-1599ce6f6c2a7831.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


fastclick的主要工作是在body或者顶层元素中绑定touch相关事件，在touch相关事件中标记手势的位置与时间，根据此信息拦截click事件并判断是否模拟触发。

在处理300ms延迟的过程中，主要工作是模拟并拦截真正的click事件。
首先，拦截点击事件的思路是将元素的onclick事件置为空，并用addEventListener重新绑定，理由是onclick将会在fastclick模拟的点击事件之前触发，在构造函数中关键代码如下：

```
function FastClick(layer, options) {
    ...

    // If a handler is already declared in the element's onclick attribute, it will be fired before
    // FastClick's onClick handler. Fix this by pulling out the user-defined handler function and
    // adding it as listener.
    if (typeof layer.onclick === 'function') {
        oldOnClick = layer.onclick;
        layer.addEventListener('click', function(event) {
            oldOnClick(event);
        }, false);
        layer.onclick = null;
    }    
}
```

接着，看看fastclick如何判断用户的点击事件是真正的点击，在onTouchEnd事件中，判断的关键代码如下：

```
// event.timeStamp为touchend事件的事件，lastClickTime是上一次touchend事件的事件，此处判断是否为双击操作
if ((event.timeStamp - this.lastClickTime) < this.tapDelay) {
    this.cancelNextClick = true;
    return true;
}

// trackingClickStart是touchstart事件的事件，此处判断是否为长按操作
if ((event.timeStamp - this.trackingClickStart) > this.tapTimeout) {
    return true;
}
```

如果此次点击是真正的点击事件，有两种情况要触发模拟的click事件：一种是由needsFocus函数判断是否为可以focus的元素，如<input type="text">、textarea等；另一种是由needsClick函数判断是否为需要原生点击的原生，不需要原生点击的也需要模拟click事件，这部分的代码逻辑比较简单主要根据判断元素的tagName和class来判断，这里就不贴代码了。

需要触发模拟click事件的情况中，第一种情况（如输入框等）是需要触发focus事件的，触发之后再触发click事件，而第二种（如按钮等）则单纯触发click事件即可。接下来，我们先分析focus事件的响应函数，再看模拟的click事件。
focus主要工作一方面在为了将光标移到移到输入框尾部，另一方面触发元素的focus事件，其响应函数为：

```
FastClick.prototype.focus = function(targetElement) {
    var length;

    if (deviceIsIOS && targetElement.setSelectionRange && targetElement.type.indexOf('date') !== 0 && targetElement.type !== 'time' && targetElement.type !== 'month' && targetElement.type !== 'email') {
        // 通过 targetElement.setSelectionRange(length, length) 将光标的位置定位在内容的尾部（但注意，这时候还没触发focus事件）
        length = targetElement.value.length;
        targetElement.setSelectionRange(length, length);
    } else {
        targetElement.focus();
    }
};
```

模拟的click事件，本质就是用代码创建一个Event作为点击事件触发，关键代码如下：

```
FastClick.prototype.sendClick = function(targetElement, event) {
    ...
    // Synthesise a click event, with an extra attribute so it can be tracked
    clickEvent = document.createEvent('MouseEvents');
    clickEvent.initMouseEvent(this.determineEventType(targetElement), true, true, window, 1, touch.screenX, touch.screenY, touch.clientX, touch.clientY, false, false, false, false, 0, null);
    clickEvent.forwardedTouchEvent = true;
    targetElement.dispatchEvent(clickEvent);
};
```

最后，fastclick使用了preventDefault和stopImmediatePropagation拦截原生的click响应函数。preventDefault函数很常见了，但stopImmediatePropagation真是头一次见它。根据规范可知，该方法不仅可以阻止冒泡，还能将元素绑定的后序相同类型事件的监听函数的执行也一起阻止了，也就是说如果在点击事件中调用了它，可以阻止点击事件冒泡传递到父级元素，同时又能阻止该元素上的其他点击响应函数。

## 最后，如何修复？

Issues中给出的修复方法是强制元素focus，即在改写的focus响应函数中直接触发元素的focus事件：

```
FastClick.prototype.focus = function(targetElement) {
    targetElement.focus();
};
```

推测原因是由于ios11.3取消了input元素setSelectionRange自动聚焦的功能（非此原因 (⊙﹏⊙)）

> (6.22更新) 对比了一下ios11.3与之前的fastclick相关运行过程，只有320行左右有区别，“document.activeElement.blur();”，ios11.3之后在第二次点击时有经过，而ios11.3之前的没有。另外，350行左右的“targetElement.setSelectionRange(length, length);”，是引起输入框聚焦的原因，但仅仅执行这个函数还无法到达聚焦的效果，fastclick还做了哪些相关工作，仍未知。

此外，ios11.3支持了Web API：允许对事件支持被动模式，减少滚动屏幕的性能损耗和奔溃，并且针对document的touch事件监听添加被动模式的配置，因此document将不再调用preventDefault方法。这些改动会引起fastclick的另一个bug，当静置app或锁屏几秒后页面将无法响应任何点击操作。
解决方法也很简单，只需去除被动模式，如下：

```
// 支持设置passive的，将被动模式显式设置为false
layer.addEventListener('touchstart', this.onTouchStart, {passive:false});
// 否则，去除默认的被动模式
layer.addEventListener('touchstart', this.onTouchStart, false);
```
