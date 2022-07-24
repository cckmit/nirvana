---
title: JS Array常用方法详解及兼容性代码（一）
date: 2021-03-11 21:12:40
categories: 前端
---

[ES5中，Array新增API之forEach、filter、map、reduce]
## 一： forEach
forEach() 方法对数组的每个元素执行一次提供的函数。
**注意:** `没有返回一个新数组! & 没有返回值!`
**应用场景：**为一些相同的元素，绑定事件处理器！

**语法:**
```
array.forEach(callback(currentValue, index, array){
    //do something
}, this)

array.forEach(callback[, thisArg])
```

**参数：**
`callback`:
为数组中每个元素执行的函数，该函数接收三个参数：
>`currentValue(当前值)`
数组中正在处理的当前元素。
`index(索引)`
数组中正在处理的当前元素的索引。
`array`
forEach()方法正在操作的数组。

`thisArg`可选
可选参数。当执行回调 函数时用作this的值(参考对象)。

**返回值**
undefined

**注意：**`没有办法中止或者跳出 forEach 循环，除了抛出一个异常。如果你需要这样，使用forEach()方法是错误的，你可以用一个简单的循环作为替代。如果您正在测试一个数组里的元素是否符合某条件，且需要返回一个布尔值，那么可使用 Array.every或 Array.some。如果可用，新方法 find()或者findIndex 也可被用于真值测试的提早终止。`

**兼容旧环境**

```
if (!Array.prototype.forEach) {

  Array.prototype.forEach = function(callback, thisArg) {

    var T, k;

    if (this == null) {
      throw new TypeError(' this is null or not defined');
    }

    // 1. Let O be the result of calling toObject() passing the
    // |this| value as the argument.
    var O = Object(this);

    // 2. Let lenValue be the result of calling the Get() internal
    // method of O with the argument "length".
    // 3. Let len be toUint32(lenValue).
    var len = O.length >>> 0;

    // 4. If isCallable(callback) is false, throw a TypeError exception. 
    // See: http://es5.github.com/#x9.11
    if (typeof callback !== "function") {
      throw new TypeError(callback + ' is not a function');
    }

    // 5. If thisArg was supplied, let T be thisArg; else let
    // T be undefined.
    if (arguments.length > 1) {
      T = thisArg;
    }

    // 6. Let k be 0
    k = 0;

    // 7. Repeat, while k < len
    while (k < len) {

      var kValue;

      // a. Let Pk be ToString(k).
      //    This is implicit for LHS operands of the in operator
      // b. Let kPresent be the result of calling the HasProperty
      //    internal method of O with argument Pk.
      //    This step can be combined with c
      // c. If kPresent is true, then
      if (k in O) {

        // i. Let kValue be the result of calling the Get internal
        // method of O with argument Pk.
        kValue = O[k];

        // ii. Call the Call internal method of callback with T as
        // the this value and argument list containing kValue, k, and O.
        callback.call(T, kValue, k, O);
      }
      // d. Increase k by 1.
      k++;
    }
    // 8. return undefined
  };
}
```

## 二：filter
filter() 方法创建一个新数组, 其包含通过所提供函数实现的测试的所有元素。 

**语法:**
```
var new_array = arr.filter(callback[, thisArg])
```

**参数：**
`callback`:
用来测试数组的每个元素的函数。调用时使用参数 (element, index, array)。
返回true表示保留该元素（通过测试），false则不保留
`thisArg`可选
可选参数。当执行回调 函数时用作this的值(参考对象)。

**返回值**
一个新的通过测试的元素的集合的数组

**注意：**`filter 不会改变原数组，它返回过滤后的新数组。`

**兼容旧环境**

```
if (!Array.prototype.filter)
{
  Array.prototype.filter = function(fun /* , thisArg*/)
  {
    "use strict";

    if (this === void 0 || this === null)
      throw new TypeError();

    var t = Object(this);
    var len = t.length >>> 0;
    if (typeof fun !== "function")
      throw new TypeError();

    var res = [];
    var thisArg = arguments.length >= 2 ? arguments[1] : void 0;
    for (var i = 0; i < len; i++)
    {
      if (i in t)
      {
        var val = t[i];

        // NOTE: Technically this should Object.defineProperty at
        //       the next index, as push can be affected by
        //       properties on Object.prototype and Array.prototype.
        //       But that method's new, and collisions should be
        //       rare, so use the more-compatible alternative.
        if (fun.call(thisArg, val, i, t))
          res.push(val);
      }
    }

    return res;
  };
}
```
## 三：map
map() 方法创建一个新数组，其结果是该数组中的每个元素都调用一个提供的函数后返回的结果。
**语法:**
```
let new_array = arr.map(function callback(currentValue, index, array) { 
    // Return element for new_array 
}[, thisArg])
```

