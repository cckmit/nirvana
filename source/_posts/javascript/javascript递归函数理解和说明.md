---
title: javascript递归函数理解和说明
date: 2021-10-07 16:28:03
categories: 前端
---
递归函数通常在后端用的比较多。对于后端开发人员来说，递归应该是小菜一碟，很简单的事情，但是很多前端确对这个不是很了解。其实，前端中也是经常用递归的，最简单的例子，我之前的一篇博客，讲过[setTimeout()](http://www.haorooms.com/post/js_setTimeout)其中，setTimeout中让一个数没个一秒增加1，不断重复执行，就是一个简单的递归！ **说白了，递归就是函数自己调用自己**。很简单，没有那么可怕！js的另外一个难点就是闭包，闭包和递归，很多前端望而生畏，关于[闭包](http://www.haorooms.com/post/js_bb)
## js递归调用

```
// 一个简单的阶乘函数  
var f = function (x) {  
    if (x === 1) {  
        return 1;  
    } else {  
        return x * f(x - 1);  
    }  
};  
```

Javascript中函数的巨大灵活性，导致在递归时使用函数名遇到困难，对于上面的变量式声明，f是一个变量，所以它的值很容易被替换：

```
var fn = f;  
f = function () {};  
```

函数是个值，它被赋给fn，我们期待使用fn(5)可以计算出一个数值，但是由于函数内部依然引用的是变量f，于是它不能正常工作了。

所以，一旦我们定义了一个递归函数，便须注意不要轻易改变变量的名字。

上面谈论的都是函数式调用，函数还有其它调用方式，比如当作对象方法调用。

我们常常这样声明对象：

```
var obj1 = {  
    num ： 5，  
    fac ： function (x) {  
        // function body  
    }  
};  
```

声明一个匿名函数并把它赋值给对象的属性（fac）。

如果我们想要在这里写一个递归，就要引用属性本身：

```
var obj1 = {  
    num : 5,  
    fac : function (x) {  
        if (x === 1) {  
            return 1;  
        } else {  
            return x * obj1.fac(x - 1);  
        }  
    }  
}; 
```

当然，它也会遭遇和函数调用方式一样的问题：

```
var obj2 = {fac: obj1.fac};  
obj1 = {};  
obj2.fac(5); // Sadness  
```

方法被赋值给obj2的fac属性后，内部依然要引用obj1.fac，于是…失败了。

换一种方式会有所改进：

```
var obj1 = {  
     num : 5,  
     fac : function (x) {  
        if (x === 1) {  
            return 1;  
        } else {  
            return x * this.fac(x - 1);  
        }  
    }  
};  
var obj2 = {fac: obj1.fac};  
obj1 = {};  
obj2.fac(5); // ok  
```

通过this关键字获取函数执行时的context中的属性，这样执行obj2.fac时，函数内部便会引用obj2的fac属性。

可是函数还可以被任意修改context来调用，那就是万能的call和apply：

```
obj3 = {};  
obj1.fac.call(obj3, 5); // dead again  
```

于是递归函数又不能正常工作了。

我们应该试着解决这种问题，还记得前面提到的一种函数声明的方式吗？

```
var a = function b(){};  
```

这种声明方式叫做内联函数（inline function），虽然在函数外没有声明变量b，但是在函数内部，是可以使用b()来调用自己的，于是

```
var fn = function f(x) {  
    // try if you write "var f = 0；" here  
    if (x === 1) {  
        return 1;  
    } else {  
        return x * f(x - 1);  
    }  
};  
var fn2 = fn;  
fn = null;  
fn2(5); // OK  
// here show the difference between "var f = function f() {}" and "function f() {}"  
var f = function f(x) {  
    if (x === 1) {  
        return 1;  
    } else {  
        return x * f(x - 1);  
    }  
};  
var fn2 = f;  
f = null;  
fn2(5); // OK  
var obj1 = {  
    num : 5,  
    fac : function f(x) {  
        if (x === 1) {  
            return 1;  
        } else {  
            return x * f(x - 1);  
        }  
    }  
};  
var obj2 = {fac: obj1.fac};  
obj1 = {};  
obj2.fac(5); // ok  

var obj3 = {};  
obj1.fac.call(obj3, 5); // ok  
```

就这样，我们有了一个可以在内部使用的名字，而不用担心递归函数被赋值给谁以及以何种方式被调用。

Javascript函数内部的arguments对象，有一个callee属性，指向的是函数本身。因此也可以使用arguments.callee在内部调用函数：

```
function f(x) {  
    if (x === 1) {  
        return 1;  
    } else {  
        return x * arguments.callee(x - 1);  
    }  
}
```

但arguments.callee是一个已经准备被弃用的属性，很可能会在未来的ECMAscript版本中消失，在ECMAscript 5中"use strict"时，不能使用arguments.callee。

**最后一个建议是**：如果要声明一个递归函数，请慎用new Function这种方式，Function构造函数创建的函数在每次被调用时，都会重新编译出一个函数，递归调用会引发性能问题——你会发现你的内存很快就被耗光了。

## js递归函数应用

最近在做项目的时候，用到了递归函数，用来调用json的子节点，把所有json中的子节点中包含某个数的object，都push到一个数组中，然后对其进行绑定。

我是通过如下递归调用的

```
var new_array=[];
     function _getChilds(data){
         if(data.ObjType=="某个数"){
            new_array.push(cs_data);
        }
        if(data.Childs){
          if(data.Childs.length>0){
              getChilds(data.Childs)
          }
       }
     }
    function getChilds(data){
        for(var i=0;i<data.length;i++){
            _getChilds(data[i]);
        }
    }

使用方法：getChilds（"json数据"）
```

就把json中所有包含某个数的数据push到new_array数组当中了。
