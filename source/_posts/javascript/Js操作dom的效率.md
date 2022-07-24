---
title: Js操作dom的效率
date: 2021-03-11 21:12:40
categories: 前端
---

在web开发过程中，前端执行效率的瓶颈往往都是在dom操作上面，dom操作是一件很耗性能的事情，如何才能在dom操作过程中尽量节约性能呢？

**1、减少reflow**

**什么是reflow？**

当 DOM 元素的属性发生变化 (如 color) 时, 浏览器会通知 render 重新描绘相应的元素, 此过程称为 repaint。

如果该次变化涉及元素布局 (如 width), 浏览器则抛弃原有属性, 重新计算并把结果传递给 render 以重新描绘页面元素, 此过程称为 reflow。

**减少reflow的方法**

1.  先将元素从document中删除，完成修改后再把元素放回原来的位置（当对某元素及其子元素进行大量reflow操作时，1,2两种方法效果才会比较明显）
2.  将元素的display设置为”none”，完成修改后再把display修改为原来的值
3.  修改多个样式属性时定义class类代替多次修改style属性（for certain同学推荐）
4.  大量添加元素到页面时使用documentFragment

例如

```
for(var i=0;i<100:i++){
    var child = docuemnt.createElement("li");
    child.innerHtml = "child";
    document.getElementById("parent").appendChild(child);
}
```

上述代码会多次操作dom，效率比较低，可以改为下面的形式 创建documentFragment，将所有元素加入到docuemntFragment不会改变dom结构，最后将其添加到页面，只进行了一次reflow

```
var frag = document.createDocumentFragment();
for(var i=0;i<100:i++){
        var child = docuemnt.createElement("li");
        child.innerHtml = "child";
    frag.appendChild(child);
}
document.getElementById("parent").appendChild(frag);
```

**2、暂存dom状态信息**

当代码中需要多次访问元素的状态信息，在状态不变的情况下我们可以将其暂存到变量中，这样可以避免多次访问dom带来内存的开销，典型的例子就是：

```
ar lis = document.getElementByTagName("li");
for(var i=1;i<lis.length;i++){
    //***
}
上述方式会在每一次循环都去访问dom元素，我们可以简单将代码优化如下
var lis = document.getElementByTagName("li");
for(var i=1,j=lis.length ;i<j;i++){
    //***
}
```

**3、缩小选择器的查找范围**

查找dom元素时尽量避免大面积遍历页面元素，尽量使用精准选择器，或者指定上下文以缩小查找范围，以jquery为例

*   少用模糊匹配的选择器：例如$("[name*='_fix']")，多用诸如id以及逐步缩小范围的复合选择器$("li.active")等
*   指定上下文：例如$("#parent .class")，$(".class",$el)等

**4、使用事件委托**

**使用场景：**一个有大量记录的列表，每条记录都需要绑定点击事件，在鼠标点击后实现某些功能，我们通常的做法是给每条记录都绑定监听事件，这种做法会导致页面会有大量的事件监听器，效率比较低下。

**基本原理**：我们都知道dom规范中事件是会冒泡的，也就是说在不主动阻止事件冒泡的情况下任何一个元素的事件都会按照dom树的结构逐级冒泡至顶端。而event对象中也提供了event.target（IE下是srcElement）指向事件源，因此我们即使在父级元素上监听该事件也可以找到触发该事件的最原始的元素，这就是委托的基本原理。废话不多说，上示例

```
$("ul li").bind("click",function(){
    alert($(this).attr("data"));
})
```

上述写法其实是给所有的li元素都绑定了click事件来监听鼠标点击每一个元素的事件，这样页面上会有大量的事件监听器。

根据上面介绍的监听事件的原理我们来改写一下

```
$("ul").bind("click",function(e){
    if(e.target.nodeName.toLowerCase() ==="li"){
        alert($(e.target).attr("data"));
    }
})
```

这样一来，我们就可以只添加一个事件监听器去捕获所有li上触发的事件，并做出相应的操作。

当然，我们不必每次都做事件源的判断工作，可以将其抽象一下交给工具类来完成。jquery中的delegate()方法就实现了该功能

语法是这样的$(selector).delegate(childSelector,event,data,function)，例如：

```
$("div").delegate("button","click",function(){
  $("p").slideToggle();
});
```

参数说明（引自w3school）

![](https://upload-images.jianshu.io/upload_images/10024246-d8f6aee706ebe272.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "Javascript执行效率小结_")

**Tips:**事件委托还有一个好处就是，即使在事件绑定之后动态添加的元素上触发的事件同样可以监听到哦，这样就不用在每次动态加入元素到页面后都为其绑定事件了

暂时先总结到这。

* * *

原文来自：www.cnblogs.com/gewei/archive/2013/03/29/2988180.html

