---
title: Console新学习
date: 2021-03-11 21:12:40
categories: 前端
---

接着上一篇[前端实用的调试命令console使用](/nirvana/javascript/前端/前端实用的调试命令console使用)

#### 查看所有方法

`console` 除了上面的几个方法还有什么方法呢？`log` 除了能打印字符串外，还能打印出对象，我们可以利用 `console.log` 打印自己。

代码：

```
console.log(console);
```

输出：

```
Object {
    assert: ...,
    clear: ...,
    count: ...,
    debug: ...,
    dir: ...,
    dirxml: ...,
    error: ...,
    group: ...,
    groupCollapsed: ...,
    groupEnd: ...,
    info: ...,
    log: ...,
    markTimeline: ...,
    profile: ...,
    profileEnd: ...,
    table: ...,
    time: ...,
    timeEnd: ...,
    timeStamp: ...,
    timeline: ...,
    timelineEnd: ...,
    trace: ...,
    warn: ...
}
```

上面的 `console` 是 `chrome浏览器`中打印出来的 。
我们可以在 `开发环境` 中，`合理的利用` 这些方法来帮助我们开发。

#### 十一：清理控制台

如果我们在控制台调试的时候。浏览器和命令行 `clear` 一样提供了一个清理函数 `console.clear()` 。

```
console.clear()
```

当然我们也可以用 `chrome` 的 `command line api` 来清理控制台。

```
clear()
```

又或者可以使用按键Mac上 `cmd + k` ，Win `ctrl + l`（我用的是`chrome浏览器`）。

#### 十二：分组并展开

当代码非常长，或者我们需要把一个函数，或者一个文件中的函数等区分出来。我们可以使用分组来实现。
上一篇用group可以分组，但是每次都要手动展开，我们可以使用 `console.groupCollapsed` ，用法和 `console.group` 类似。

代码：

```
console.group('人');
console.log("手");
console.log("脚");
console.groupEnd();

console.group('修仙');
console.log("法力无边");
console.log("腾云架雾");
console.groupEnd();
```

输出：

![](https://upload-images.jianshu.io/upload_images/10024246-88f3c68fa9f3197c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##十三：查看运行堆栈

 `console.trace` 可以看透运行时的结果栈。
代码：

```
function add(num) {
    if (0 < num) {
        console.trace("现在num的值为", num);
        return num + add(num - 1);
    } else {
        return 0;
    }
}

var a =3;
add(3);
```

输出：

![](https://upload-images.jianshu.io/upload_images/10024246-111b03c67baabdb6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#### 十四：统计次数

有时候我们需要统计一个函数或者被调用了几次，我们通常会增加一个变量 `count` 来记录，然后在控制台中查看。这样相当的麻烦，我们可以使用 `console.count` 函数来帮忙我们记录次数，并输出。

```
function hi(name) {
    console.count(name);
    return "hi " + name;
}

for(var i = 0; i < 10; i++) {
    if(i < 4) {
        hi("person");
    } else {
        hi("god");
    }
}
```
结果：
```
person: 1
person: 2
person: 3
person: 4
god: 1
god: 2
god: 3
god: 4
god: 5
god: 6

```
