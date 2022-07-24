---
title: 理解JavaScript内联命名函数
date: 2021-04-21 14:37:02
categories: 前端
---
在使用JavaScript递归调用时，我们往往会在函数内部调用函数自身(通过函数名)，但是当我们改变了定义时所用函数名的指向时，那么这个递归函数指针关联的递归函数也将随之失效。
```
    var factorial = function (num) {
        if (num <= 1) {
            return 1;
        } else {
            return num * factorial(num - 1);
        }
    };
    console.log(factorial(5)); // 120;
    var anothorFactorial = factorial;
    factorial = null;
    console.log(anothorFactorial(5)); // Uncaught TypeError: factorial is not a function
```
在非严格模式下我们可以使用 num * arguments.callee(num - 1)来代替函数名。
严格模式下，通常会采用内联命名函数来解决这个问题。

```
    var factorial = (function f(num) {
        if (num <= 1) {
            return 1;
        } else {
            return num*f(num - 1);
        }
    });
    console.log(factorial(5)); // 120
    var anothorFactorial = factorial;
    factorial = null;
    console.log(anothorFactorial(5)); // 120
```

这种方式的函数调用区别于下面这种方式：

```
     function f(num) {
        if (num <= 1) {
            return 1;
        } else {
            return num*f(num - 1);
        }
    };
    var factorial = f;
    var anothorFactorial = factorial;
    factorial = null;
    console.log(anothorFactorial(5)); // 120
    f = null;
    console.log(anothorFactorial(5)); // Uncaught TypeError: f is not a function
```

给函数表达式的匿名函数命名并不会改变表达式的性质，即不会让它变成一个函数声明

```
    var factorial = function f() {
        console.log(factorial == f); // true
    }

    factorial(); //true
    console.log(typeof f); // undefined
    f(); // Uncaught ReferenceError: f is not defined
```

上述代码证明:尽管我们可以给内联函数命名，但是这些名称只能在自身函数内部才是可见的。使用内联方式调用，其本质和函数表达式没有太大不同，唯一的区别就是内联命名函数在函数内部提供了一个仅仅可供自身调用的函数指针，该指针指向函数自身。