---
title: JS Array常用方法详解及兼容性代码（二）
date: 2021-03-11 21:12:40
categories: 前端
---

[ ES5中，Array新增API（下）之reduceRight、every、indexOf]

#五： reduceRight
reduceRight() 方法接受一个函数作为累加器（accumulator）和数组的每个值（从右到左）将其减少为单个值。
**注意:** `Array.prototype.reduce()方法对累加器和数组中的每个元素（从左到右）应用一个函数，将其减少为单个值。") 的执行方向相反`

**示例:**
```
let flattened = [
    [0, 1], 
    [2, 3], 
    [4, 5]
].reduceRight((a, b) => {
    return a.concat(b);
}, []);

// flattened is [4, 5, 2, 3, 0, 1]
```
**语法:**
```
arr.reduceRight(callback[, initialValue])
```

**参数：**
`callback`
一个回调函数，用来操作数组中的每个元素，可接受四个参数：
>`previousValue`
上一次调用回调的返回值，或提供的 initialValue
`currentValue`
当前被处理的元素
`index`
当前处理元素的索引
`array`
调用 reduce 的数组

`initialValue`
可作为第一次调用回调 callback 的第一个参数
**返回值**
执行之后的返回值

**兼容旧环境**

```
if ('function' !== typeof Array.prototype.reduceRight) {
  Array.prototype.reduceRight = function(callback /*, initialValue*/) {
    'use strict';
    if (null === this || 'undefined' === typeof this) {
      throw new TypeError('Array.prototype.reduce called on null or undefined');
    }
    if ('function' !== typeof callback) {
      throw new TypeError(callback + ' is not a function');
    }
    var t = Object(this), len = t.length >>> 0, k = len - 1, value;
    if (arguments.length >= 2) {
      value = arguments[1];
    } else {
      while (k >= 0 && !(k in t)) {
        k--;
      }
      if (k < 0) {
        throw new TypeError('Reduce of empty array with no initial value');
      }
      value = t[k--];
    }
    for (; k >= 0; k--) {
      if (k in t) {
        value = callback(value, t[k], k, t);
      }
    }
    return value;
  };
}
```
#六： every
every() 方法测试数组的所有元素是否都通过了指定函数的测试。

**语法:**
```
arr.every(callback[, thisArg])
```

**参数：**
`callback`
用来测试每个元素的函数。
`thisArg`
执行 callback 时使用的 this 值。

**注意：**`every 和数学中的"所有"类似，当所有的元素都符合条件才返回true。另外，空数组也是返回true。`

**兼容旧环境**

```
if (!Array.prototype.every)
{
  Array.prototype.every = function(fun /*, thisArg */)
  {
    'use strict';

    if (this === void 0 || this === null)
      throw new TypeError();

    var t = Object(this);
    var len = t.length >>> 0;
    if (typeof fun !== 'function')
        throw new TypeError();

    var thisArg = arguments.length >= 2 ? arguments[1] : void 0;
    for (var i = 0; i < len; i++)
    {
      if (i in t && !fun.call(thisArg, t[i], i, t))
        return false;
    }

    return true;
  };
}
```
#七： indexOf
indexOf()方法返回在数组中可以找到一个给定元素的第一个索引，如果不存在，则返回-1。
**注意:** `对于String方法，请参阅 [String.prototype.indexOf()]")。
`

**语法:**
```
arr.indexOf(searchElement)
arr.indexOf(searchElement[, fromIndex = 0])
```

**参数：**
`searchElement`
要查找的元素
`fromIndex`
开始查找的位置。如果该索引值大于或等于数组长度，意味着不会在数组里查找，返回-1。如果参数中提供的索引值是一个负值，则将其作为数组末尾的一个抵消，即-1表示从最后一个元素开始查找，-2表示从倒数第二个元素开始查找 ，以此类推。 注意：如果参数中提供的索引值是一个负值，并不改变其查找顺序，查找顺序仍然是从前向后查询数组。如果抵消后的索引值仍小于0，则整个数组都将会被查询。其默认值为0.

