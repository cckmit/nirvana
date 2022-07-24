---
title: npm qs模块
date: 2021-03-11 21:12:40
categories: 前端
---

### 简介

qs 是一个增加了一些安全性的查询字符串解析和序列化字符串的库。
主要维护者：Jordan Harband
最初创建者和维护者：TJ Holowaychuk

### 用法

```
var qs = require('qs');
var assert = require('assert');

var obj = qs.parse('a=c');
assert.deepEqual(obj, { a: 'c' });

var str = qs.stringify(obj);
assert.equal(str, 'a=c');

```

1.  解析对象

    ```
     qs.parse(string, [options]);

    ```

*   qs 允许在查询字符串中使用`[]`的方式创建嵌套的对象。例如，字符串`'foo[bar]=baz'`可以转换为：

    ```
      	assert.deepEqual(qs.parse('foo[bar]=baz'), {
      	    foo: {
      	        bar: 'baz'
      	    }
      	});

    ```

*   When using the plainObjects option the parsed value is returned as a null object, created via Object.create(null) and as such you should be aware that prototype methods will not exist on it and a user may set those names to whatever value they like:

    ```
      	var nullObject = qs.parse('a[hasOwnProperty]=b', { plainObjects: true });
      	assert.deepEqual(nullObject, { a: { hasOwnProperty: 'b' } });

    ```

*   By default parameters that would overwrite properties on the object prototype are ignored, if you wish to keep the data from those fields either use plainObjects as mentioned above, or set allowPrototypes to true which will allow user input to overwrite those properties. WARNING It is generally a bad idea to enable this option as it can cause problems when attempting to use the properties that have been overwritten. Always be careful with this option.

    ```
      	var protoObject = qs.parse('a[hasOwnProperty]=b', { allowPrototypes: true });
      	assert.deepEqual(protoObject, { a: { hasOwnProperty: 'b' } });

    ```

*   也可以解析 URI 编码：

    ```
      	assert.deepEqual(qs.parse('a%5Bb%5D=c'), {
      	    a: { b: 'c' }
      	});

    ```

*   还可以像这样嵌套对象：`'foo[bar][baz]=foobarbaz'`：

    ```
      	assert.deepEqual(qs.parse('foo[bar][baz]=foobarbaz'), {
      	    foo: {
      	        bar: {
      	            baz: 'foobarbaz'
      	        }
      	    }
      	});

    ```

*   当使用嵌套对象时，qs 在默认情况下最多解析到的深度是第五层（注：从第一个方括号到算起，到第五个方括号），例如尝试解析一个这样的字符串`'a[b][c][d][e][f][g][h][i]=j'`将会得到以下结果：

    ```
      	var expected = {
      	    a: {
      	        b: {
      	            c: {
      	                d: {
      	                    e: {
      	                        f: {
      	                            '[g][h][i]': 'j'
      	                        }
      	                    }
      	                }
      	            }
      	        }
      	    }
      	};
      	var string = 'a[b][c][d][e][f][g][h][i]=j';
      	assert.deepEqual(qs.parse(string), expected);

    ```

*   可以给 qs.parse 传递一个 `depth` 参数覆盖默认值：

    ```
      	var deep = qs.parse('a[b][c][d][e][f][g][h][i]=j', { depth: 1 });
      	assert.deepEqual(deep, { a: { b: { '[c][d][e][f][g][h][i]': 'j' } } });

    ```

*   当 qs 用于解析用户输入的时候，解析深度的限制有助于减轻用户的滥用行为。最好将 depth 设置为一个合理的尽量小的数字。

*   出于类似的原因，qs 在默认情况下最多解析 1000 个参数。通过传递 `parameterLimit` 参数可以修改默认值：

    ```
      	var limited = qs.parse('a=b&c=d', { parameterLimit: 1 });
      	assert.deepEqual(limited, { a: 'b' });

    ```

*   忽略查询字符串开头的 `?` 可以使用 `ignoreQueryPrefix`：

    ```
      	var prefixed = qs.parse('?a=b&c=d', { ignoreQueryPrefix: true });
      	assert.deepEqual(prefixed, { a: 'b', c: 'd' });

    ```

