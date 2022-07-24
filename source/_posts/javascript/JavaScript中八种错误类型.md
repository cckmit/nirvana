---
title: JavaScript中八种错误类型
date: 2022-02-18 16:28:03
categories: 前端
---
>ECMS中定义了以下八种错误类型,并在错误发生时抛出不同的错误对象。
Error
InternalError
EvalError
RangeError
ReferenceError
SyntaxError
TypeError
URIError

## Error
`Error`是最基本的错误类型，其他的错误类型都继承自该类型。因此，所有错误的类型共享了一组相同的属性。 这个类型的错误很少见。一般使用开发人员自定义抛出的错误。
```
new Error([message[,fileName[,lineNumber]]])，
//第一个参数表示错误提示信息，第二个是文件名，第三个是行号。
```
![](https://upload-images.jianshu.io/upload_images/10024246-20c28a74c1b5b06f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## InternalError
`InternalError`类型的错误会在底层JavaScript引擎抛出异常时由浏览器抛出.例如,递归过多导致了栈溢出.这类型并不是代码中通常要处理的错误,如果真的发生了这种错误,很可能代码哪里弄错了或者有危险.
```
 "InternalError: too much recursion"//（内部错误：递归过深）。
```
## EvalError
`EvalError`类型错误会在使用`eval()`函数发生异常时抛出evalError错误.`ECMA-262`规定,'如果eval属性没有被直接调用(就是没有将其名称作为一个Identifier(标识符),也就是`CallExpression`中的`MemberExpression`).
基本上,只要不把`eval()`当成函数调用就会报错.
不同浏览器抛出的错误会有差异,但很少会这么使用,所以平时不常见
![](https://upload-images.jianshu.io/upload_images/10024246-69b47d7ffa9871e2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>`需要注意的是：ES5以上的JavaScript中已经不再抛出该错误，但依然可以通过new关键字来自定义该类型的错误提示。
## RangeError
这个错误会在数值超出相应范围时触发。比如使用new Array()的时候传递一个负数或者是超过数组最大长度（4,294,967,295）的数，比如Number.MAX_VALUE，Number.MIN_VALUE。注意递归爆炸也有这个错误。
![](https://upload-images.jianshu.io/upload_images/10024246-9993bf53bcaa6b9f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## ReferenceError
这个错误一般就是出现在变量找不到的情况，比如：
![](https://upload-images.jianshu.io/upload_images/10024246-6df0a918531ca685.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## SyntaxError
SyntaxError 主要在语法编写出现问题时发生。
```
// 1. Syntax Error: 语法错误
// 1.1 变量名不符合规范
var 1       // Uncaught SyntaxError: Unexpected number
var 1a       // Uncaught SyntaxError: Invalid or unexpected token
// 1.2 给关键字赋值
function = 5     // Uncaught SyntaxError: Unexpected token =
```
## TypeError
这个错误在JavaScript中是经常遇到的，不管是初学者还是老手。在变量中保存着以外的类型时，或者在访问不存在的方法时。都会导致这种错误。但是归根结底还是由于在执行特定于类型的操作时，变量的类型并不符合要求所致。
>在给函数传参前没有验证的情况下,错误频繁发生.

![](https://upload-images.jianshu.io/upload_images/10024246-d231b1e702b08e27.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## URIError
`URIError`只会在使用`encodeURL()`或`decodeURL()`但传入了格式错误的URL时发生,但非常罕见,因为上面两个函数非常稳健.