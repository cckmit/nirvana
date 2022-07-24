---
title: 如何判断一个APP页面是原生的还是H5页面
date: 2022-05-03 16:28:03
categories: 前端
---
>作者：25学堂
原文链接：http://www.25xt.com/appdesign/11851.html

如今最火的APP开发模式是Hybrid  APP开发（即混合模式，半原生半H5页面）。
- 原生是Native APP
- H5就是Web App
不仔细去观察，一般人都不会察觉出来的，再加上现在的H5技术和原生应用的技术很多类似，或者说实现的效果很相像。
在Hybrid当中，如何快速的判断一个APP页面是原生的还是H5页面呢？综合网友的答案汇总整理了一下。如果你们还有更好的判断方法也可以告知我。

## 一、看断网的情况

把手机的网络断掉。然后点开页面。然后可以正常显示的东西就是原生写的。

显示404或者错误页面的是html页面。

## 二、看布局边界

可以打开  开发者选项中的显示布局边界，页面元素很多的情况下布局是一整块的是h5的，布局密密麻麻的是原生控件。页面有布局的是原生的否则为h5页面。（仅针对安卓手机适用）如下图所示：

![](https://upload-images.jianshu.io/upload_images/10024246-74f57d94aed72e4a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 三、看复制文章的提示

这个需要你通过对比才能得出结果，比如是文章资讯页面可以长按页面试试，如果出现文字选择、粘贴功能的是H5页面，否则是native原生的页面。

有些原生APP开放了复制粘贴功能或者关闭了。而H5的css屏蔽了复制选择功能等等情况。需要通过对目标测试APP进行对比才可知。

这个在支付宝APP、蚂蚁聚宝都是可以判断的。

## 四、看加载的方式

如果在打开新页面导航栏下面有一条加载的线的话，这个页面就是H5页面，如果没有就是原生的。微信里面打开我们的H5页面常见的有个绿色的 加载线条。如下图红框里面所示：

![](https://upload-images.jianshu.io/upload_images/10024246-304cb6160651ddd0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 五、下拉页面的时候显示网址提供方的一定是H5

如下图所示：

![](https://upload-images.jianshu.io/upload_images/10024246-a75e92829d635e72.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

希望可以帮到大家，以便更加容易区分原生APP页面和H5页面。