*   还可以根据自定义的分隔符来解析 `delimiter`：

    ```
      	var delimited = qs.parse('a=b;c=d', { delimiter: ';' });
      	assert.deepEqual(delimited, { a: 'b', c: 'd' });

    ```

*   分隔符可以是正则表达式：

    ```
      	var regexed = qs.parse('a=b;c=d,e=f', { delimiter: /[;,]/ });
      	assert.deepEqual(regexed, { a: 'b', c: 'd', e: 'f' });

    ```

*   `allowDots` 选项可以启用点表示法：

    ```
      	var withDots = qs.parse('a.b=c', { allowDots: true });
      	assert.deepEqual(withDots, { a: { b: 'c' } });

    ```

2.  解析数组

*   qs 也可以用`[]`解析数组：

    ```
      	var withArray = qs.parse('a[]=b&a[]=c');
      	assert.deepEqual(withArray, { a: ['b', 'c'] });

    ```

*   可以指定数组索引：

    ```
      	var withIndexes = qs.parse('a[1]=c&a[0]=b');
      	assert.deepEqual(withIndexes, { a: ['b', 'c'] });

    ```

*   请注意，如果想要将字符串解析成数组而不是对象，那么`[]`之间的值必须是一个数字。 在创建具有特定索引的数组时，qs会将稀疏数组压缩为仅保留其顺序的现有值：

    ```
      	var noSparse = qs.parse('a[1]=b&a[15]=c');
      	assert.deepEqual(noSparse, { a: ['b', 'c'] });

    ```

*   空字符串也是一个值，并将被保留：

    ```
      	var withEmptyString = qs.parse('a[]=&a[]=b');
      	assert.deepEqual(withEmptyString, { a: ['', 'b'] });

      	var withIndexedEmptyString = qs.parse('a[0]=b&a[1]=&a[2]=c');
      	assert.deepEqual(withIndexedEmptyString, { a: ['b', '', 'c'] });

    ```

*   qs 还会限制数组最大索引为 20，任何索引大于20的数组成员都将被转换为以索引为键的对象：

    ```
     	var withMaxIndex = qs.parse('a[100]=b');
     	assert.deepEqual(withMaxIndex, { a: { '100': 'b' } });

    ```

*   `arrayLimit` 选项可以修改默认限制：

    ```
      	var withArrayLimit = qs.parse('a[1]=b', { arrayLimit: 0 });
      	assert.deepEqual(withArrayLimit, { a: { '1': 'b' } });

    ```

*   字符串不解析成数组，可以设置 `parseArrays` 为 `false`

    ```
      	var noParsingArrays = qs.parse('a[]=b', { parseArrays: false });
      	assert.deepEqual(noParsingArrays, { a: { '0': 'b' } });

    ```

*   如果混合使用两种格式，qs 会将字符串解析为对象：

    ```
      	var mixedNotation = qs.parse('a[0]=b&a[b]=c');
      	assert.deepEqual(mixedNotation, { a: { '0': 'b', b: 'c' } });

    ```

*   也可以创建元素为对象的数组：

    ```
      	var arraysOfObjects = qs.parse('a[][b]=c');
      	assert.deepEqual(arraysOfObjects, { a: [{ b: 'c' }] });

    ```

3.  序列化字符串

    ```
     qs.stringify(object, [options]);

    ```

*   默认情况下，对象序列化后进行URI编码后输出：

    ```
      	assert.equal(qs.stringify({ a: 'b' }), 'a=b');
      	assert.equal(qs.stringify({ a: { b: 'c' } }), 'a%5Bb%5D=c');

    ```

*   通过设置 `encode` 为 `false` 禁止 URI 编码：

    ```
      	var unencoded = qs.stringify({ a: { b: 'c' } }, { encode: false });
      	assert.equal(unencoded, 'a[b]=c');

    ```

