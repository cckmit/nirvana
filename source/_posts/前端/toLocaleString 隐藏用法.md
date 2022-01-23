---
title: toLocaleString 隐藏用法
date: 2022-01-21 16:28:03
category: javascript
---
toLocaleString 有很多种用法其中有不少用法可以满足一些常见得业务需求。
哪些类中包含这个API
```
Array.prototype.toLocaleString([locales[,options]])  
Number.prototype.toLocaleString([locales[,options]])
Date.prototype.toLocaleString([locales[,options]]) 
```
## Date对象

##### 语法
```dateObj.toLocaleString([locales [, options]])```
##### 参数

**locales**：可选，带有BCP 47语言标记的字符串或字符串数组，用来表示要转为目标语言的类型，具体参考这个 [Intl](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Intl)
**options**：可选，配置属性对象。
- dateStyle：可能的值为full、long、medium、short。
- timeStyle：可能的值为full、long、medium、short。
- month：可能的值为numeric、2-digit、long、short、narrow。
- year：可能的值为numeric、2-digit。
- weekday：可能的值为long、short、narrow。
- day、hour、minute、second：可能的值为numeric、2-digit。
- timeZone：可能的值为 IANA 的时区数据库。
- timeZooneName：可能的值为long、short。
- hour12：24小时周期还是12小时周期，可能的值为true、false
这些options可以组合显示需要的内容
```
var date = new Date(Date.UTC(2012, 11, 20, 3, 0, 0));

//请求参数(options)中包含参数星期(weekday)，并且该参数的值为长类型(long)
var options = {weekday: "long", year: "numeric", month: "long", day: "numeric"};
alert(date.toLocaleString("de-DE", options));
// → "Donnerstag, 20. Dezember 2012"

//一个应用使用 世界标准时间(UTC),并且UTC使用短名字(short)展示
options.timeZone = "UTC";
options.timeZoneName = "short";//若不写这一行那么仍然显示的是世界标准时间；但是GMT三个字母不会显示
alert(date.toLocaleString("en-US", options));
// → "Thursday, December 20, 2012, GMT"

// 使用24小时制
alert(date.toLocaleString("en-US", {hour12: false}));
// → "12/19/2012, 19:00:00"
```

## NUM对象
##### 参数

**locales**：可选，带有BCP 47语言标记的字符串或字符串数组，用来表示要转为目标语言的类型，具体参考这个 [Intl](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Intl)
**options**：可选，配置属性对象。

*   **style**：数字展示样式

    | style字段值 | 说明 |
    | --- | --- |
    | **decimal** | 用于纯数字格式（默认） |
    | **currency** | 用于货币格式 |
    | **percent** | 用于百分比格式 |
    | **unit** | 用于单位格式 |

*   **currency**：当 **options.style为currency** 时，**options.currency** 用来表示货币单位的类型

    | currency字段值 | 说明 |
    | --- | --- |
    | **USD** | 使用美元格式（默认） |
    | **EUR** | 使用欧元格式 |
    | **CNY** | 使用人民币格式 |

# 示例

```
 // 1、将数字进行千分位切割展示
var num = 1331231  
console.log(num.toLocaleString())  //  1,331,231

 // 2、将数字转为货币样式
var number = 123456.789;  
console.log(number.toLocaleString('zh', { style: 'currency', currency: 'EUR' }));    //€123,456.79   
console.log(number.toLocaleString('zh', { style: 'currency', currency: 'CNY' }));    //¥123,456.79   
console.log(number.toLocaleString('zh', { style: 'currency', currency: 'CNY',currencyDisplay:'code' }));    //CNY 123,456.79
console.log(number.toLocaleString('zh', { style: 'currency', currency: 'CNY',currencyDisplay:'name' }));    //123,456.79人民币

// 3、数字转百分比并四舍五入取整
var num1 = 0.12 
var num2 = 2.45
var num3 = 2.12345
console.log(num1.toLocaleString('zh',{style:'percent'}))  //  12%
console.log(num2.toLocaleString('zh',{style:'percent'}))  //  245%  
console.log(num3.toLocaleString('zh',{style:'percent'}))  //  212%  
```
## Array对象
数组中的元素将使用各自的 toLocaleString 方法转成字符串，这些字符串将使用一个特定语言环境的字符串（例如一个逗号 ","）隔开。
##### 参数
**locales**：可选，带有BCP 47语言标记的字符串或字符串数组，用来表示要转为目标语言的类型，具体参考这个 [Intl](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Intl)
`options` 可选
一个可配置属性的对象，
- 对于数字 `Number.prototype.toLocaleString()`，
- 对于日期`Date.prototype.toLocaleString()`

```
// 将纯数字/字符串数组所有元素用逗号拼接起来  
var numArray = [12,564,'55',5,'8']  
console.log(numArray.toLocaleString())  //  12,564,55,5,8

// 数字同步转换百分比
var numArray = [12,564,'55',5,'8']  
console.log(numArray.toLocaleString('zh',{style:'percent'})) //1,200%,56,400%,55,500%,8

// 转换日期
const array1 = [1, 'a', new Date('21 Dec 1997 14:12:00 UTC')];
const localeString = array1.toLocaleString('en', { timeZone: 'UTC' });

console.log(localeString);//1,a,12/21/1997, 2:12:00 PM
```
