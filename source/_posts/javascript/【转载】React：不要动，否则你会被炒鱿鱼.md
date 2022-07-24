---
title: 【转载】React：不要动，否则你会被炒鱿鱼
date: 2022-07-11 14:27:21
categories: 前端
tags: [react]
---
大家好，我卡颂。

不知道大家在用`React`开发时，有没有注意到`react`与`react-dom`这两个包中有个很奇葩的属性`__SECRET_INTERNALS_DO_NOT_USE_OR_YOU_WILL_BE_FIRED`：

![image.png](https://upload-images.jianshu.io/upload_images/10024246-a27b278d6b99300b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

直译过来就是**内部神秘属性，不要乱用！否则你会被炒鱿鱼**。

为什么会有个这么唬人的属性？今天我们来聊聊。

## React项目架构

我们在项目中习惯使用如下语句引入`Hook`：

```
import {useState} from 'react';
```

这是不是意味着所有`Hook`的具体实现都在`react`这个包中？实际不是的。

所有`Hook`的具体实现在`ReactFiberHooks.new.js`方法中，该方法来自于`react-reconciler`这个包。

那为什么我们项目中从来没有主动引入过这个包呢？因为`react-reconciler`中被使用的部分，被打包进`react-dom`中了。

简单来说，`React`为了实现跨平台渲染，采用的是**一个主模块** + **一个渲染器**的模式。

其中**主模块**就是`react`包，他提供了所有通用方法。

**渲染器**针对宿主环境不同而不同，比如：

*   浏览器环境使用`ReactDOM/client`渲染器
*   `SSR`使用`ReactDOM/server`渲染器
*   `Native`环境使用`ReactNative`渲染器

渲染器除了**宿主环境相关的代码**外，还有大量通用逻辑（比如`Diff`算法）。

所以可以认为，`react-dom`是由如下多个包中**被使用的部分**打包而成：

*   `shared`，一个存放通用方法的包
*   `react-reconciler`，提供包括`Hooks`的实现、`Diff`算法、优先级调度等更新相关功能
*   `react-dom`，提供宿主环境方法，比如**DOM的增/移动/删/改**
*   等等其他包

这也是为什么宿主环境千差万别，但都能通过执行`useState`改变状态，触发视图更新。

原因在于 —— **Hooks的实现**与**宿主环境操作视图的方法**被打包进了同一个包中。

既然**Hooks的实现**被打包进`react-dom`（或其他宿主环境对应的包）中，那如何做到最终使用时是从`react`中导出的呢？就像这样：

```
// 而不是 from 'react-dom'
import {useState} from 'react';
```

这就用到了开篇提到的变量`__SECRET_INTERNALS_DO_NOT_USE_OR_YOU_WILL_BE_FIRED`。

## 内部结构

可以认为，当`React`团队希望在`react`与**宿主环境对应的包**之间共享数据时，就会把他保存在这个神秘的内部变量中。

比如上文提到的，**Hook的具体实现**。

再比如，`object.assign`方法的`polyfill`，在`react`与`react-dom`中都会用到，但如果两个包中分别引入，再分别打包，那么`polyfill`的代码会重复出现在`react`与`react-dom`两个包中。

为了减少重复代码，`react`会引入`object.assign`方法的`polyfill`，再将它保存在神秘的内部变量中。

`react`作为`react-dom`的`peerDependencies`，当项目中引入这两个包后，`react-dom`内部使用的`object.assign`实际来自`react`：

```
// react-dom包内部
const react = require('react');
const { assign } = react.__SECRET_INTERNALS_DO_NOT_USE_OR_YOU_WILL_BE_FIRED;
```

## 常见问题

了解了神秘的内部变量的作用，我们再来看看这种实现会造成的问题。

假设我们有2个项目：

*   组件库项目A，负责开发组件
*   业务项目B，依赖A

B安装依赖后，A会出现在B的`node_modules`中。

为了调试方便，我们用`npm link`功能将B中依赖的A由**B的node_modules中的A**改为**组件库项目A**，

当`npm link`后，B中业务代码使用的`useState`来自于**B的node_modules中的react**。

而B中引入的组件库A的组件中使用的`useState`来自于**A的node_modules中的react**。

不同的`react`对应不同的`__SECRET_INTERNALS_DO_NOT_USE_OR_YOU_WILL_BE_FIRED`，最终对应不同的`react-dom`。

这就会造成报错。

解决办法是在项目中为`react`增加别名（alias），使项目中所有用到`react`的地方都指向同一个`react`。

## 总结

本文我们了解了`react`与`react-dom`中神秘的内部变量`__SECRET_INTERNALS_DO_NOT_USE_OR_YOU_WILL_BE_FIRED`的作用。

他能够在这两个包之间传递共享的数据。

需要注意的一点是，如果你也想用这种方式在两个包之间共享数据，需要将其中一个包设为另一个包的`peerDependencies`。

否则，在打包时，**被共享的数据**只会在两个包中分别存在一份。
>原文：https://segmentfault.com/a/1190000042097974