*   通过设置 `encodeValuesOnly` 为 `true`，可以禁用对 key 进行URI 编码：

    ```
      	var encodedValues = qs.stringify(
      	    { a: 'b', c: ['d', 'e=f'], f: [['g'], ['h']] },
      	    { encodeValuesOnly: true }
      	);
      	assert.equal(encodedValues,'a=b&c[0]=d&c[1]=e%3Df&f[0][0]=g&f[1][0]=h');

    ```

    *   可以通过设置`encoder` 选项自定义编码方式（*注意：当 `encode` 被设置为 `false` 的时候，不适用*）：

        ```
          var encoded = qs.stringify({ a: { b: 'c' } }, { encoder: function (str) {
              // Passed in values `a`, `b`, `c`
              return // Return encoded string
          }})

        ```

*   与`encoder` 类似 `decoder` 可以用来解码：

    ```
      	var decoded = qs.parse('x=z', { decoder: function (str) {
      	    // Passed in values `x`, `z`
      	    return // Return decoded string
      	}})

      Examples beyond this point will be shown as though the output is not URI encoded for clarity. Please note that the return values in these cases will be URI encoded during real usage.

    ```

*   当数组被序列化时，默认显示索引：

    ```
      	qs.stringify({ a: ['b', 'c', 'd'] });
      	// 'a[0]=b&a[1]=c&a[2]=d'

    ```

*   可以通过设置 `indices` 为 `false` 不显示索引：

    ```
      	qs.stringify({ a: ['b', 'c', 'd'] }, { indices: false });
      	// 'a=b&a=c&a=d'

    ```

*   可以通过设置 `arrayFormat` 选项指定数组输出格式：

    ```
      	qs.stringify({ a: ['b', 'c'] }, { arrayFormat: 'indices' })
      	// 'a[0]=b&a[1]=c'
      	qs.stringify({ a: ['b', 'c'] }, { arrayFormat: 'brackets' })
      	// 'a[]=b&a[]=c'
      	qs.stringify({ a: ['b', 'c'] }, { arrayFormat: 'repeat' })
      	// 'a=b&a=c'

    ```

*   对象序列化时，默认使用 `[]` 表示法：

    ```
      	qs.stringify({ a: { b: { c: 'd', e: 'f' } } });
      	// 'a[b][c]=d&a[b][e]=f'

    ```

*   通过设置 `allowDots` 为 `true`修改为点表示法：

    ```
      	qs.stringify({ a: { b: { c: 'd', e: 'f' } } }, { allowDots: true });
      	// 'a.b.c=d&a.b.e=f'

    ```

*   空字符串和`null`值将被省略，但是`=`会保留：

    ```
      	assert.equal(qs.stringify({ a: '' }), 'a=');

    ```

*   没有值的键将什么也不返回（例如空对象或数组）：

    ```
      	assert.equal(qs.stringify({ a: [] }), '');
      	assert.equal(qs.stringify({ a: {} }), '');
      	assert.equal(qs.stringify({ a: [{}] }), '');
      	assert.equal(qs.stringify({ a: { b: []} }), '');
      	assert.equal(qs.stringify({ a: { b: {}} }), '');

    ```

*   值为 `undefined` 的属性将会被完全忽略：

    ```
      	assert.equal(qs.stringify({ a: null, b: undefined }), 'a=');

    ```

*   `addQueryPrefix` 设置为 `true`可以在查询字符串前面加 `?`：

    ```
      	assert.equal(qs.stringify({ a: 'b', c: 'd' }, { addQueryPrefix: true }), '?a=b&c=d');

    ```

*   分隔符也可以设置：

    ```
      	assert.equal(qs.stringify({ a: 'b', c: 'd' }, { delimiter: ';' }), 'a=b;c=d');

    ```

*   如果只是序列化日期对象，可以使用 `serializeDate` 选项：

    ```
      	var date = new Date(7);
      	assert.equal(qs.stringify({ a: date }), 'a=1970-01-01T00:00:00.007Z'.replace(/:/g, '%3A'));
      	assert.equal(
      	    qs.stringify({ a: date }, { serializeDate: function (d) { return d.getTime(); } }),
      	    'a=7'
      	);

    ```

