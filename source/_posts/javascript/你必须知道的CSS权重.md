---
title: 你必须知道的CSS权重
date: 2021-04-21 14:46:40
categories: 前端
---
# 层叠

我们都知道CSS的全称是`Cascading Style Sheets` 也就是`层叠样式表`

那么当多个选择器同时选中一个元素时 会发生什么呢

这就是CSS需要解决的问题

*   `声明冲突`：同一个样式，多次应用到同一个元素

*   `层叠`：`解决声明冲突的过程`，浏览器自动处理（`权重计算`）

* * *

## 1\. 比较重要性

重要性从高到低：

1.  作者样式表中的 `!important`样式

2.  作者样式表中的普通样式

3.  浏览器默认样式表中的样式

> 作者样式表：开发者书写的样式

* * *

*   我们知道 class 选择器的权重是高于元素选择器的

但当元素选择器的背景样式设置了 `!important` 时， `重要性变得最高`，所以优先显示

```
	<style>
		//元素选择器
        div{
            width : 100px;
            height : 100px;
            background: #008c8c !important; 
        }
        //class选择器
        .box{
            background: red;
        }
    </style>

```

![](https://upload-images.jianshu.io/upload_images/10024246-f18dace6fd391bd2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   浏览器默认样式表就是浏览器赋予元素的默认样式

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/10024246-5c456121502d0f93.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上图右上角的标志就是浏览器的默认样式

![](https://upload-images.jianshu.io/upload_images/10024246-d022fb5fbae9dec9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

浏览器默认样式可以被我们书写的样式覆盖

* * *

## 2\. 比较特殊性

看选择器的`权重`：

*   总体规则：选择器选中的范围越窄，权重越大，越特殊

*   具体规则：通过选择器，计算出一个四位数（x x x x）

    1.千位：如果是内联样式，记1，否则记0 （`内联样式权重 ：1000`）

    2.百位：等于选择器中所有id选择器的数量 （`id选择器权重 ：100`）

    3.十位：等于选择器中所有类选择器、属性选择器、伪类选择器的数量 （`类选择器、属性选择器、伪类选择器的权重 ：10`）

    4.个位：等于元素选择器、伪元素选择器的数量 （ `元素、伪元素的权重 ：1`）

> 计数规则是逢256进一
> 一般情况下 256个class选择器可以干掉一个id选择器

* * *

*   id选择器 > class选择器 显示绿色

```
	.box{
        background: red;
        }
    #box{
        background: green;
    }

```

![](https://upload-images.jianshu.io/upload_images/10024246-150316ad111e2bd9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   class选择器 > 元素选择器 显示红色

```
	div{
        width : 100px;
        height : 100px;
        background: #008c8c; 
        display : inline-block;
    }
    .box{
        background: red;
    }

```

![](https://upload-images.jianshu.io/upload_images/10024246-acf09262530ff7bb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   权重简单计算 ：权重高的优先显示

```
	//计算四位数：元素选择器有一个十位加1为0010 class选择器有一个百位加1为0110
	//简单一点 ：div元素选择器权重为10 class选择器为100 加起来就是110
	div.box{
        background: red;
    }
    //id选择器有一个千位加1为1000
    //简单一点 ：id选择器权重为1000
    #box{
        background: green;
    }

```

![](https://upload-images.jianshu.io/upload_images/10024246-0603442fdf3a2e60.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   剩下的就不一一列举了

* * *

## 3\. 比较源次序

`代码书写靠后的优先`

后面的代码会覆盖前面的代码

* * *

## 比较顺序

*   当重要性一致并且设置的`属性冲突`时 才会去比较特殊性

```
	//当重要性一致时 id选择器的权重大于class选择器
	 .box{
        background: red !important;
     }
     #box{
        background: green !important;
     }

```

![在这里插入图片描述](https://upload-images.jianshu.io/upload_images/10024246-1ae09aac7a39ae82.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   当特殊性一致时 才会去比较源次序

```
	//当两个都是class选择器时 书写靠后的代码优先显示
	.box{
        background: green ;
    }
    .box{
        background: red ;
    }

```

![](https://upload-images.jianshu.io/upload_images/10024246-9a1bf0d0b20de846.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* * *

## 补充

*   由于我们`自己写的代码的重要性高于浏览器默认样式`

所以我们可以在制作页面之前链接一份`重置样式表` 把我们不想要的默认样式给覆盖掉 比如无序列表的小圆点 去除默认的外边距内边距等等

```
	<link rel="stylesheet" href="reset.css">

```

*   CSS存在继承机制 但是`CSS的继承是发生在层叠之后的`

这就是有时候为什么明明样式继承了父元素、权重也没有错却没有显示的问题

原因就是继承的样式被浏览器的默认样式给覆盖了

>本文链接：[https://blog.csdn.net/weixin_45178648/article/details/104897517](https://blog.csdn.net/weixin_45178648/article/details/104897517)

