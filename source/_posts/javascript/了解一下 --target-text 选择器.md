---
title: 了解一下 ::target-text 选择器
date: 2022-05-05 16:28:03
categories: 前端
---
最近在 MDN 官网看到了一个从未见过的选择器，[::target-text](https://developer.mozilla.org/en-US/docs/Web/CSS/::target-text)。

![image.png](https://upload-images.jianshu.io/upload_images/10024246-82c65446ca36bd8b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

简单研究了一下，觉得还有点意思，也有点实际用处，现在分享一下。

## 一、::target-text 是干什么的

想必大家都用过`:target`这个选择器，可以很方便的从URL中匹配到页面上的内容，并且实现锚定定位。比如文档目录上经常看到这样的

![](https://upload-images.jianshu.io/upload_images/10024246-8db75859c4f574ab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

但是，`:target`必须要求页面中包含`id`为该目标的元素，如果不存在就没办法定位了。为了解决这个问题，于是，`::target-text`就出现了！

从字面意思上来看，**::target-text** 表示"锚定文本"选择器。官方MDN上的描述为：

> 如果浏览器支持**滚动到文本片段**这个特性，则会滚动到这部分文本所在的地方，并且允许用户自定义高亮显示该部分文本样式。

什么意思呢，这里官方有一个例子 [scroll-to-text demo](https://mdn.github.io/css-examples/target-text/index.html#:~:text=From%20the%20foregoing%20remarks%20we%20may%20gather%20an%20idea%20of%20the%20importance)

![](https://upload-images.jianshu.io/upload_images/10024246-494cee9832145c98.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到点击这个链接后，浏览器自动跳转到指定的文本片段，并且该文本会有高亮的样式（图中的紫色背景，白色文字）。

于是，`::target-text`可以用来自定义这部分的样式

```
::target-text {
  background-color: rebeccapurple;
  color: white;
}
```

不过，支持的样式比较有限，和`::selection`差不多，仅支持文本相关样式

## 二、如何指定跳转位置

我们都知道，`:target`是通过在URL上指定`#`加 id 来匹配的，如下

```
http://www.example.com/index.html#section2

<section id="section2">Example</section>
```
回到刚才那个例子，可以看到跳转链接是这样的

![](https://upload-images.jianshu.io/upload_images/10024246-211e7dd1af835d3d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
可以看到，`::target-text `也是有对应的规则的，如下

```
http://www.example.com/index.html#:~:text=textStart
```

这里的`textStart`就是表示页面中需要跳转的文本内容。不过需要注意的是，如果有多段文本都能够匹配，那么会定位到第一个相匹配的文本（和 id 有点类似）。

## 三、如何精准的定位

单纯的指定一小段文本，很容易出现定位不准的情况（太容易重复了）。为了解决这个问题，有两个方案

1.  尽量指定长的文本，这样就不会重复了
2.  在文本前后加上限制，比如起始点字符

方案一虽然可行，但是也有问题，一是地址栏太长，不太美观，而是我只需要分享这一小段内容出去，不需要那么多。现在看下方案二。这里简单介绍下`:~:text`的完整语法

```#:~:text=[prefix-,]textStart[,textEnd][,-suffix]```

*   prefix- 前缀文本
*   textStart 文本开始
*   textEnd 文本结束
*   -suffix 后缀文本

从语法上，只有 textStart 是必填，其他都是可选。怎么用的呢？假设我们想定位这一段文本（不包含首尾标点）

![](https://upload-images.jianshu.io/upload_images/10024246-ac747adb5822970a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以直接指定起始字符，`Mlle,parachute`

```#:~:text=Mlle,parachute```

> 可以访问这个链接 [https://mdn.github.io/css-examples/target-text/index.html#:~:text=Mlle,parachute](https://mdn.github.io/css-examples/target-text/index.html#:~:text=Mlle,parachute)

效果如下

![](https://upload-images.jianshu.io/upload_images/10024246-3e55d98972c843b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到定位区域在第一个`parachute`处就结束了，并没有定位到后面的。这时可以继续限制一下，比如把后面的`.`加进来，作为后缀

```#:~:text=Mlle,parachute,-.```

> 可以访问这个链接 [https://mdn.github.io/css-examples/target-text/index.html#:~:text=Mlle,parachute,-.](https://mdn.github.io/css-examples/target-text/index.html#:~:text=Mlle,parachute,-.)

效果如下

![](https://upload-images.jianshu.io/upload_images/10024246-82e36f25f503c6d6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这样就能精准的定位到想要的内容了

## 四、浏览器行为和兼容性

虽然有上面的语法，但实际上浏览器已经内置了快捷操作。选中一段文本，右键会出现这样的菜单，有一个“复制指向突出显示的内容的链接”选项（Edge浏览器提示略有不同），如下

![](https://upload-images.jianshu.io/upload_images/10024246-68eb3c883d4108df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击这个会自动复制一段包含`#:~:text=`的链接，浏览器会自动处理选中文本的前后限制，保证结果的唯一性。如下，将刚才复制的地址直接粘贴在浏览器打开

![](https://upload-images.jianshu.io/upload_images/10024246-23cbcadfafdcc616.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


然后说一下兼容性。

这个属性非常新，可以在 MDN 官网看到具体的兼容信息，需要 Chrome 89+ 以上才行

![](https://upload-images.jianshu.io/upload_images/10024246-71b4d22312aec983.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


试了一下安卓系统上也是没有问题的，比如在微信中打开的效果如下
![](https://upload-images.jianshu.io/upload_images/10024246-73da4e527b01dc7f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

默认是一个黄色背景（貌似还不能自定义颜色），点击任意地方就消失了。

比较适合在阅读一本书时，想分享某一章节的某一小段精彩文本给他人，这样就能快速定位到分享的地方了，还能高亮显示。是不是很方便呢？

## 五、简单总结一下

详细通过本文，应该可以了解到`::target-text`是什么了吧？下面简单总结一下

1.  ::target-text 和 :target 类似，都可以跳转到指定位置
2.  ::target-text 无需 id，可以指定任意文本
3.  地址栏匹配规则是 #:~:text=[prefix-,] textStart [,textEnd] [,-suffix]，只有 textStart 是必填，其他都是可选
4.  浏览器支持“复制指向突出显示的内容的链接”操作，可以不必手动拼接
5.  兼容性有点差，安卓用户可以使用

当然这本身是一个渐进增强的属性，能够支持体验更好，不支持也没什么大事。
