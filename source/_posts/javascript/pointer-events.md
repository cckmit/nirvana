---
title: pointer-events
date: 2021-03-11 21:12:40
categories: 前端
---

pointer-events
语法：
pointer-events：auto| none | visiblepainted | visiblefill | visiblestroke | visible | painted | fill | stroke | all
默认值：auto
适用于：所有元素
继承性：有
动画性：否
计算值：指定值
- 取值：
auto：
与pointer-events属性未指定时的表现效果相同。在svg内容上与visiblepainted值相同
none：
元素永远不会成为鼠标事件的target。但是，当其后代元素的pointer-events属性指定其他值时，鼠标事件可以指向后代元素，在这种情况下，鼠标事件将在捕获或冒泡阶触发父元素的事件侦听器。
其他值只能应用在SVG上。

说明：
设置或检索在何时成为属性事件的target。
使用pointer-events来阻止元素成为鼠标事件目标不一定意味着元素上的事件侦听器永不会触发。如果元素后代明确指定了pointer-events属性并允许其成为鼠标事件的目标，那么指向该元素的任何事件在事件传播过程中都将通过父元素，并以适当的方式触发其上的事件侦听器。当然位于屏幕上在父元素上但不在后代元素上的鼠标活动都不会被父元素和后代元素捕获（将会穿过父元素而指向位于其下面的元素）。
对应的脚本特性为pointerEvents。
兼容性：
浅绿 = 支持

红色 = 不支持

粉色 = 部分支持
![](https://upload-images.jianshu.io/upload_images/10024246-2b69550f256cac89.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
<!DOCTYPE html>
<html lang="zh-cmn-Hans">
<head>
<meta charset="utf-8" />
<title></title>

<style>
a[href="http://example.com"] {
	pointer-events: none;
}
</style>
</head>
<body>
<ul>
	<li><a href="https://html.cn/">HTML中文网</a></li>
	<li><a href="http://example.com">一个不能点击的链接</a></li>
</ul>
</body>
</html>
```
