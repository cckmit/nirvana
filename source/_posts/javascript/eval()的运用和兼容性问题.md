---
title: eval()的运用和兼容性问题
date: 2021-03-11 21:12:40
categories: 前端
---

## eval()的运用

eval() 函数可将字符串转换为代码实行，并返回一个或多个值

eval调用时，实例为eval( “( javascript代码 )” )

eval()的返回值

eval()的返回值遵照以下划定规矩：

1.  假如eval()的参数不是字符串，那末eval()将直接返回参数。
2.  假如eval()的参数是字符串，那末eval()将这个字符串剖析成代码后举行实行，并返回末了一行代码实行的效果。
3.  假如字符串没法剖析成正当的代码，eval()将抛出SyntaxError毛病。

举例1（eval的参数不是字符串）：
![](https://upload-images.jianshu.io/upload_images/10024246-dc4d5e8e42bc6faf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
运转效果（谷歌浏览器测试）：
![](https://upload-images.jianshu.io/upload_images/10024246-70af05064badec1d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
举例2（eval的参数是字符串）：
![](https://upload-images.jianshu.io/upload_images/10024246-84320fcb1e2c83b6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
运转效果（谷歌浏览器测试）：
![](https://upload-images.jianshu.io/upload_images/10024246-591d433602ba4377.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
固然，假如不运用eval()要领，上面的代码能够运用匿名函数写
![](https://upload-images.jianshu.io/upload_images/10024246-6d61d72a83601c84.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
举例3（字符串没法剖析成正当的代码）：
[图片上传失败...(image-e61579-1612431403324)] 

运转效果（谷歌浏览器测试）：此时能够看到 谷歌浏览器控制台报错
![](https://upload-images.jianshu.io/upload_images/10024246-70b8a87576a7ba81.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## eval()的兼容性问题

IE6/7/8不兼容

![](https://upload-images.jianshu.io/upload_images/10024246-22edc1da1eb8e7f8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
运用IE8来测试代码：

```
var str = "function(){alert('Test eval')}";
var fn = eval("(" + str + ")");
fn();

```

没有弹出框，控制台报错：
![](https://upload-images.jianshu.io/upload_images/10024246-d1ba5b9f2702b160.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


解决要领：

a)var s = “function(){alert(‘Test!’)}”;

b)var s = “0?0:function(){alert(‘Test!’)}”;
固然这个解决要领是从外洋论坛内里找到(网站：[http://stackoverflow.com/ques…](http://stackoverflow.com/questions/6807649/problems-with-ie9-javascript-eval))
[图片上传失败...(image-ee8f60-1612431403324)] 

粗心是：这在JScript诠释器内里是一个bug，它不会出如今IE9除非你运用混淆形式或兼容来看。IE8毛病将这个函数表达式诠释为函数的声明，使得它没有任何的返回值。所以你能够写成其他比较典范的表达式，从从而在JScript诠释器中组成一个表达式。

那末我们就晓得IE6/7/8运用JScrip诠释器来剖析eval()把参数当初函数声明，没有返回值，所以我么能够把eval()函数内里的字符串代码写成一个表达式，即能够写成：

function(){alert(‘Test!’)} 或 0?0:function(){alert(‘Test!’)}
改代码：

```
var str = "0 ? 0 : function(){alert('Test eval')}";
var fn = eval("(" + str + ")");
fn();

```

在IE8内里测试效果：
![](https://upload-images.jianshu.io/upload_images/10024246-d9debb70a6316c77.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


末了应用ietester东西测试在IE6也一样没有问题。

>原文作者：东雄平
    原文地址: [https://segmentfault.com/a/1190000007394042](https://segmentfault.com/a/1190000007394042)
