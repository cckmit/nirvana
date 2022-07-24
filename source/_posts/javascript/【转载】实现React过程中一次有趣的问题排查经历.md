---
title: 【转载】实现React过程中一次有趣的问题排查经历
date: 2022-07-16 14:27:21
categories: 前端
tags: [react]
---
## 问题现象

以下是这个用例的内容：

```
it('uses the fallback value when in an environment without Symbol', () => {
        expect((<div />).$typeof).toBe(0xeac7);
    });
```

他测试的是在**不支持Symbol的环境**，`jsx`的内部属性`$$typeof`是否正确。

我们知道，`jsx`仅仅是`JS`的语法糖，在编译时会被编译成函数调用，比如：

```
// 编译前
<div />

// 编译后 React17之前
React.createElement('div');

// 编译后 React17之后
jsxRuntime.jsx('div');
```

在`React.createElement`（或`jsxRuntime.jsx`）方法的实现中，最终会返回如下数据结构：

```
const element: ReactElement = {
  $typeof: REACT_ELEMENT_TYPE,
  type,
  key,
  ref,
  props
};
```

其中`$$typeof`属性用于区分**jsx对象的类型**，比如`REACT_ELEMENT_TYPE`代表这个`jsx对象`是一个`React Element`。

在支持`Symbol`的环境，`$$typeof`对应一个唯一的`symbol`。在不支持的环境，对应一个16进制数字。

比如`REACT_ELEMENT_TYPE`的定义如下：

```
const supportSymbol = typeof Symbol === 'function' && Symbol.for;

export const REACT_ELEMENT_TYPE = supportSymbol
    ? Symbol.for('react.element')
    : 0xeac7;
```

回到我们的测试用例，他的测试意图就很明显了：在不支持`Symbol`的环境，**div对应jsx对象**的`$$typeof`属性应该返回数字`0xeac7`。

```
it('uses the fallback value when in an environment without Symbol', () => {
        expect((<div />).$typeof).toBe(0xeac7);
    });
```

那么如何制造一个**不支持Symbol的环境**呢？

很简单，在所有用例执行前的`beforeEach`钩子函数（`jest`提供的）中将`global.Symbol`置为`undefined`：

```
beforeEach(() => {
        jest.resetModules();

        originalSymbol = global.Symbol;
    // 制造不支持Symbol的环境
        global.Symbol = undefined;

        React = require('react');
        ReactDOM = require('react-dom');
        ReactTestUtils = require('react-dom/test-utils');
    });
```

当引入`react`、`react-dom`时，其内部执行时`global.Symbol === undefined`。

这就模拟了**不支持Symbol的环境**。

但是这个用例却挂了：

[图片上传失败...(image-d22e3d-1657982127417)]

上述代码应该是没问题的，毕竟是`React`官方会跑的用例。那么问题出在哪儿呢？

## babel的锅

在`React17`发布时，带来了[全新的 JSX 转换](https://link.segmentfault.com/?enc=T8GcnH9w%2BFTbGm3psyiTyA%3D%3D.M6NMoXIbra2boJtX%2Bv9eE6bEBKp2povGHRTOifSMkPXZN5cLCAjdZR0vsHUej5yggDLCHpAPgAbBtD3P9qdDrj6jGmYonLyeMCSeR3PAq3Iglmi%2B9aPctofzMI5CW8oJ "全新的 JSX 转换")。

在17之前，`jsx`会编译为`React.createElement`，17之后会编译为`jsxRuntime.jsx`。

同时会在模块顶部引入如下语句：

```
import { jsx as _jsx } from "react/jsx-runtime";
import { jsxs as _jsxs } from "react/jsx-runtime";
```

上述被引入的语句的执行先于下述语句：

```
originalSymbol = global.Symbol;
global.Symbol = undefined;
```

所以在语句执行时，环境中还存在`global.Symbol`，就造成开篇提到的问题。

那为什么`React`官方跑用例时没有问题呢？

答案是：`React`跑用例时会将`jsx`编译为`React.createElement`。

这样不会在模块顶部插入新的引入语句。

当引入`React`时，环境中已经不存在`global.Symbol`了：

```
originalSymbol = global.Symbol;
global.Symbol = undefined;

React = require('react');
ReactDOM = require('react-dom');
ReactTestUtils = require('react-dom/test-utils');
```

## 总结

由于编译在内存中进行，不太好排查编译后代码。所以如果对`React`各方面特性了解不深的话，这个问题真不太好排查。

当前[big-react](https://link.segmentfault.com/?enc=FjTDIf5%2B2pvkMZmOy2oOwQ%3D%3D.yR6aUf8acsF2ZiM5gteDPY5URkjzSqlinxn%2BFKXMo1tBS9hKnyyyRbe%2FdFuYTdk3 "big-react")代码量还比较少，对从0实现`React`感兴趣的朋友可以关注下，给个`star`哦～
>原文：https://segmentfault.com/a/1190000042103304
