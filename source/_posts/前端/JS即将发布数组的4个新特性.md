---
title: JS即将发布数组的4个新特性
date: 2022-01-28 16:28:03
category: javascript
---

>新特性总览：
at(): 数组支持索引查询
Array Group: 数组元素分类；
Array find from last: 数组逆向查询
Resizable and growable Array Buffer: Array Buffer支持大小伸缩

## at()

>数组支持索引查询

过去我们在使用`[]`语法时，会以为数组和字符串支持按照索引去查询元素。比如：
```
const arr = [1,2,3,4,5];
console.log(arr[4])  // 5
```
这样的使用确实会让我们产生误导，以为`arr[4]`是查询数组第四个元素，但其实试一下`arr[-1]`就发现取到的是undefined。在EMCAScript里，`[]`语法最初被设计是适用于所有对象，比如`arr[1]`，实际上只是引用了键为 “1” 的对象的属性，这是任何对象都可以拥有的。所以`arr[-1]`这样的代码书写看似有效，但它返回对象的 “-1” 属性的值.
```
const arr = [1,2,3,4,5];
console.log(arr[-1])  // undefined
```
at()提案就是解决数组、字符串、TypedArray不能直接通过索引查询。该提案目前进入satge4，被各大浏览器实现。可以复制下面的代码直接在浏览器尝试。
```
const arr = [1,2,3,4,5];
console.log(arr.at(-2))  // 4
```
评价： 过去代码中`arr[arr.length-1]`的使用，会让初学者或者其他语言的开发者感觉古怪，`arr.at(index)`则显然更加语义话，是个不错的提案。
### Array Group

>用于数组元素分类

给数组Array的原型上添加了两个方法，`groupBy`和`groupByToMap`。内容比较简单，直接看使用案例就可以看懂，这里不再赘述。

- groupBy

使用案例：
将数组中的元素，按照数字 ‘40’ 来进行分类
```
let array = [23, 56, 78, 42, 11, 49]
array.groupBy((item,index) => {
    return item > 40 ? '比40大' : "比40小"
})
// {'比40大': [56, 78, 42, 49] , '比40小': [23,11]}
```
`groupBy`方法返回了一个新的匿名对象，其中对象的键key为`groupBy`的回调函数的返回值。
如果没有groupBy提案，我们过去是这么实现：
```
Array.prototype.groupBy = function(callback) {
    const object = {};
    for(let i =0; i < this.length; i++) {
        let key = callback(this[i],i,this);
        if(object[key]) {
            object[key].push(this[i])
        } else {
            object[key] = [this[i]]
        }
    }
    return object;
}
```

- groupByToMap

`groupByToMap`方法返回了一个常规的Map，其中Map的键key为`groupBy`的回调函数的返回值。
使用案例：
```
const array = [1, 2, 3, 4, 5];
const odd  = { odd: true };
const even = { even: true };
array.groupByToMap((num, index, array) => {
  return num % 2 === 0 ? even: odd;
});

// =>  Map { {odd: true}: [1, 3, 5], {even: true}: [2, 4] }
复制代码
如果没有groupByToMap提案，我们过去是这么实现：
Array.prototype.groupByToMap = function(callback) {
    const mapObject = new Map();
    for(let i =0; i < this.length; i++) {
        let key = callback(this[i],i,this);
        if(mapObject.get(key)) {
            mapObject.get(key).push(this[i])
        } else {
            mapObject.set(key,[this[i]])
        }
    }
    return mapObject;
}
```
评价：实际开发中，类似这样的数组` [ 1 ,  2 ,  3 ,  4 ,  5 ]`，如果要分奇数偶数的话，需要重新声明一个对象来存储。
`const obj = { odd: [1, 3, 5], even: [2, 4] }`

而`groupBy`提案返回的是一个null-prototype对象，`{ odd: [1, 3, 5], even: [2, 4] },`不需要再声明一个具名对象。`groupByToMap`返回的是一个常规Map对象，总体来说比较实用。
### Array find from last

>从数组的最后一个到第一个查找元素的方法

使用案例：
按照以前的js写法，要想倒叙查询数组元素。
先进行一次反转`reverse`，反转一次数组，再使用find进行查询。
```
const array = [{ value: 1 }, { value: 2 }, { value: 3 }, { value: 4 }];

[...array].reverse().find(n => n.value % 2 === 1); 
// { value: 3 }
```
或者在原型上挂一个方法来实现：
```
Array.prototype.findLast = function(callback) {
    const len = this.length;
    for(let i = len - 1; i > 0; i--) {
        if(callback(this[i],i,this)) {
            return this[i]
        }
    }
    return -1
}
```
`Array find from last `提案方案：
通过提案提供的方法`findLast`，支持了直接逆向查询数组。
```
array.findLast(n => n.value % 2 === 1); // { value: 3 }
```
评价：从性能看，减少了一次数组反转，确实有看的见的优化。从语义上来说，有正向查询就应该有反向查询，符合人们的思维习惯，总体来说比较实用。
###    Resizable and growable Array Buffer