*   可以使用 `sort` 选项来修改键的顺序：

    ```
      	function alphabeticalSort(a, b) {
      	    return a.localeCompare(b);
      	}
      	assert.equal(qs.stringify({ a: 'c', z: 'y', b : 'f' }, { sort: alphabeticalSort }), 'a=c&b=f&z=y');

    ```

*   最后，可以使用 `filter` 选项过滤序列化输出的键。如果给`filter`传递一个函数，每个键调用一次该函数并用返回的值替换原来值。如果给`filter`传递一个数组，它将用于选择对象的key和数组的索引：

    ```
      	function filterFunc(prefix, value) {
      	    if (prefix == 'b') {
      	        // Return an `undefined` value to omit a property.
      	        return;
      	    }
      	    if (prefix == 'e[f]') {
      	        return value.getTime();
      	    }
      	    if (prefix == 'e[g][0]') {
      	        return value * 2;
      	    }
      	    return value;
      	}
      	qs.stringify({ a: 'b', c: 'd', e: { f: new Date(123), g: [2] } }, { filter: filterFunc });
      	// 'a=b&c=d&e[f]=123&e[g][0]=4'
      	qs.stringify({ a: 'b', c: 'd', e: 'f' }, { filter: ['a', 'e'] });
      	// 'a=b&e=f'
      	qs.stringify({ a: ['b', 'c', 'd'], e: 'f' }, { filter: ['a', 0, 2] });
      	// 'a[0]=b&a[2]=d'

    ```

4.  处理 `null` 值

*   默认情况下，`null` 值被视为空对象：

    ```
     	var withNull = qs.stringify({ a: null, b: '' });
     	assert.equal(withNull, 'a=&b=');

    ```

*   解析字符串的时候并不会区分参数有没有等号，没有值的话都会解析为空字符串：

    ```
      	var equalsInsensitive = qs.parse('a&b=');
      	assert.deepEqual(equalsInsensitive, { a: '', b: '' });

    ```

*   要想区分空字符串和`null`值可以使用 `strictNullHandling` 选项，序列化后的 `null` 值没有`=`

    ```
      	var strictNull = qs.stringify({ a: null, b: '' }, { strictNullHandling: true });
      	assert.equal(strictNull, 'a&b=');

    ```

*   要解析不带 `=` 的值返回 `null`可以使用 `strictNullHandling` 选项：

    ```
      	var parsedStrictNull = qs.parse('a&b=', { strictNullHandling: true });
      	assert.deepEqual(parsedStrictNull, { a: null, b: '' });

    ```

*   想要完全跳过值为 `null` 的键不解析，可以使用 `skipNulls` 选项：

    ```
      	var nullsSkipped = qs.stringify({ a: 'b', c: null}, { skipNulls: true });
      	assert.equal(nullsSkipped, 'a=b');

    ```

5.  处理特殊字符集：

*   默认情况下，字符的编码和解码在utf-8中完成。 如果希望将查询字符串编码为不同的字符集（i.e.Shift JIS），您可以使用qs-iconv库：

    ```
      	var encoder = require('qs-iconv/encoder')('shift_jis');
      	var shiftJISEncoded = qs.stringify({ a: 'こんにちは！' }, { encoder: encoder });
      	assert.equal(shiftJISEncoded, 'a=%82%B1%82%F1%82%C9%82%BF%82%CD%81I');

    ```

*   这也适用于解码查询字符串：

    ```
      	var decoder = require('qs-iconv/decoder')('shift_jis');
      	var obj = qs.parse('a=%82%B1%82%F1%82%C9%82%BF%82%CD%81I', { decoder: decoder });
      	assert.deepEqual(obj, { a: 'こんにちは！' });

    ```

6.  RFC 3986 and RFC 1738 space encoding

    RFC3986 used as default option and encodes ’ ’ to %20 which is backward compatible. In the same time, output can be stringified as per RFC1738 with ’ ’ equal to ‘+’.

    ```
     	assert.equal(qs.stringify({ a: 'b c' }), 'a=b%20c');
     	assert.equal(qs.stringify({ a: 'b c' }, { format : 'RFC3986' }), 'a=b%20c');
     	assert.equal(qs.stringify({ a: 'b c' }, { format : 'RFC1738' }), 'a=b+c');
    ```
