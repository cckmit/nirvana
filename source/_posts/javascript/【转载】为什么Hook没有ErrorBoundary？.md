---
title: 【转载】为什么Hook没有ErrorBoundary？
date: 2022-06-22 14:27:21
categories: 前端
tags: [react]
---
>原文：https://segmentfault.com/a/1190000041974765

大家好，我卡颂。

在很多全面使用`Hooks`开发的团队，唯一使用`ClassComponent`的场景就是**使用ClassComponent创建ErrorBoundary**。

可以说，如果`Hooks`存在如下两个生命周期函数的替代品，就能全面抛弃`ClassComponent`了：

*   getDerivedStateFromError
*   componentDidCatch

那为什么还没有对标的`Hook`呢？

今天我们从上述两个生命周期函数的实现原理，以及要移植到`Hook`上需要付出的成本来谈论这个问题。

欢迎加入[人类高质量前端框架群](https://link.segmentfault.com/?enc=AD9h8gfDdYVuqw4vYT0Ptw%3D%3D.tNWJkhg0DQdMydkcIrkdhl5xILm1Tx%2F7rNbSH0xDqxoLuZ1YnjTQX9oWTMxFcAhW)，带飞

## ErrorBoundary实现原理

`ErrorBoundary`可以捕获子孙组件中**React工作流程**内的错误。

**React工作流程**指：

*   render阶段，即**组件render**、**Diff算法**发生的阶段
*   commit阶段，即**渲染DOM**、**componentDidMount/Update执行**的阶段

这也是为什么**事件回调中发生的错误**无法被`ErrorBoundary`捕获 —— 事件回调并不属于**React工作流程**。

### 如何捕获错误

**render阶段**的整体执行流程如下：

```
do {
  try {
    // render阶段具体的执行流程
    workLoop();
    break;
  } catch (thrownValue) {
    handleError(root, thrownValue);
  }
} while (true);
```

可以发现，如果**render阶段**发生错误，会被捕获并执行`handleError`方法。

类似的，**commit阶段**的整体执行流程如下：

```
try {
  // ...具体执行流程
} catch (error) {
  captureCommitPhaseError(current, nearestMountedAncestor, error);
}
```

如果**commit阶段**发生错误，会被捕获并执行`captureCommitPhaseError`方法。

### getDerivedStateFromError原理

捕获后的错误如何处理呢？

我们知道，`ClassComponent`中`this.setState`第一个参数，除了可以接收**新的状态**，也能接收**改变状态的函数**作为参数：

```
// 可以这样
this.setState(this.state.num + 1)

// 也可以这样
this.setState(num => num + 1)
```

`getDerivedStateFromError`的实现，就借助了`this.setState`中**改变状态的函数**这一特性。

当捕获错误后，即：

*   对于**render阶段**，`handleError`执行后
*   对于**commit阶段**，`captureCommitPhaseError`执行后

会在`ErrorBoundary`对应组件中触发类似如下更新：

```
this.setState(
  getDerivedStateFromError.bind(null, error)
)
```

这就是为什么`getDerivedStateFromError`要求开发者返回**新的state** —— 本质来说，他就是触发一次新的更新。

### componentDidCatch原理

再来看另一个`ErrorBoundary`相关的生命周期函数 —— `componentDidCatch`。

`ClassComponent`中`this.setState`的第二个参数，可以接收**回调函数**作为参数：

```
this.setState(newState, () => {
  // ...回调
})

```

当触发的更新渲染到页面后，回调会触发。

这就是`componentDidCatch`的实现原理。

当捕获错误后，会在`ErrorBoundary`对应组件中触发类似如下更新：

```this.setState(this.state, componentDidCatch.bind(this, error))```

### 处理“未捕获”的错误

可以发现，**React运行流程**中的错误，都已经被`React`自身捕获了，再交由`ErrorBoundary`处理。

如果没有定义`ErrorBoundary`，这些**被捕获的错误**需要重新抛出，营造**错误未被捕获的感觉**。

那这一步在哪里执行呢？

与`this.setState`类似，`ReactDOM.render(element, container[, callback])`第三个参数也能接收**回调函数**。

如果开发者没有定义`ErrorBoundary`，那么`React`最终会在`ReactDOM.render`的回调中抛出错误。

可以发现，在`ClassComponent`中`ErrorBoundary`的实现完全依赖了`ClassComponent`已有的特性。

而`Hooks`本身并不存在类似`this.setState`的回调特性，所以实现起来会比较复杂。

## 实现Hooks中的ErrorBoundary

除了上述谈到的阻碍，`FunctionComponent`与`ClassComponent`在源码层面的运行流程也有细节上的差异，要照搬实现也有一定难度。

如果一定要实现，在**最大程度复用现有基础设施**的指导方针下，`useErrorBoundary`（`ErrorBoundary`在`Hooks`中的实现）的使用方式应该类似如下：
```
function ErrorBoundary({children}: {children: ReactNode}) {
  const [errorMsg, updateError] = useState<Error | null>(null);

  useErrorBoundary((e: Error) => {
    // 捕获到错误，触发更新
    updateError(e);
  })

  return (
    <div>
      {errorMsg ? '报错：' + errorMsg.toString() : children}
    </div>
  )
}
```

其中`useErrorBoundary`的触发方式类似`useEffect`：
```
useErrorBoundary((e: Error) => {
  // ...
})

// 类似
useEffect(() => {
  // ...
})
```

笔者仿照`ClassComponent`中`ErrorBoundary`的实现原理与`useEffect`的实现原理，实现了原生Hooks —— `useErrorBoundary`。

感兴趣的朋友可以在[useErrorBoundary在线示例](https://link.segmentfault.com/?enc=p2IDWnToOxEBq69hvaO6Mg%3D%3D.7wfxRUagyLbrMB6VqGXYvmTc68Zi44IeJDqmNqdDNQPW2kTC2e4qESSJOvuhGd5t%2FhwTKkSrRNvUOlmLvqEIGA%3D%3D)体验效果。

## 总结

`ErrorBoundary`在`ClassComponent`中的实现使用了`this.setState`的回调函数特性，这使得`Hooks`中要完全实现同样功能，需要额外开发成本。

笔者猜测，这是没有提供对应原生`Hooks`的原因之一。

