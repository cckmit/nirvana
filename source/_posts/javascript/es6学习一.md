---
title: es6学习一
date: 2021-03-11 21:12:40
categories: 前端
---

# let和const
介绍let和const之前先说说var关键字，他有几个特点：

 1. 可以重复声明
```javascript
	var a = 1;
	var a = 2;
```
 2. 无法限制修改
 ```javascript
		var a = 1;
		a = 2
 ```
 3. 没有块级作用域
 ```javascript
	for(var i =0; i < 10; i++)  {
		console.log(i)
	}
	console.log(i) // 10
 ```
##  let的使用
#### 基本使用
ES6 新增了let命令，用来声明变量。它的用法类似于var，但是所声明的变量，只在let命令所在的代码块内有效。

```javascript
{
  let a = 10;
  var b = 1;
}

a // ReferenceError: a is not defined.
b // 1

// for循环的计数器，就很合适使用let命令
for (let i = 0; i < 10; i++) {
  // ...
}

console.log(i);
// ReferenceError: i is not defined
```

#### 不存在变量提升
var命令会发生“变量提升”现象，即变量可以在声明之前使用，值为undefined。这种现象多多少少是有些奇怪的，按照一般的逻辑，变量应该在声明语句之后才可以使用。

为了纠正这种现象，let命令改变了语法行为，它所声明的变量一定要在声明后使用，否则报错。

```javascript
// var 的情况
console.log(foo); // 输出undefined
var foo = 2;

// let 的情况
console.log(bar); // 报错ReferenceError
let bar = 2;
```

#### 暂时性死区

在代码块内，使用let命令声明变量之前，该变量都是不可用的。这在语法上，称为“暂时性死区”

```javascript
if (true) {
  // TDZ开始
  tmp = 'abc'; // ReferenceError
  console.log(tmp); // ReferenceError

  let tmp; // TDZ结束
  console.log(tmp); // undefined

  tmp = 123;
  console.log(tmp); // 123
}
```
上面代码中，在let命令声明变量tmp之前，都属于变量tmp的“死区”。

```javascript
typeof x; // ReferenceError
let x;

typeof undeclared_variable // "undefined"
```
“暂时性死区”也意味着typeof不再是一个百分之百安全的操作。

#### 不允许重复声明 
let不允许在相同作用域内，重复声明同一个变量。

```javascript
// 报错
function func() {
  let a = 10;
  var a = 1;
}

// 报错
function func() {
  let a = 10;
  let a = 1;
}

function func(arg) {
  let arg;
}
func() // 报错

function func(arg) {
  {
    let arg;
  }
}
func() // 不报错
```

#### ES6 的块级作用域
let实际上为 JavaScript 新增了块级作用域。

```javascript
function f1() {
  let n = 5;
  if (true) {
    let n = 10;
  }
  console.log(n); // 5
}
```

## const使用
const声明一个只读的常量。一旦声明，常量的值就不能改变。
const声明的变量不得改变值，这意味着，const一旦声明变量，就必须立即初始化，不能留到以后赋值。
const的作用域与let命令相同：只在声明所在的块级作用域内有效。
const命令声明的常量也是不提升，同样存在暂时性死区，只能在声明的位置后面使用。
const声明的常量，也与let一样不可重复声明。


# 箭头函数
#### 基本使用
ES6 允许使用“箭头”（=>）定义函数。

```js
var f = v => v;

// 等同于
var f = function (v) {
  return v;
};


var f = () => 5;
// 等同于
var f = function () { return 5 };

var sum = (num1, num2) => num1 + num2;
// 等同于
var sum = function(num1, num2) {
  return num1 + num2;
};
```

如果箭头函数的代码块部分多于一条语句，就要使用大括号将它们括起来，并且使用return语句返回。

```js
var sum = (num1, num2) => { return num1 + num2; }
```
箭头函数的一个用处是简化回调函数。

```js
// 正常函数写法
[1,2,3].map(function (x) {
  return x * x;
});

// 箭头函数写法
[1,2,3].map(x => x * x);
```

```js
// 正常函数写法
var result = values.sort(function (a, b) {
  return a - b;
});

// 箭头函数写法
var result = values.sort((a, b) => a - b);
```
#### 使用注意点
箭头函数有几个使用注意点。

（1）函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象。

（2）不可以当作构造函数，也就是说，不可以使用new命令，否则会抛出一个错误。

（3）不可以使用arguments对象，该对象在函数体内不存在。如果要用，可以用 rest 参数代替。

