---
title: 带你撸一遍JS隐式转换细则
date: 2021-06-10 16:28:03
categories: 前端
---
写在开篇之前：记录学习点滴，如有错误与补充，希望大家积极指正。

## js的数据类型

js数据类型：`null`，`undefined`，`String`，`Number`，`Boolean`，`Symbol`，`Object`，其中

原始类型：`Number`、`String`、`Null`、`Undefined`、`Boolean`

引用类型：`Object`,包含`Array`

JavaScript 是一种弱类型或者说动态语言。这意味着你不用提前声明变量的类型，在程序运行过程中，类型会被自动确定。这也意味着你可以使用同一个变量保存不同类型的数据。

## 常见的隐式转换场景

我们经常在开发过程中遇到如下场景：

*   `if`判断中

```
    if(XXX){
        ...
    }
复制代码
```

这里就发生了隐式转换，我们知道`if`的判断条件是`Boolean`类型，代码在执行到if判断时，js将XXX转换成了Boolean类型。

*   比较操作符 `"=="`

```
  [] == []  // false
  {} == {}  // false
  [] != []  // true
复制代码
```

看到这几个比较操作，如果比较懵的话，没关系，后面我会娓娓道来。

*   加号`"+"` 与 减号 `"-"`

```
var add = 1 + 2 + '3'
console.log(add);  //'33'

var minus = 3 - true
console.log(minus); //2

复制代码
```

可以看出有些情况下`+`用作`加号操作符`，有些时候`+`作为`字符串连接符`，`true`被转换为`Number`类型，值为1，相减后得到2。

*   `.`点号操作符

```
var a = 2;
console.log(a.toString()); // '2';

var b = 'zhang';
console.log(b.valueOf()); //'zhang';

复制代码
```

在对数字，字符串进行点操作调用方法时，默认将数字，字符串转成对象。

*   关系运算符比较时

```
 3 > 4  // false
"2" > 10  // false
"2" > "10"  // true

复制代码
```

如果比较运算符两边都是数字类型，则直接比较大小。如果是非数值进行比较时，则会将其转换为数字然后在比较，如果符号两侧的值都是字符串时，不会将其转换为数字进行比较，而是分别比较字符串中字符的Unicode编码。

> 在讨论之前我们需要先了解下 `valueOf()` 与 `toString` 方法的一些要点。

*   `toString()`和`valueOf()`都是对象的方法
*   `toString()`返回的是字符串，而`valueOf()`返回的是原对象
*   `undefined`和`null`没有`toString()`和`valueOf()`方法
*   包装对象的`valueOf()`方法返回该包装对象对应的原始值
*   使用`toString()`方法可以区分内置函数和自定义函数

大家没事可以自己实践下这两个方法的使用和区别，其余具体区别和特性不再详述~

那么接下来说说数据类型之间是如何转换的吧。

## String，Boolean，Number，对象之间的相互转换

#### 1\. 其他类型转为字符串类型

*   null：转为`"null"`。
*   undefined：转为`"undefined"`。
*   Boolean：`true`转为`"true"`，`false`转为`"false"`。
*   Number：`11`转为`"11"`,科学计数法`11e20`转为`"1.1e+21"`。
*   数组：空数组`[]`转为空字符串`""`，如果数组中的元素有`null`或者`undefined`,同样当做空字符串处理，`[1,2,3,4]`转为`"1,2,3,4"`，相当于调用数组的join方法，将各元素用逗号`","`拼接起来。
*   函数：`function a(){}`转为字符串是`"function a(){}"`。
*   一般对象：相当于调用对象的toString()方法，返回的是`"[object,object]"`。

```

    String(null)  // "null"
    String(undefined) // "undefined"
    String(true)  // "true"
    String(false)  // "false"
    String(11)  // "11"
    String(11e20)  // "1.1e+21"
    String([])  // ""
    String([1,null,2,undefined,3])  // 1,,2,,3
    String(function a(){})  // "function a(){}"
    String({})  // "[object,object]"
    String({name:'zhang'})  // "[object,object]"

复制代码
```

#### 2\. 其他类型转为Boolean类型

> 只有`null`，`undefined`，`0`，`false`，`NaN`，`空字符串`这6种情况转为布尔值结果为`false`，其余全部为`true`，例子如下