**返回值**
首个被找到的元素在数组中的索引位置; 若没有找到则返回 -1

**兼容旧环境**

```
if (!Array.prototype.indexOf) {
  Array.prototype.indexOf = function(searchElement, fromIndex) {

    var k;

    // 1. Let O be the result of calling ToObject passing
    //    the this value as the argument.
    if (this == null) {
      throw new TypeError('"this" is null or not defined');
    }

    var O = Object(this);

    // 2. Let lenValue be the result of calling the Get
    //    internal method of O with the argument "length".
    // 3. Let len be ToUint32(lenValue).
    var len = O.length >>> 0;

    // 4. If len is 0, return -1.
    if (len === 0) {
      return -1;
    }

    // 5. If argument fromIndex was passed let n be
    //    ToInteger(fromIndex); else let n be 0.
    var n = +fromIndex || 0;

    if (Math.abs(n) === Infinity) {
      n = 0;
    }

    // 6. If n >= len, return -1.
    if (n >= len) {
      return -1;
    }

    // 7. If n >= 0, then Let k be n.
    // 8. Else, n<0, Let k be len - abs(n).
    //    If k is less than 0, then let k be 0.
    k = Math.max(n >= 0 ? n : len - Math.abs(n), 0);

    // 9. Repeat, while k < len
    while (k < len) {
      // a. Let Pk be ToString(k).
      //   This is implicit for LHS operands of the in operator
      // b. Let kPresent be the result of calling the
      //    HasProperty internal method of O with argument Pk.
      //   This step can be combined with c
      // c. If kPresent is true, then
      //    i.  Let elementK be the result of calling the Get
      //        internal method of O with the argument ToString(k).
      //   ii.  Let same be the result of applying the
      //        Strict Equality Comparison Algorithm to
      //        searchElement and elementK.
      //  iii.  If same is true, return k.
      if (k in O && O[k] === searchElement) {
        return k;
      }
      k++;
    }
    return -1;
  };
}
```
# 八： lastIndexOf
lastIndexOf() 方法返回指定元素（也即有效的 JavaScript 值或变量）在数组中的最后一个的索引，如果不存在则返回 -1。从数组的后面向前查找，从 fromIndex 处开始。

**语法:**
```
arr.lastIndexOf(searchElement[, fromIndex = arr.length - 1])
```

**参数：**
`searchElement`
被查找的元素。
`fromIndex`
从此位置开始逆向查找。默认为数组的长度减 1，即整个数组都被查找。如果该值大于或等于数组的长度，则整个数组会被查找。如果为负值，将其视为从数组末尾向前的偏移。即使该值为负，数组仍然会被从后向前查找。如果该值为负时，其绝对值大于数组长度，则方法返回 -1，即数组不会被查找。
**返回值**
数组中最后一个元素的索引，如未找到返回-1

**注意：**`lastIndexOf使用[严格相等](strict equality，即 ===）比较 searchElement和数组中的元素。
`

**兼容旧环境**

```
if (!Array.prototype.lastIndexOf) {
  Array.prototype.lastIndexOf = function(searchElement /*, fromIndex*/) {
    'use strict';

    if (this === void 0 || this === null) {
      throw new TypeError();
    }

    var n, k,
        t = Object(this),
        len = t.length >>> 0;
    if (len === 0) {
      return -1;
    }

    n = len - 1;
    if (arguments.length > 1) {
      n = Number(arguments[1]);
      if (n != n) {
        n = 0;
      }
      else if (n != 0 && n != (1 / 0) && n != -(1 / 0)) {
        n = (n > 0 || -1) * Math.floor(Math.abs(n));
      }
    }

    for (k = n >= 0
          ? Math.min(n, len - 1)
          : len - Math.abs(n); k >= 0; k--) {
      if (k in t && t[k] === searchElement) {
        return k;
      }
    }
    return -1;
  };
}
```