（4）不可以使用yield命令，因此箭头函数不能用作 Generator 函数。

上面四点中，第一点尤其值得注意。this对象的指向是可变的，但是在箭头函数中，它是固定的。

```js
var handler = {
  id: '123456',

  init: function() {
    document.addEventListener('click',
      event => this.doSomething(event.type), false);
  },

  doSomething: function(type) {
    console.log('Handling ' + type  + ' for ' + this.id);
  }
};

handler.init() 
// 出发点击事件会打印： Handling click for 123456
```
这里要理解this的指向问题

this指向的固定化，并不是因为箭头函数内部有绑定this的机制，实际原因是箭头函数根本没有自己的this，导致内部的this就是外层代码块的this。正是因为它没有this，所以也就不能用作构造函数。

所以，箭头函数转成 ES5 的代码如下。

```js
// ES6
function foo() {
  setTimeout(() => {
    console.log('id:', this.id);
  }, 100);
}

// ES5
function foo() {
  var _this = this;

  setTimeout(function () {
    console.log('id:', _this.id);
  }, 100);
}
```
上面代码中，转换后的 ES5 版本清楚地说明了，箭头函数里面根本没有自己的this，而是引用外层的this。

请问下面的代码之中有几个this？

```js
function foo() {
  return () => {
    return () => {
      return () => {
        console.log('id:', this.id);
      };
    };
  };
}

var f = foo.call({id: 1});

var t1 = f.call({id: 2})()(); // id: 1
var t2 = f().call({id: 3})(); // id: 1
var t3 = f()().call({id: 4}); // id: 1
```
# 解构赋值
ES6 允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为解构（Destructuring）。本质上，这种写法属于“模式匹配”，只要等号两边的模式相同，左边的变量就会被赋予对应的值。
什么使模式匹配，从数组或对象等号左边和等号右边模式相同，对应的变量就是对应的值
#### 数组的解构赋值
```js
// es5可以这样声明变量
var a = 1;
var b = 2;
var c = 3;
// 也可以这样
var a = 1, b = 2, c = 3;

// es6
let [a, b, c] = [1, 2, 3]

let let [x, , y] = [1, 2, 3]; //  x = 1, y = 3
let [head, ...tail] = [1, 2, 3, 4]; // head: 1, tail: [1, 2, 3, 4]
let [x, y, ...z] = ['a'];
x // "a"
y // undefined
z // []
```
如果解构不成功变量的值就会等于undefined

不完全结构嵌套

```js
let [a, [b, c], d] = [1, [2, 3, 4], 5]
let [x, y] = [1, 2, 3]; // x = 1, y = 2
```
上述代码中左右两边对应的结构式相同的，解构过程中会根据从左到右顺序一次给对应的变量赋值

注意：如果等号右边不是数组，则会报错

```js
var [a] = 1
var [c] = true
var [a] = {}
var [a] = undefined
var [a] = null
// xx is not iterable
```
可以设置默认值

```js
let [foo = true] = [];
foo // true
let [x, y = 'b'] = ['a']; // x='a', y='b'
let [x, y = 'b'] = ['a', undefined]; // x='a', y='b'
let [x = 1] = [undefined];
x // 1
let [x = 1] = [null];
x // null
```
以上的代码总结：设置默认是es6内部使用严格（===）进行判断变量是否为undefined，只有等于undefined时，默认值设置才会生效

```js
let [x = 1, y = x] = [];     // x=1; y=1
let [x = 1, y = x] = [2];    // x=2; y=2
let [x = 1, y = x] = [1, 2]; // x=1; y=2
// 之所以会报错，是因为x用y做默认值时，y还没有声明。
let [x = y, y = 1] = [];     // ReferenceError: y is not defined
```
默认值可以引用解构赋值的其他变量，但该变量必须已经声明。

#### 对象的解构赋值
对象的解构赋值和数组时类似
对象和变量解构对应，不同的是，属性必须是对象中存在的属性，否则变量就会等于undefined
```js
let { foo, bar } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"

let { baz } = { foo: "aaa", bar: "bbb" };
baz // undefined

// 如果变量名和属性名不一样的情况
let { foo: baz } = { foo: 'aaa', bar: 'bbb' };
baz // "aaa"

let obj = { first: 'hello', last: 'world' };
let { first: f, last: l } = obj;
f // 'hello'
l // 'world'
 
// 那么可以总结一下：以上的解构赋值的写法实际上是下面这种方式的简写
let { foo: foo, bar: bar } = { foo: "aaa", bar: "bbb" };
```
也就是说，对象的解构赋值的内部机制，是先找到同名属性，然后再赋给对应的变量。真正被赋值的是后者，而不是前者。
```js
let { foo: baz } = { foo: "aaa", bar: "bbb" };
baz // "aaa"
foo // error: foo is not defined
```
上面代码中，foo是匹配的模式，baz才是变量。真正被赋值的是变量baz，而不是模式foo。