```

    Boolean(null)  // false
    Boolean(undefined)  // false
    Boolean(0)  // false
    Boolean(false)  // false
    Boolean("false")  // true
    Boolean(NaN)  // false
    Boolean("")  // false
    Boolean([])  // true
    Boolean({})  // true

复制代码
```

#### 3\. 其他类型转为Number类型

*   null：转为 `0`。
*   undefined：转为`NaN`。
*   Boolean：`true`转为`1`，`false`转为`0`。
*   字符串：如果是纯数字的字符串，则转为对应的数字，如`11`转为`"11"`，`"1.1e+21"`转为`1.1e+21`，`空字符串`转为`0`，其余情况则为`NaN`。
*   数组：数组首先会被转换成原始类型，即`primitive value`，得到原始类型后再根据上面的转换规则转换。
*   对象：和数组一样

```
Number(null)  // 0
Number(undefined)  //NaN
Number(true)  //1
Number(false)  //0
Number("11")  //11
Number("1.1e+21") //1.1e+21
Number("abc")  //NaN
Number([])   // 0
Number([0])  // 0
Number([1])  // 1
Number(["abc"])  NaN
Number({})  // NaN

复制代码
```

#### 4\. 对象转为其他类型(原始类型)

*   当对象转为其他原始类型时，会先调用对象的`valueOf()`方法，如果`valueOf()`方法返回的是原始类型，则直接返回这个原始类型
*   如果`valueOf()`方法返回的是不是原始类型或者`valueOf()方法`不存在，则继续调用对象的`toString()`方法，如果`toString()`方法返回的是原始类型，则直接返回这个原始类型,如果不是原始类型，则直接报错抛出异常。

> 注意：对于不同类型的对象来说，转为原始类型的规则有所不同，比如`Date`对象会先调用`toString`

```
    var o1 = {
        valueOf(){
            return 1
        }
    }
    var o2 = {
        toString(){
            return 2
        }
    }
    var o3 = {
        valueOf(){
            return {}
        },
        toString(){
            return 3
        }
    }

    Number(o1)  //1
    Number(o2)  //2
    Number(o3)  //3

复制代码
```

`o1`的`valueOf`方法返回原始类型`1`，故结果为`1`

`o2`没有`valueOf`方法，调用`toString`方法得到原始类型`2`，故结果为`2`

`o3`既有`valueOf`方法，也有`toString`方法，先调用`valueOf`方法返回的不是原始类型，故继续调用`toString`方法，返回的是原始类型`3`，故结果为`3`

我们再看一个例子

