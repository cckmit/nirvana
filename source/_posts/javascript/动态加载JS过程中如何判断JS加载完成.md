---
title: 动态加载JS过程中如何判断JS加载完成
date: 2021-03-11 21:12:40
categories: 前端
---

在正常的加载过程中，js文件的加载是同步的，也就是说在js加载的过程中，浏览器会阻塞接下来的内容的解析。这时候，动态加载便显得尤为重要了，由于它是异步加载，因此，它可以在后台自动下载，并不会妨碍其它内容的正常解析，由此，便可以提高页面首次加载的速度。

那么，既然js是异步加载，我们又该如何判断它是否加载完成呢？

在IE或一些基于IE内核的浏览器中（如Maxthon）,它是通过script节点的readystatechange方法来判断的，而其它的一些浏览器中，往往是通过load事件来决定的，如下代码：

```
function dynamicLoad() {
    var _doc = document.getElementsByTagName('head')[0];
    var script = document.createElement('script');
    script.setAttribute('type', 'text/javascript');
    script.setAttribute('src', 'jquery-1.4.4.js');
    script.onload = script.onreadystatechange = function () {
        if (!this.readyState || this.readyState == 'loaded' || this.readyState == 'complete') {
            alert('done');
        }
        script.onload = script.onreadystatechange = null;
    }
}
```

我们把该方法添加到body的load事件中，<body onload='dynamicLoad()'>，运行，会发现IE或Maxthon中，会弹出‘done’对话框，但是firefox或chrome中却完全没有反应，why?

这是因为，如果script节点没有添加到DOM树中，那么在chrome和firefox中是不会响应script的load事件的。但是IE中却可以。。修改后如下代码：

```
function dynamicLoad() {
    var _doc = document.getElementsByTagName('head')[0];
    var script = document.createElement('script');
    script.setAttribute('type', 'text/javascript');
    script.setAttribute('src', 'jquery-1.4.4.js');
    _doc.appendChild(script);
    script.onload = script.onreadystatechange = function () {
        if (!this.readyState || this.readyState == 'loaded' || this.readyState == 'complete') {
            alert('done');
        }
        script.onload = script.onreadystatechange = null;
    }
} 
```
这时候，所有的浏览器都可以响应，你可以尝试一下~