对象的解构也可以指定默认值。
```js
var {x = 3} = {};
x // 3

var {x, y = 5} = {x: 1};
x // 1
y // 5

var {x: y = 3} = {};
y // 3

var {x: y = 3} = {x: 5};
y // 5

var { message: msg = 'Something went wrong' } = {};
msg // "Something went wrong"

// 默认值生效的条件是，对象的属性值严格等于undefined。
var {x = 3} = {x: undefined};
x // 3

var {x = 3} = {x: null};
x // null
```

#### 字符串的解构赋值
字符串也可以解构赋值。这是因为此时，字符串被转换成了一个类似数组的对象。

```js
const [a, b, c, d, e] = 'hello';
a // "h"
b // "e"
c // "l"
d // "l"
e // "o"
```
类似数组的对象都有一个length属性，因此还可以对这个属性解构赋值。
```js
let {length : len} = 'hello';
len // 5
```
#### 用途
变量的解构赋值用途很多。

（1）交换变量值

```js
let x = 1;
let y = 2;

[x, y] = [y, x];
```
（2）从函数返回多个值

```js
// 返回一个数组

function example() {
  return [1, 2, 3];
}
let [a, b, c] = example();

// 返回一个对象

function example() {
  return {
    foo: 1,
    bar: 2
  };
}
let { foo, bar } = example();
```
（3）提取 JSON 数据
```js
let jsonData = {
  retCode: '000000',
  retInfo: "字段校验成功",
  data: [867, 5309]
};

let { retCode, retInfo, data: number } = jsonData;

console.log(id, status, number);
// '000000', "字段校验成功", [867, 5309]
```
等等。。。。。。
# 对象、数组、字符串的扩展
#### 对象的扩展
对象定义过程中更简单写法
```js
let io = 'computed'
const obj = {
	io,
	foo() {
		console.log('hello')
	}
}

// es5
var io = 'computed'
var obj = {
	io: io,
	foo: function() {
		console.log('hello')
	}
}
```

#### 数组的扩展
（1）扩展运算符
扩展运算符（spread）是三个点（...）。它好比 rest 参数的逆运算，将一个数组转为用逗号分隔的参数序列。

```js
console.log(1, ...[2,3,4]) // 1,2,3,4
// 相当于
console.log(1,2,3,4)
```
```js
// 用于函数的参数
function add(x, y) {
	return x + y
}
let number = [35, 27]
add(...number) // 62
// 相当于
add(number[0], number[1])
```
```js
// 用于函数替代apply的方法
// ES5 的写法
Math.max.apply(null, [14, 3, 77])
// ES6 的写法
Math.max(...[14, 3, 77])
// 等同于
Math.max(14, 3, 77);

// ES5
new (Date.bind.apply(Date, [null, 2015, 1, 1]))
// ES6
new Date(...[2015, 1, 1]);

```

扩展运算符的应用

1.复制数组

```js
// 浅度复制
const arr = ['hello', 'world']
const arrCopy = arr
// 这样的简单复制只是将数组的指针赋给了arrCopy变量，所以arr和arrCopy数组的元素改变时，另一个也会同时改变
arr[0] = 1
console.log(arrCopy) // [0, 'world']

// es5解决方法
const arr = ['hello', 'world']
const arrCopy = arr.concat()
arr[0] = 'OK'
console.log(arrCopy) // ['hello', 'world']

// 扩展运算符提供了复制数组的简便写法。
const a1 = [1, 2];
// 写法一
const a2 = [...a1];
// 写法二
const [...a2] = a1;
/* 注意：扩展运算符只能赋值数组元素为基本数据类型的元素，
如果元素的数据类型为引用类型，依然保存的是应用类型的指针 */
```
2.合并数组
```js
const a = [2, 3, 5]
const b = [12, 13]

// es5
a.concat(b) // [2,3,5,12,13]

// es6
const result = [...a, ...b] // [2,3,5,12,13]

// 这两种方式也都是浅拷贝，如果修改了原数组的成员，会同步反映到新数组。
const a1 = [{ foo: 1 }];
const a2 = [{ bar: 2 }];

const a3 = a1.concat(a2);
const a4 = [...a1, ...a2];

a3[0] === a1[0] // true
a4[0] === a1[0] // true
```