![](https://upload-images.jianshu.io/upload_images/10024246-f053a4864324a4e2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


将一个`空数组`转成`Number`类型，`空数组`是一个对象，先调用`valueOf()`得到`[]`，不是原始类型，继续调用`toString()`方法，得到的是一个`空字符串`，根据`Number`的转换规则，`空字符串`转换成`数字`为`0`,故`Number([])`为`0`

## 宽松相等（==）的隐式转换

我们知道宽松相等是存在隐式转换的，相比较时只要两边的值相等，不需要类型相等，就可以得到`true`，那么看看有哪些情况的比较吧

### 原始类型之间相比较

#### 1\. 字符串类型与数字类型相比较

*   当字符串类型与数字类型相比较时，`字符串类型`会被转换为`数字类型`
*   当字符串是由纯数字组成的字符串时，转换成对应的数字，字符串为空时转换为`0`，其余的都转换为`NaN`。 小例子可如下：

```

    "1" == 1  //true
    "" == 0  //true
    "1.1e+21" == 1.1e+21  //true
    "Infinity" == Infinity  //true
    NaN == NaN  //false  因为NaN与任何值都不相等，包括自己

复制代码
```

#### 2\. 布尔类型与其他类型相比较

*   只要`布尔类型`参与比较，该`布尔类型`就会率先被转成`数字类型`
*   布尔类型`true`转为`1`，`false`转为``0

小例子可如下：

```
true == 1  // true
false == 0  // true
true == 2  // false
"" == false // true
"1" == true // true
复制代码
```

根据规则，布尔型参与比较，会把布尔类型转为数字类型。
- 第一个demo，`true`被转为数字类型`1`，比较变为`1 == 1`,结果为`true`。
- 第二个demo，`false`被转为数字类型`0`，比较变为`0 == 0`，结果为`true`。
- 第三个demo，`true`被转为数字类型`1`，，比较变为`1 == 2`，结果为`false`。
- 第四个demo，`false`被转为数字类型`0`，比较变为`"" == 0`，根据字符串与数字相比较，会率先把字符串变成数字，空字符串转为数字类型为0，比较变为`0 == 0`，故结果为`true`。
- 第五个demo与第四个相似，`true`被转换成数字类型1，比较变为`"1" == 1`，根据字符串与数字相比较，会率先把字符串变成数字，字符串`"1"`转为数字类型为1，变成`1 == 1`，故结果为`true`。

#### `null`类型和 `undefined`类型与其他类型相比较

首先得知道`null`与`undefined`的定义

`null`: 代表“空值”，代表一个空对象指针,使用`typeof`运算得到 `"object"`，所以可以认为它是一个特殊的对象值。

`undefined`:声明了一个变量但并未为该变量赋值，此变量的值默认为undefined。

这里多啰嗦一部分内容，`null`与`undefined`分别应用的场景:

##### `null`的场景：

*   作为原型链的终点
*   作为函数的参数，表示该函数的参数不是对象

##### `undefined`的场景：

*   声明了一个变量但并未为该变量赋值，此变量的值默认为`undefined`
*   函数没有明确写`return`，默认返回`undefined`
*   调用函数时，没有传参数，默认参数值为`undefined`
*   对象的某个属性没有赋值，默认值为`undefined`

Javascript规定`null`与`undefined`宽松相等(`==`)，并且都与自身相等，但是与其他所有值都不宽松相等。

```
null == null   //true
undefined == undefined  //true
null == undefined  //true
null == 0  //false
null == false  //false
undefined == 0  //false
undefined == false  //false
null == []  //false
null == {}  //false
unfefined == []  //false
undefined == {}  //false
复制代码
```

### 对象与原始类型的相比较

> `对象`与`原始类型`相比较时，会把`对象`按照`对象转换规则`转换成`原始类型`，再比较。

小例子如下：

```

 {} == 0  // false
 {} == '[object object]'  // true
 [] == false  // true
 [1,2,3] == '1,2,3' // true

复制代码
```

先看一下,根据对象转换规则，`{}`,`[]`,`[1,2,3]`转为原始类型后的结果如下:

![](https://upload-images.jianshu.io/upload_images/10024246-b7f8fb23dd3aba4d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


第一个例子,根据上图可知，{}的原始值为`"[object object]"`，比较变成`"[object object]" == 0`，接着根据字符串与数字类型相比较规则，先将字符串转换成数字类型，可知`[object object]`转为数字为`NaN`，比较变成`NaN == 0`，因为`NaN`与任何值都不想等，故结果为`false`。

第二个例子，由第一个分析可知，{}的原始值为`"[object object]"`，比较变成`"[object object]" =="[object object]"`,故结果为`true`。

第三个例子,根据上图可知，`[]`的原始值为空字符串`""`，比较变成`"" == 0`，接着根据字符串与数字类型相比较规则，先将字符串转换成数字类型，可知`""`转为数字为`0`，比较变成`0 == 0`，故结果为`true`。

第四个例子，根据数组转换成原始类型的规则可知，数组的原始类型结果是由数组各个元素由逗号进行分割组成的字符串，故比较变成`"1,2,3" == "1,2,3"`,所以结果为`true`。

### 对象与对象相比较

首先先说一下`基本类型`(原始类型)与`引用类型`的存储。

> `基本类型`：是指存放在`栈内存`中的简单数据段，数据大小确定，内存空间大小可以分配，它们是直接按值存放的，所以可以直接按值访问。

> `引用类型`：是指存放在`堆内存`中的对象，变量名保存在`栈内存`里，而对应的值保存在`堆内存`里，这个变量在`栈内存`中实际保存的是：`这个值在堆内存中的地址`，也就是`指针`，指针指向堆内存中的地址。

那么对象相比较的规则就出来了:

> 如果两个对象指向同一个对象，相等操作符返回 `true`，否则为`false`。

```
var a = {};
var b = {};
a == b  // false

var c = [];
var d = [];
c == d  // false
复制代码
```

虽然 a 和 b 都保存了一个 Object，但这是两个独立的 Object，它们的地址是不同的。c与d也是如此。 所以 `[] == []` 为`false`，`{} == {}` 也是`false`。 那么改成如下呢？

```
var a = {};
var b = a;
a == b;
复制代码
```

变量b保存的是a的指针，指向同一个对象，所以a == b。

那么接下来看下面的例子

```
[] == ![]  // true
{} == !{}  // false
复制代码
```

Javascript规定，`逻辑非 (!)` 的优先级高于`相等操作符 ( == )`

再则`取非`的含义时什么呢？

`取非`：首先通过`Boolean()`函数将它的操作值转换为`布尔值`，然后`求反`。

第一个例子，先看`![]`，也就是对空数组`[]`取非，根据取非定义，先执行`Boolean([])`,我们知道只有`null`，`undefined`，`false`，`0`，`""`，`NaN`，执行`Boolean()`函数时结果才为`false`，取余全为`true`，故`Boolean([])`结果为`true`，取非得到`false`，比较变为`[] == false`, 这下变成了`对象类型`与`布尔类型`相比较了，这就比较简单了，根据前面的对象类型比较规则，布尔类型比较规则，很容易的出比较变为`"" == 0`，再根据字符串与数字类型相比较规则，比较变为`0 == 0`，显然结果为`true`。

第二个例子，同第一个例子一样，先执行`Boolean({})`得到`true`,再取反得到`false`，比较变为`{} == false`,现在比较变为`对象类型`与`布尔类型`相比较了，根据之前写的例子知道，先指向{}的valueOf()方法得到的不是原始类型，继续执行{}的toString()方法到的结果为`"[object object]"`，比较变为`"[object object]" == 0`,再根据字符串与数字类型相比较规则,`"[object object]"`转为数字类型为`NaN`，比较变成`NaN == 0`,`NaN`不与任何值相等，显然结果为`false`。

之前比较火的一个面试题：怎么定义`a`，可以使`a==1&&a==2&&a==3`？结合对象隐式转换规则，试一试吧！

### 字符串连接符（+）与加号运算符（+）如何区分

以下情况可视为`字符串连接符`

*   含有`+`两边的数据，任意一个为字符串
*   含有`+`两边的数据，其中一边为对象，并且取得的原始值为字符串

以上2种情况都视为字符串连接符，将自动的对不是字符串的数据执行String()方法转为字符串

以下情况可视为加号运算符

*   加号两边都为数字类型
*   加号两边都是基本类型，除了`字符串类型`，则可视为`加号`。也就是说加号两边可以为`Boolean`，`Number`，`null`，`undefined`这种情况，肯定是加号运算符。将对不是`Numebr`类型的数据执行`Number()`方法再相加。
*   其中一边是基本类型，字符串除外，另一边是对象，并且对象获得的原始值不是字符串。

另外值得一提的是： `NaN`与任何数相加都为`NaN`,

那么接下来看下面的例子

```
"a" + "b"  // "ab"
"a" + 1    // "a1"
"a" + {}   // "a[object object]"
"a" + []   // "a"
"a" + true  // "atrue"
"a" + null  // "anull"
"a" + undefined // "aundefined"

 1 + 2     // 3
 1 + true  // 2
 1 + null  // 1
 true + true   // 2
 true + false  // 1
 true + null   // 1
 false + null  // 0

 var c = {
     valueOf(){
         return 1;
     }
 }

 1 + undefined   // NaN
 true + undefined  // NaN
 true + c   // 2
 1 + c   // 2

 NaN + 1  // NaN
 NaN + null  // NaN

 [] + {}  // "[object object]"
 {} + []  // 0
复制代码
```

这里值得提出来的是`Number(undefined)`为`NaN`，不难看出上面几个的结果为啥等于`NaN`。 那么`[] + {}`与`{} + []`只是调了一个位置，为什么结果会大不相同呢？

我们知道`[]`的原始值为`""`，`{}`的原始值为`[object object]`，在这里就不再重述了。 那么第一个例子，拼接起来等于`"[object object]"`没什么问题，那么第二个例子是为什么呢？

原来是`js解释器`会将开头的 `{}` 看作一个代码块，而不是一个`js对`象，于是运算可写成`+[]`，这结果可不就是`0`嘛，上代码看一下

[图片上传失败...(image-bbb4d8-1623944329379)]

<figcaption></figcaption>

大家可以自己自己操作看看~~~

好了，大致就是这么多了，也是看了很多文章和结合高程写出来的，撸了一遍，着实加深了印象，给自己点个赞，嘻嘻嘻~~~

>作者：Autumn秋田
链接：https://juejin.cn/post/6844903934876745735
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
