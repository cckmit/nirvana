---
title: 【this】的指向性问题
date: 2021-10-01 16:28:03
categories: 前端
---
`面试中this指向`可是避不开的点！但是，看到这篇文章只需要记住一句话就能解决所有this指向问题——**`this永远指向它的左邻调用对象！`**

## 一、单层嵌套this指向

```
//浏览器环境
var name = "outer windows";
function a() {
    var name = "inner function";
    console.log(this.name); 
    console.log("inner:" + this);
}
a();
console.log("outer:" + this)
```


输出结果为：


```
outer windows
inner:[object Window]
outer:[object Window]
```


  观察结果，在`函数a`中即使定义了`name`变量，输出`this.name`时显示的是外层定义赋值的`outer windows`，从这里我们不难看出此时`函数a`中的`this`实际上是代表了外层的作用域（该实例中也就是顶层作用域window）；依次在函数内部和外层对`this`输出，指向同一个`对象 window`。
  回过头再看开篇的一句话：`this永远指向它的左邻调用对象！`；该实例代码中`函数a`在哪里调用，它的左邻又是什么对象呢？我们对代码进行简单转化：


```
//浏览器环境
var name = "outer windows";
function a() {
    var name = "inner function";
    console.log(this.name); 
    console.log("inner:" + this);
}
//a()调用变形
this.a(); // 等价于Window.a()
console.log("outer:" + this)
```


注意：**前面没有调用的对象那么可以默认就是全局对象 `this/window`，这就相当于是 `this/window.a()`；**

## 二、多层嵌套this指向

### 1、形式一


```
//浏览器环境
var name = "outer windows";
var a = {
    name: "1 inner function",
    fn : function () {
        var name = "2 inner function"
        console.log(this.name); 
    }
}
a.fn();
```

输出结果为：


```
1 inner function
```


  这个结果和你想象的是不是一样的呢？如果你也是这个答案，那么你对`this永远指向它的左邻调用对象`这句话应该是有了一定理解！同样，我们对上述实例进行解析：最后的函数调用为`a.fun()`变形后等价于`window.a.fn()`，`函数fn`的最左调用对象为`对象a`，所以`函数fn中的this指向其最左的对象a`，故`this.name`为对象`a的name属性值`。

### 2、形式二


```
//浏览器环境
var name = "outer windows";
var a = {
    name: "1 inner function",
    fn : function () {
        var name = "2 inner function"
        console.log(this.name); 
    }
}
var b = a.fn;
b();
```


输出结果为：


```
outer windows
```


  小朋友，你是不是有好多问好？？？相较于`形式一`，该实例中要区分下`变量赋值`和`函数执行`两个概念！分析该实例代码：`var b = a.fn; `实际执行的操作是`声明全局变量b，并对b进行赋值，赋值为a.fn`，也就是说此时b的值实际上是指向


```
function () {
    var name = "2 inner function"
    console.log(this.name); 
}
```


当代码执行到`b()`时才是对上述函数进行了`调用执行`，同样我们对代码进行补充完整：`this/window.b()`，`this永远指向它的左邻调用对象`，此处指向的就是window，其`name`的属性值为`outer windows`；为了验证b的实际值，我们不妨对其进行输出：`console.log(b)`:[图片上传失败...(image-f90233-1633082525149)] 

### 3、形式三


```
//浏览器环境
var name = "outer windows";
function fn() {
    var name = '2 inner function';
    a();
    function a() {
        var name = "2 inner function"
        console.log(this.name); 
    }
}
fn()
```


输出结果为：


```
outer windows
```

  看完形式一和形式二是不是觉得根据`this永远指向它的左邻调用对象`，自己对`this指向`已经了如指掌了！但是看到形式三的例子是不是又有点迷糊！其实一切都离不开`this永远指向它的左邻调用对象`这句话；对上述代码进行分析：`fn() 等价于this/window.fn()`所以此时`fn中的this指代的是window`，在函数fn中又立即调用执行了其作用域中定义的函数a，此时a中的this指向函数fn，前面提到fn的this其实是指向window；所以a中的this其实是指向window的，可以简化为伪代码`window.fn().a()`，符合`this永远指向它的左邻调用对象`！

### 形式四


```
//浏览器环境
var name = "outer windows";
function a() {
       var name = "2 inner function"
       console.log(this.name); 
   }
function fn() {
    var name = '2 inner function';
    a();
}
fn()
```

输出结果为：


```
outer windows
```


对于形式四大家可以自己进行分析下！

## 三、总结

  无论代码中有多少层的且套，我们都可以运用`this永远指向它的左邻调用对象`进行分析得到正确的this指向！最后再重申：`this永远指向它的左邻调用对象 ！this永远指向它的左邻调用对象！this永远指向它的左邻调用对象！`