3.与解构赋值结合
扩展运算符可以与解构赋值结合起来，用于生成数组。

```js
// ES5
a = list[0], rest = list.slice(1)
// ES6
[a, ...rest] = list
```

```js
const [first, ...rest] = [1, 2, 3, 4, 5];
first // 1
rest  // [2, 3, 4, 5]

const [first, ...rest] = [];
first // undefined
rest  // []

const [first, ...rest] = ["foo"];
first  // "foo"
rest   // []
```
如果将扩展运算符用于数组赋值，只能放在参数的最后一位，否则会报错。

```js
const [...butLast, last] = [1, 2, 3, 4, 5];
// 报错

const [first, ...middle, last] = [1, 2, 3, 4, 5];
// 报错
```

（2）Array.from()
Array.from方法用于将两类对象转为真正的数组：类似数组的对象（伪数组）和可遍历（iterable）的对象（包括 ES6 新增的数据结构 Set 和 Map）。

```js
let arrayLike = {
    '0': 'a',
    '1': 'b',
    '2': 'c',
    length: 3
};

// ES5的写法
var arr1 = [].slice.call(arrayLike); // ['a', 'b', 'c']

// ES6的写法
let arr2 = Array.from(arrayLike); // ['a', 'b', 'c']
```
实际应用中，常见的类似数组的对象是 DOM 操作返回的 NodeList 集合，以及函数内部的arguments对象。Array.from都可以将它们转为真正的数组。

```js
// NodeList对象
let ps = document.querySelectorAll('p');
Array.from(ps).filter(p => {
  return p.textContent.length > 100;
});

// arguments对象
function foo() {
  var args = Array.from(arguments);
  // ...
}
```

（3）Array.of()
Array.of方法用于将一组值，转换为数组。

```js
Array.of(3, 11, 8) // [3,11,8]
Array.of(3) // [3]
Array.of(3).length // 1
```
（4）数组实例的 includes()
Array.prototype.includes方法返回一个布尔值，表示某个数组是否包含给定的值，与字符串的includes方法类似。ES2016 引入了该方法。
参数： 该方法接受两个参数，第一个参数接受一个数值，第二个参数搜索的起始位置，默认为0。如果第二个参数为负数，则表示倒数的位置，如果这时它大于数组长度（比如第二个参数为-4，但数组长度为3），则会重置为从0开始。
返回值： 布尔类型，true表示搜索的结果在当前的数组中

```js
[1, 2, 3].includes(2)     // true
[1, 2, 3].includes(4)     // false
[1, 2, NaN].includes(NaN) // true

[1, 2, 3].includes(3, 3);  // false
[1, 2, 3].includes(3, -1); // true
```
与indexOf的比较：indexOf的含义是找到参数值的第一个出现位置，所以要去比较是否不等于-1，表达起来不够直观。二是，它内部使用严格相等运算符（===）进行判断，这会导致对NaN的误判。

```js
[NaN].indexOf(NaN)
```

下面代码用来检查当前环境是否支持该方法，如果不支持，部署一个简易的替代版本。

```js
const contains = (() =>
  Array.prototype.includes
    ? (arr, value) => arr.includes(value)
    : (arr, value) => arr.some(el => el === value)
)();
contains(['foo', 'bar'], 'baz'); // => false
```

#### 字符串的扩展
模版字符串

模板字符串（template string）是增强版的字符串，用反引号（`）标识。它可以当作普通字符串使用，也可以用来定义多行字符串，或者在字符串中嵌入变量。

传统的 JavaScript 语言，输出模板通常是这样写的（下面使用了 jQuery 的方法）。

```js
$('#result').append(
  'There are <b>' + basket.count + '</b> ' +
  'items in your basket, ' +
  '<em>' + basket.onSale +
  '</em> are on sale!'
);
/*上面这种写法相当繁琐不方便，因为变量与字符串之间的拼接需要用到+和引号，
并且换行时不方便，ES6 引入了模板字符串解决这个问题。
*/
$('#result').append(`
  There are <b>${basket.count}</b> items
   in your basket, <em>${basket.onSale}</em>
  are on sale!
`);
```

```js
// 普通字符串
`In JavaScript '\n' is a line-feed.`