**参数：**
`callback`:
生成新数组元素的函数，使用三个参数：
>`currentValue`
callback 的第一个参数，数组中正在处理的当前元素。
`index`
callback 的第二个参数，数组中正在处理的当前元素的索引。
`array`
callback 的第三个参数，map 方法被调用的数组。

`thisArg`可选
可选参数。当执行回调 函数时用作this的值(参考对象)。

**返回值**
一个新数组，每个元素都是回调函数的结果。

**注意：**`不修改调用它的原数组本身（当然可以在 callback 执行时改变原数组）。`

**兼容旧环境**

```
if (!Array.prototype.map) {
  Array.prototype.map = function(callback, thisArg) {

    var T, A, k;

    if (this == null) {
      throw new TypeError(" this is null or not defined");
    }

    // 1. 将O赋值为调用map方法的数组.
    var O = Object(this);

    // 2.将len赋值为数组O的长度.
    var len = O.length >>> 0;

    // 3.如果callback不是函数,则抛出TypeError异常.
    if (Object.prototype.toString.call(callback) != "[object Function]") {
      throw new TypeError(callback + " is not a function");
    }

    // 4. 如果参数thisArg有值,则将T赋值为thisArg;否则T为undefined.
    if (thisArg) {
      T = thisArg;
    }

    // 5. 创建新数组A,长度为原数组O长度len
    A = new Array(len);

    // 6. 将k赋值为0
    k = 0;

    // 7. 当 k < len 时,执行循环.
    while(k < len) {

      var kValue, mappedValue;

      //遍历O,k为原数组索引
      if (k in O) {

        //kValue为索引k对应的值.
        kValue = O[ k ];

        // 执行callback,this指向T,参数有三个.分别是kValue:值,k:索引,O:原数组.
        mappedValue = callback.call(T, kValue, k, O);

        // 返回值添加到新数组A中.
        A[ k ] = mappedValue;
      }
      // k自增1
      k++;
    }

    // 8. 返回新数组A
    return A;
  };      
}
```
## 四：reduce
reduce() 方法对累加器和数组中的每个元素（从左到右）应用一个函数，将其减少为单个值。
**语法:**
```
arr.reduce(callback[, initialValue])
```

**参数：**
`callback`:
执行数组中每个值的函数，包含四个参数：
>`accumulator`
累加器累加回调的返回值; 它是上一次调用回调时返回的累积值，或initialValue（如下所示）。
`currentValue`
数组中正在处理的元素。
`currentIndex`可选
数组中正在处理的当前元素的索引。 如果提供了initialValue，则索引号为0，否则为索引为1。
`array`可选
调用reduce的数组

`initialValue`可选
用作第一个调用 callback的第一个参数的值。 如果没有提供初始值，则将使用数组中的第一个元素。 在没有初始值的空数组上调用 reduce 将报错。

**返回值**
函数累计处理的结果

**注意：**`如果没有提供initialValue，reduce 会从索引1的地方开始执行 callback 方法，跳过第一个索引。如果提供initialValue，从索引0开始。`

**兼容旧环境**

```
if (!Array.prototype.reduce) {
  Object.defineProperty(Array.prototype, 'reduce', {
    value: function(callback /*, initialValue*/) {
      if (this === null) {
        throw new TypeError( 'Array.prototype.reduce ' + 
          'called on null or undefined' );
      }
      if (typeof callback !== 'function') {
        throw new TypeError( callback +
          ' is not a function');
      }

      // 1. Let O be ? ToObject(this value).
      var o = Object(this);

      // 2. Let len be ? ToLength(? Get(O, "length")).
      var len = o.length >>> 0; 

      // Steps 3, 4, 5, 6, 7      
      var k = 0; 
      var value;

      if (arguments.length >= 2) {
        value = arguments[1];
      } else {
        while (k < len && !(k in o)) {
          k++; 
        }

        // 3. If len is 0 and initialValue is not present,
        //    throw a TypeError exception.
        if (k >= len) {
          throw new TypeError( 'Reduce of empty array ' +
            'with no initial value' );
        }
        value = o[k++];
      }

      // 8. Repeat, while k < len
      while (k < len) {
        // a. Let Pk be ! ToString(k).
        // b. Let kPresent be ? HasProperty(O, Pk).
        // c. If kPresent is true, then
        //    i.  Let kValue be ? Get(O, Pk).
        //    ii. Let accumulator be ? Call(
        //          callbackfn, undefined,
        //          « accumulator, kValue, k, O »).
        if (k in o) {
          value = callback(value, o[k], k, o);
        }

        // d. Increase k by 1.      
        k++;
      }

      // 9. Return accumulator.
      return value;
    }
  });
}
```




