---
title: JavaScript的值传递和引用传递
date: 2021-03-11 21:12:40
categories: 前端
---

*   原文: [Explaining Value vs. Reference in Javascript](https://codeburst.io/explaining-value-vs-reference-in-javascript-647a975e12a0)

**为了保证可读性，本文采用意译而非直译。另外，本文版权归原作者所有，翻译仅用于学习。**

JavaScript有5种基本的数据类型，分别是：布尔、null、undefined、String和Number。这些基本类型在赋值的时候是通过值传递的方式。值得注意的是还有另外三种类型: Array、Function和Object，它们通过引用来传递。从底层技术上看，它们三都是对象。

### 基本数据类型

如果一个基本的数据类型绑定到某个变量，我们可以认为该变量包含这个基本数据类型的值。

```
var x = 10;
var y = 'abc';
var z = null;
```
当我们使用`=`将这些变量赋值到另外的变量，实际上是将对应的值拷贝了一份，然后赋值给新的变量。我们把它称作`值传递`。
```
var x = 10;
var y = 'abc';

var a = x;
var b = y;

console.log(x, y, a, b) // 10, 'abc', 10, 'abc'
```
`a`和`x`都包含10，`b`和`y`都包含`'abc'`，并且它们是完全独立的拷贝，互不干涉。如果我们将`a`的值改变，`x`不会受到影响。
```
var x = 10;
var y = 'abc';
var a = x;
var b = y;
a = 5;
b = 'def';
console.log(x, y, a, b); // 10, 'abc', 5, 'def'
```
###对象

如果一个变量绑定到一个非基本数据类型(Array, Function, Object)，那么它只记录了一个内存地址，该地址存放了具体的数据。注意之前提到指向基本数据类型的变量相当于包含了数据，而现在指向非基本数据类型的变量本身是不包含数据的。

对象在内存中被创建，当我们声明`arr = []`，我们在内存中创建了一个数组。`arr`记录的是该内存的地址。

```
var arr = []; // (a)
arr.push(1); // (b)
```

当执行完(a)之后，内存中创建了一个空的数组对象，其内存地址为`#001`，`arr`指向该地址。

| 变量 | 地址 | 对象 |
| --- | --- | --- |
| arr | #001 | [] |

当执行完(b)之后，数组对象中多了一个元素，但是数组的地址依然没有变，`arr`也没有变。

| 变量 | 地址 | 对象 |
| --- | --- | --- |
| arr | #001 | [1] |

### 引用传递

对象是通过引用传递，而不是值传递。也就是说，变量赋值只会将地址传递过去。
```
var reference = [1];
var refCopy = reference;
```

| 变量 | 地址 | 对象 |
| --- | --- | --- |
| reference | #001 | [1] |
| refCopy | #001 ||

`reference`和`refCopy`指向同一个数组。 如果我们更新`reference`，`refCopy`也会受到影响。
```
reference.push(2);
console.log(reference, refCopy); // [1, 2], [1, 2]
```
| 变量 | 地址 | 对象 |
| --- | --- | --- |
| reference | #001 | [1, 2] |
| refCopy | #001 |

###引用重新赋值

如果我们将一个已经赋值的变量重新赋值，那么它将包含新的数据或则引用地址。

```
var obj = { first: 'fundebug.com'};
obj = { second: 'fundebug.cn'};
```

`obj`从指向第一个对象变为指向第二个对象。

| 变量 | 地址 | 对象 |
| --- | --- | --- |
| obj | #001 | {first: ‘fundebug.com’} |
|  | #002 | {second: ‘fundebug.cn’} |

如果一个对象没有被任何变量指向，就如第一个对象(地址为`#001`)，JavaScript引擎的垃圾回收机制会将该对象销毁并释放内存。

###== 和 ===

对于引用类型的变量，`==`和`===`只会判断引用的地址是否相同，而不会判断对象具体里属性以及值是否相同。因此，如果两个变量指向相同的对象，则返回`true`。

```
var arrRef = ['Hi!'];
var arrRef2 = arrRef;

console.log(arrRef === arrRef2); // true
```
如果是不同的对象，即使包含相同的属性和值，也会返回`false`。
```
var arr1 = ["Hi!"];
var arr2 = ["Hi!"];

console.log(arr1 === arr2); // false
```
如果想判断两个不同的对象是否真的相同，一个简单的方法就是将它们转换为字符串然后判断。
```
var arr1str = JSON.stringify(arr1);
var arr2str = JSON.stringify(arr2);

console.log(arr1str === arr2str); // true
```

另一个方法就是递归地判断每一个属性的值，直到基本类型位置，然后判断是否相同。

###函数参数

当我们将基本类型数据传入函数，函数会将这些数据拷贝赋值给函数的参数变量。
```
var hundred = 100;
var two = 2;
function multiply(x, y) {
 return x * y;
}
var twoHundred = multiply(hundred, two);
```

`hundred`的值拷贝给变量`x`，`two`的值拷贝给变量`y`。

### 纯函数

对于一个函数，给定一个输入，返回一个唯一的输出。除此之外，不会对外部环境产生任何附带影响。我们机会称该函数为纯函数。所有函数内部定义的变量在函数返回之后都被垃圾回收掉。

但是，如果函数的输入是对象(Array, Function, Object)，那么传入的是一个引用。对该变量的操作将会影响到原本的对象。这样的编程手法将产生附带影响，是的代码的逻辑复杂和可读性变低。

因此，很多数组函数，比如`Array.map`和`Array.filter`是以纯函数的形式实现。虽然它们的参数是一个数组变量，但是通过深度拷贝并赋值给一个新的变量，然后在新的数组上操作，来防止原始数组被更改。

我们来看一个例子：
```
function changeAgeImpure(person) {
 person.age = 25;
 return person;
}
var alex = {
 name: 'Alex',
 age: 30
};
var changedAlex = changeAgeImpure(alex);
console.log(alex); // { name: 'Alex', age: 25 }
console.log(changedAlex); // { name: 'Alex', age: 25 }
```

在非纯函数`changeAgeImpure`中，将对象`person`的`age`更新并返回。原始的`alex`对象也被影响，`age`更新为25。

让我们来看如何实现一个纯函数：
```
function changeAgePure(person) {
 var newPersonObj = JSON.parse(JSON.stringify(person));
 newPersonObj.age = 25;
 return newPersonObj;
}
var alex = {
 name: 'Alex',
 age: 30
};
var alexChanged = changeAgePure(alex);
console.log(alex); // { name: 'Alex', age: 30 }
console.log(alexChanged); // { name: 'Alex', age: 25 }
```
我们通过`JSON.sringify`将对象变为一个字符串，然后再通过`JSON.parse`将字符串变回对象。通过该操作会生成一个新的对象。

###一道简单的面试题

值传递和引用传递经常在面试中被问到，来尝试回答一下如下代码如何输出：
```
function changeAgeAndReference(person) {
 person.age = 25;
 person = {
 name: 'John',
 age: 50
 };

 return person;
}
var personObj1 = {
 name: 'Alex',
 age: 30
};
var personObj2 = changeAgeAndReference(personObj1);
console.log(personObj1); // -> ?
console.log(personObj2); // -> ?
```