>扩展ArrayBuffer构造函数，采用额外的最大长度，以允许就地增长和缩小缓冲区。

在介绍提案之前，需要提前了解下什么是`ArrayBuffer`，它是解决什么问题的，怎么用，在哪里用。这样会方便理解该提案。以下介绍`ArrayBuffer的`内容我已经过滤了很多细枝末节的理论，希望能做到之前没有这方面基础的同学也可以一次性读懂ArrayBuffer。
`Array Buffer`这个概念，大家在日常开发中几乎不会直接接触这个概念，但和我们关系却很密切。
简单理解`Array Buffer`，可以说它让`EMCAScript`操作内存空间成为可能。它的诞生是因为一个项目，`WebGL`，这个项目里需要浏览器与显卡进行频繁的通信，而以往的文本传输，需要把文本'hello wrold'，翻译01010111...类似这样的机器语言，非常消耗性能，而直接以二进制的形式通信就非常有必要了，Array Buffer也就此诞生；
 `const buffer = new ArrayBuffer(32);`
这样创建了一段32字节的内存空间，buffer。有一些约定好的特性，比如生成的ArrayBuffer，不能被直接修改，需要通过
TypedArray视图和DataView视图修改，TypedArray就是一系列更改内存的构造函数统称，比如Uint8Array，Uint16Array，Uint32Array，DataView是更灵活的操作ArrayBuffer的视图。大概是因为最开始设计是为了解决图像绘制的问题，所以就起名叫各种视图，记住就ok了。
以下是`TypedArray`视图支持的一些数据类型

|数据类型	|字节长度|	含义|	对应的 C 语言类型|
|--|--|--|--|
|Int8	1|	8 |位带符号整数	|signed char|
|Int16|	2	|16 位带符号整数|	short|
|Int32|	4|	32 位带符号整数|	int|
```
//生成12字节的内存空间
const buffer = new ArrayBuffer(12);
//用Int32Array视图读取这个内存空间
const x1 = new Int32Array(buffer);
//直接修改
x1[0] = 1;//1
//用Int8Array视图读取这个内存空间
const x2 = new Uint8Array(buffer);
x2[0]  = 2;//2
//x1的值发生变化
x1[0] // 2
```
上面代码对同一段内存，分别建立两种视图：32 位带符号整数（`Int32Array`构造函数）和 8 位不带符号整数（`Uint8Array`构造函数）。由于两个视图对应的是同一段内存，一个视图修改底层内存，会影响到另一个视图。
- `ArrayBuffer` 的一些理解
`Array Buffer`是一个二进制数组，也可以说是一个构造函数，但本质上是一个由一堆 0 1 组成的缓存空间，他在内存中主要发挥数据缓冲的作用。简单理解就是计算机要运行，就是cpu要处理 0 1 组成的二进制数据，但有时二进制数据来的又快又多，cpu还在处理其他任务，那这些数据就得原地等待，如果cpu处理任务很快，但是数据传输的慢了，就得让cpu空闲等待数据，造成资源浪费，ArrayBuffer就是充当一个缓冲内存空间。让这个内存空间随时数据存在，不会让数据和cpu等待。
- `ArrayBuffer` 遇到的瓶颈
现有的ArrayBuffer存在一个问题，当你正在的`ArrayBuffer`空间不够需要增加空间时，需要调用`ArrayBuffer`原型上的一个方法，`ArrayBuffer.prototype.slice()`，使用如下
```
const buffer = new ArrayBuffer(8);
const newBuffer = buffer.slice(0, 3);
```
上面代码拷贝buffer对象的前 3 个字节（从 0 开始，到第 3 个字节前面结束），生成一个新的ArrayBuffer对象。
slice方法其实包含两步，第一步是先分配一段新内存，第二步是将原来那个`ArrayBuffer`对象拷贝过去。
这样的方式一次使用尚且可以，但在一些绘图场景下，频繁地创建新内存，再复制一份过去，会大量损耗性能。
- 改造 ArrayBuffer
以上就介绍完了`ArrayBuffer`，和它目前面临的瓶颈。下面讲一下本次进入stage3的提案。
本提案拓展了ArrayBuffer的构造函数，增加最大和最小伸缩长度的配置---maximumByteLength。
重写了transfer方法，可以用来直接转移内存空间，不必再复制一份内存再挪动。
改造之后的ArrayBuffer构造函数
```
class ArrayBuffer {
    //options为配置项 
    //maximumByteLength 配置内存伸缩长度
    constructor(byteLength,[ options ]);
    
    //挪动内存
    transfer(newByteLength);
    
    //更改内存长度
    resize(newByteLength);
    
    //分离出一个不可调整大小的ArrayBuffer
    slice(start, end);
    
    //判断是否可以伸缩
    resizable();
    
    //获取配置的最大内存长度
    maximumByteLength();
    
    //获取当前内存长度
    byteLength();
}
```
使用案例：
声明一个1024字节的内存，并把最大内存长度设置为1024的平方
```
//增加配置项，maximumByteLength，最大内存长度允许到1024的平方
let rab = new ArrayBuffer(1024, { maximumByteLength: 1024 ** 2 });

//当前内存长度 1024字节
assert(rab.byteLength === 1024);

//最大长度 1024平方字节
assert(rab.maximumByteLength === 1024 ** 2);

//可以就地更改长度
assert(rab.resizable);

//更改内存长度
rab.resize(rab.byteLength * 2);

//内存长度发生变化
assert(rab.byteLength === 1024 * 2);
```
`transfer`是对内存进行挪动
`let ab = rab.transfer(1024);`