// 多行字符串
`In JavaScript this is
 not legal.`

console.log(`string text line 1
string text line 2`);

// 字符串中嵌入变量
let name = "Bob", time = "today";
`Hello ${name}, how are you ${time}?`
```

#### 函数的扩展
（1）函数参数的默认值
es6增加了函数参数的默认值

```js
// es5
function Foo (x, y) {
	this.x = x || 'hello';
	this.y = y || 'world'
}

// es6
function Foo (x = 'hello', y = 'world') {
	this.x = x ;
	this.y = y'
}
```
设置函数的默认参数之后，函数内部不能再次声明，否则会报错

```js
function boo (x = 1) {
	let x;
}
// Identifier 'x' has already been declared
```

另外，一个容易忽略的地方是，参数默认值不是传值的，而是每次都重新计算默认值表达式的值。也就是说，参数默认值是惰性求值的。

```js
let x = 99;
function foo(p = x + 1) {
  console.log(p);
}

foo() // 100

x = 100;
foo() // 101
```
上面代码中，参数p的默认值是x + 1。这时，每次调用函数foo，都会重新计算x + 1，而不是默认p等于 100。

（2）rest 参数
ES6 引入 rest 参数（形式为...变量名），用于获取函数的多余参数，这样就不需要使用arguments对象了。rest 参数搭配的变量是一个数组，该变量将多余的参数放入数组中。
```js
function add(...values) {
  let sum = 0;

  for (var val of values) {
    sum += val;
  }

  return sum;
}

add(2, 5, 3) // 10
```
上面代码的add函数是一个求和函数，利用 rest 参数，可以向该函数传入任意数目的参数。

下面是一个 rest 参数代替arguments变量的例子。
```js
// arguments变量的写法
function sortNumbers() {
  return Array.prototype.slice.call(arguments).sort();
}

// rest参数的写法
const sortNumbers = (...numbers) => numbers.sort();
```
上面代码的两种写法，比较后可以发现，rest 参数的写法更自然也更简洁。

arguments对象不是数组，而是一个类似数组的对象。所以为了使用数组的方法，必须使用Array.prototype.slice.call先将其转为数组。rest 参数就不存在这个问题，它就是一个真正的数组，数组特有的方法都可以使用。下面是一个利用 rest 参数改写数组push方法的例子。
```js
function push(array, ...items) {
  items.forEach(function(item) {
    array.push(item);
    console.log(item);
  });
}

var a = [];
push(a, 1, 2, 3)
```
ES6 引入 rest 参数（形式为...变量名），用于获取函数的多余参数，这样就不需要使用arguments对象了。rest 参数搭配的变量是一个数组，该变量将多余的参数放入数组中。
```js
function add(...values) {
  let sum = 0;

  for (var val of values) {
    sum += val;
  }

  return sum;
}

add(2, 5, 3) // 10
```
上面代码的add函数是一个求和函数，利用 rest 参数，可以向该函数传入任意数目的参数。

下面是一个 rest 参数代替arguments变量的例子。
```js
// arguments变量的写法
function sortNumbers() {
  return Array.prototype.slice.call(arguments).sort();
}

// rest参数的写法
const sortNumbers = (...numbers) => numbers.sort();
```
上面代码的两种写法，比较后可以发现，rest 参数的写法更自然也更简洁。

arguments对象不是数组，而是一个类似数组的对象。所以为了使用数组的方法，必须使用Array.prototype.slice.call先将其转为数组。rest 参数就不存在这个问题，它就是一个真正的数组，数组特有的方法都可以使用。下面是一个利用 rest 参数改写数组push方法的例子。
```js
function push(array, ...items) {
  items.forEach(function(item) {
    array.push(item);
    console.log(item);
  });
}

var a = [];
push(a, 1, 2, 3)
```
注意，rest 参数之后不能再有其他参数（即只能是最后一个参数），否则会报错。
```js
// 报错
function f(a, ...b, c) {
  // ...
}
```
函数的length属性，不包括 rest 参数。
```js
(function(a) {}).length  // 1
(function(...a) {}).length  // 0
(function(a, ...b) {}).length  // 1
```
注意，rest 参数之后不能再有其他参数（即只能是最后一个参数），否则会报错。
```js
// 报错
function f(a, ...b, c) {
  // ...
}
```
函数的length属性，不包括 rest 参数。
```js
(function(a) {}).length  // 1
(function(...a) {}).length  // 0
(function(a, ...b) {}).length  // 1
```
