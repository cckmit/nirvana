---
title: JavaScript面试题
date: 2021-03-11 21:12:40
categories: 前端
---

## 作用域

```
 (function(){ 
        var a = b =5;
    })();
console.log(b);
```

**答案：**`5`

>这个问题考查的要点是两个不同的作用域，'a'被var声明成了一个局部变量，但是'b'实际上没有被定义，所以它是一个全局变量。
这个问题还牵扯到另个一个比较重要的问题，就是strict mode，如果你选择了strict mode，上面的代码就会报Uncaught ReferenceError，因为b没有被定义，它可以帮你检查出代码的一些问题：

```
 (function(){ 
      'use strict'; 
      var a = window.b =5;
    })();
console.log(b);
```
##  创建“native”方法

写一个重复打印字符串对象的方法，输入一个整数，这个整数代表重复打印的字数，比如：

 console.log('hello'.repeatify(3));

这样会打印出hellohellohello。

### 答案：
```
String.prototype.repeatify =String.prototype.repeatify ||function(times){
    var str ='';
    for(var i =0; i < times; i++){
    str +=this;
    }
    return str;
    };
```
这个问题考查的是开发者对JavaScript继承和prototype属性的了解程度。

##  Hoisting

下面这段代码的输出结果是什么？
```
 function test(){
    console.log(a);
    console.log(foo()); var a =1; function foo(){
    return2;
    }
    }

    test();
```
**答案:**`undefined和2`

上面的代码和下面这段是等效的：

```
function test(){ var a; function foo(){
    return2;
    }
    console.log(a);
    console.log(foo());
    a =1;
    }
    test();
```
##  在JavaScript中如何执行

通过下面的代码给出结果，解释一下你的答案：
```
var obj ={
    fullname:'Colin Ihrig',
    prop:{
          fullname:'Aurelio De Rosa',
          getFullname:function(){
                  return this.fullname;
           }
    }
};
console.log(obj.prop.getFullname()); var test = obj.prop.getFullname;
console.log(test());
```

**答案：**`Aurelio De Rosa 和John Doe`

##  call() 和 apply()

解决前面的问题之后让最后一个console.log()输出Aurelio De Rosa.

**答案：**
```
console.log(test.call(obj.prop));
```
>这个问题在于call()还是apply()。 