上面是挪动从0开始的1024字节
```
assert(rab.byteLength === 0);
assert(rab.maximumByteLength === 0);
```
上面是原来的内存（rab）被分离，并清除内存。
```
// 内存被转移到ab
assert(!ab.resizable);
assert(ab.byteLength === 1024);
```
内存成功被转移到ab
#### SharedArrayBuffer

>`SharedArrayBuffer `顾名思义就是可以共享的`ArrayBuffer`

`ArrayBuffer`提案里，将`SharedArrayBuffer`也进行了改动，让他支持可以增长内存。
先简单介绍下什么是`SharedArrayBuffer`。
在EMCAScript里有一些计算任务通常会让在worker线程里，每个worker线程都是互相隔离的，他们之间的通信需要通过postMessage来完成。
除了可以进行字符串数外，也可以进行二进制数据通信，但需要将二进制数据复制一份再用过postMessage传递给另一个线程，数据量很大时效率会变低，所以在 es2017 时有人提出了可以共享的SharedArrayBuffer，js的主线程和worker线程可以共享这块内存空间，同时进行数据读写，避免了复制二进制数据带来的性能损耗。
实际场景里，在主线程创建了一个SharedArrayBuffer,并把内存地址通过postMessage发给worker线程
```
// 主线程
// 新建 1KB 共享内存
const sharedBuffer = new SharedArrayBuffer(1024);
// 主线程将共享内存的地址发送出去
w.postMessage(sharedBuffer);
复制代码
worker线程接受来自主线程的共享地址 ---- SharedArrayBuffer，不必再复制同一份二进制地址进行传递。通过共享地址提高了性能。
// Worker 线程
onmessage = function (ev) {
  // 主线程共享的数据，就是 1KB 的共享内存
  const sharedBuffer = ev.data;
  // 在共享内存上建立视图，方便读写
  const sharedArray = new Int32Array(sharedBuffer);
  // ...
};
```
ArrayBuffer提案对`SharedArrayBuffe`r也进行了拓展，允许`SharedArrayBuffer`支持增加内存，但与ArrayBuffer不同的是，不支持减少内存，很显然，如果减少内存的话，很有可能影响到正在共享这块内存的其他程序。
下面是`SharedArrayBuffer`的构造函数。在`ArrayBuffer`构造函数里的resize，这里命名是`grow`，也能体现出两者的差异了。
```
class SharedArrayBuffer {
    //和ArrayBuffer一样配置maximumByteLength
    constructor(byteLength, [ options ]);
    
    //只能增加内存，而不能缩短内存
    grow(newByteLength);
    
    //分离出一份不可增长的 SharedArrayBuffer。
    slice(start, end);

    //判断是否可以扩充内存
    growable();
    
    //获取最大内存长度
    maximumByteLength();
    
    //获取当前内存长度
    byteLength();
  }
```
目前 `ArrayBuffer `提案遇到一个阻碍，真实场景中，可能存在多个线程需要扩大内存，按照现在的设计是需要先占用内存，再扩大或缩小至目标内存，但真实场景里，可能存在内存不够的情况，那么会造成资源抢夺。所以如何分配内存资源的问题仍需解决。

>作者：奇舞精选
链接：https://juejin.cn/post/7047372765036281869