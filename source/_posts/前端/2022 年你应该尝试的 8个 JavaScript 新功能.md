---
title: 2022 年你应该尝试的 8个 JavaScript 新功能🔥
date: 2022-01-18 16:28:03
category: javascript
---
>本文分享自华为云社区[《2022 年你应该尝试的 8个 JavaScript 新功能》](https://bbs.huaweicloud.com/blogs/325503?utm_source=juejin&utm_medium=bbs-ex&utm_campaign=other&utm_content=content)，作者：前端picker。

1995年12月4日，Netscape 公司与 Sun 公司联合发布JavaScript 以来，JavaScript从推出就开始了飞速的发展，2015年6，ES6正式发布，此后JavaScript正式进入新阶段，成为企业级大规模开发语言，并仍以高速度不断发展。

下面的表格对应这版本变化：

| 全称 | 发布年份 | 缩写 / 简称 |
| --- | --- | --- |
| ECMAScript 2015 | 2015 | ES2015 / ES6 |
| ECMAScript 2016 | 2016 | ES2016 / ES7 |
| ECMAScript 2017 | 2017 | ES2017 / ES8 |
| ECMAScript 2018 | 2018 | ES2018 / ES9 |
| ECMAScript 2019 | 2019 | ES2019 / ES10 |
| ECMAScript 2020 | 2020 | ES2020 / ES11 |
| ECMAScript 2021 | 2021 | ES2021 / ES12 |
| ECMAScript 2022 | 2022 | ES2022 / ES13 |

**本文主要介绍几个已经进入stage4的提案，这几个提案有望在2022年逐步纳入标准。**（请注意：纳入标准并不等同于浏览器支持）

PS：科普-Javascript的新语法，从提出到纳入标准一共经历下面几个stage

stage-0：新语法还是一个设想，（只能由TC39成员或TC39贡献者提出）

stage-1:：提案阶段，比较正式的提议，只能由TC39成员发起，这个提案要解决的问题必须有正式的书面描述。

stage-2：草案，有了初始规范，必须对功能语法和语义进行正式描述，包括一些实验性的实现。

stage-3：候选，该提议基本已经实现，需要等待实验验证，用户反馈及验收测试通过。

stage-4：已完成，必须通过 Test262 验收测试，下一步就纳入ECMA标准。

## .at()

TC39建议在所有**基本可索引类**，例如：数组、字符串、类数组（arguments）中添加.at（）方法。

例如

```
lat arr=[1,2,3,4,5]

```

之前我们想获取数组中的第二位

```
arr[1] //2

```

最后一位的话，可能就是

```
arr[4] // 5

```

但是如果arr长度是动态的呢？我们要如何让取出最后一位? 通常的写法是：

```
arr[arr.length-1]

```

但是有了.at()方法就很简单了，.at()支持正索引和负索引。

例如

```
arr.at(-1)  //5
arr.at(-2)  //4

```

具体提案：[https://github.com/tc39/proposal-relative-indexing-method](https://github.com/tc39/proposal-relative-indexing-method)

## Object.hasOwn(object, property)

Object.hasOwn(object, property)主要是用来替代Object.prototype.hasOwnProperty()。

目前我们想要判断对象是否具有指定的对象，主要写法如下：

```

if (Object.prototype.hasOwnProperty.call(object, "fn")) {
  console.log('有')
}

```

而Object.hasOwn的写法：

```
if (Object.hasOwn(object, "fn")) {
  console.log("有")
}

```

具体提案：[https://github.com/tc39/proposal-accessible-object-hasownproperty](https://github.com/tc39/proposal-accessible-object-hasownproperty)

目前来看，V8引擎的9.3版本已经开始支持，所以说chrome应该会很快支持。

## 类的私有方法和getter/setter

类是所有支持面向对象语言的基本，而Javascript虽然支持使用class定义类，但是并没有提供 定义私有属性/方法的的 方案。本提案提出使用**#**来定义私有属性/方法

```
class Person{
    name = '小芳';
    #age = 16;
    consoleAge(){
       console.log(this.#age)
    }
}
const person = new Person();
console.log(person.name); //小芳
console.log(button.#value); //报错
button.#value = false;//报错

```

具体提案：[https://github.com/tc39/proposal-private-methods](https://github.com/tc39/proposal-private-methods)

## 检查私有属性和方法

因为新的标准会支持私有属性和方法，当我们访问不存在的私有属性/方法会报错，而在新标准中也考虑了这个情况，提供了in来检查私有属性和方法是否存在

```
class C {
  #brand;

  #method() {}

  get #getter() {}

  static isC(obj) {
    return #brand in obj && #method in obj && #getter in obj;
  }
}

```

具体提案：[https://github.com/tc39/proposal-private-fields-in-in](https://github.com/tc39/proposal-private-fields-in-in)

## Top-level `await`（顶层await）

目前，我们使用await必须是在申明async的函数中，本提案，主要是支持在没有async的情况下使用await

例如下面几种使用场景：

### 动态引入依赖

```
const strings = await import(`/i18n/${navigator.language}`);

```

这允许模块使用运行时值来确定依赖关系。这对于开发/生产拆分、国际化、环境拆分等非常有用。

### 资源初始化

```
const connection = await dbConnector();

```

这允许模块表示资源，并在模块永远无法使用的情况下产生错误。

### 加载依赖

```
let jQuery;
try {
  jQuery = await import('https://cdn-a.com/jQuery');
} catch {
  jQuery = await import('https://cdn-b.com/jQuery');
}

```

具体提案：[https://github.com/tc39/proposal-top-level-await](https://github.com/tc39/proposal-top-level-await)

## 正则匹配索引

该提案提供了一个新的`/d`，用来获取每个匹配的开始位置和结束位置信息。

```
const str = 'The question is TO BE, or not to be, that is to be.';
const regex = /to be/gd;

const matches = [...str.matchAll(regex)];
matches[0];

```

![image-20220111232438895](https://upload-images.jianshu.io/upload_images/10024246-6569d667e41d8db0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

具体提案：[https://github.com/tc39/proposal-regexp-match-indices](https://github.com/tc39/proposal-regexp-match-indices)

## new Error()抛出异常的具体原因

new Error(),可能大家第一反应是，这不是已经存在的语法嘛，是的，没错！只是新的提案：将错误与原因相关联，，向具有属性的`Error()` 构造函数添加一个附加选项参数`cause`，其值将作为属性分配给错误实例。

```
async function doJob() {
  const rawResource = await fetch('//domain/resource-a')
    .catch(err => {
      throw new Error('Download raw resource failed', { cause: err });
    });
  const jobResult = doComputationalHeavyJob(rawResource);
  await fetch('//domain/upload', { method: 'POST', body: jobResult })
    .catch(err => {
      throw new Error('Upload job result failed', { cause: err });
    });
}

try {
  await doJob();
} catch (e) {
  console.log(e);
  console.log('Caused by', e.cause);
}
// Error: Upload job result failed
// Caused by TypeError: Failed to fetch

```

具体提案：[https://github.com/tc39/proposal-error-cause](https://github.com/tc39/proposal-error-cause)

## 类static初始化块

本田针对静态字段和静态私有字段的提供了一种在 ClassDefinitionEvaluation 期间执行类静态端的每个字段初始化的机制-static blocks.例如官方提供的例子：

在没有static blocks之前，我们想给静态变量初始化（非直接赋值，可能是表达式赋值）的话，可能是放在外部实现：

```
// without static blocks:
class C {
  static x = ...;
  static y;
  static z;
}

try {
  const obj = doSomethingWith(C.x);
  C.y = obj.y
  C.z = obj.z;
}
catch {
  C.y = ...;
  C.z = ...;
}

```

有了static block的情况下：我们可以直接在static blocks中初始化变量：

```
class C {
  static x = ...;
  static y;
  static z;
  static {
    try {
      const obj = doSomethingWith(this.x);
      this.y = obj.y;
      this.z = obj.z;
    }
    catch {
      this.y = ...;
      this.z = ...;
    }
  }
}

```
