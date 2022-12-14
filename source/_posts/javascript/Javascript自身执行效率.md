---
title: Javascript自身执行效率
date: 2021-03-11 21:12:40
categories: 前端
---

Javascript中的作用域链、闭包、原型继承、eval等特性，在提供各种神奇功能的同时也带来了各种效率问题，用之不慎就会导致执行效率低下。

## 1、全局导入

我们在编码过程中多多少少会使用到一些全局变量（window,document,自定义全局变量等等），了解javascript作用域链的人都知道，在局部作用域中访问全局变量需要一层一层遍历整个作用域链直至顶级作用域，而局部变量的访问效率则会更快更高，因此在局部作用域中高频率使用一些全局对象时可以将其导入到局部作用域中，例如：
```
 //1、作为参数传入模块
  (function(window,$){
      var xxx = window.xxx;
      $("#xxx1").xxx();
      $("#xxx2").xxx();
  })(window,jQuery);
  
  //2、暂存到局部变量
  function(){
     var doc = document;
     var global = window.global;
}
```
## 2、eval以及类eval问题

我们都知道eval可以将一段字符串当做js代码来执行处理，据说使用eval执行的代码比不使用eval的代码慢100倍以上（具体效率我没有测试，有兴趣同学可以测试一下）

JavaScript 代码在执行前会进行类似“预编译”的操作：首先会创建一个当前执行环境下的活动对象，并将那些用 var 申明的变量设置为活动对象的属性，但是此时这些变量的赋值都是 undefined，并将那些以 function 定义的函数也添加为活动对象的属性，而且它们的值正是函数的定义。但是，如果你使用了“eval”，则“eval”中的代码（实际上为字符串）无法预先识别其上下文，无法被提前解析和优化，即无法进行预编译的操作。所以，其性能也会大幅度降低

其实现在大家一般都很少会用eval了，这里我想说的是两个类eval的场景
```
(new Function{},setTimeout,setInterver)

setTimtout("alert(1)",1000);

setInterver("alert(1)",1000);

(new Function("alert(1)"))();
```
上述几种类型代码执行效率都会比较低，因此建议直接传入匿名方法、或者方法的引用给setTimeout方法

## 3、闭包结束后释放掉不再被引用的变量
```
var f = (function(){
    var a = {name:"var3"};
    var b = ["var1","var2"];
    var c = document.getElementByTagName("li");
    //****其它变量
    //***一些运算
    var res = function(){
        alert(a.name);
    }
    return res;
})()
```
上述代码中变量f的返回值是由一个立即执行函数构成的闭包中返回的方法res，该变量保留了对于这个闭包中所有变量（a,b,c等）的引用，因此这两个变量会一直驻留在内存空间中,尤其是对于dom元素的引用对内存的消耗会很大，而我们在res中只使用到了a变量的值，因此，在闭包返回前我们可以将其它变量释放
```
var f = (function(){
    var a = {name:"var3"};
    var b = ["var1","var2"];
    var c = document.getElementByTagName("li");
    //****其它变量
    //***一些运算
    //闭包返回前释放掉不再使用的变量
    b = c = null;
    var res = function(){
        alert(a.name);
        }
    return res;
})()
```
