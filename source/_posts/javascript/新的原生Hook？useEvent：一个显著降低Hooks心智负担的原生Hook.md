---
title: 新的原生Hook？useEvent：一个显著降低Hooks心智负担的原生Hook
date: 2022-06-03 16:28:03
categories: 前端
---
# 前言

useEvent[1] 是一个刚刚提案的原生Hook，还处于RFC。讨论地址在这里\~[2]下面有些代码就是来自其中

> RFC：Request for Comments 提案还在广泛的讨论阶段

# 为什么要这样🤔

## 没有 useEvent 的时候😶

我们先看看不用 useEvent 的情况：

```
function Chat() {
  const [text, setText] = useState('');

  // 🟡 Always a different function
  const onClick = () => {
    sendMessage(text);
  };

  return <SendButton onClick={onClick} />;
}
```

其中点击事件的回调函数 `onClick` 中需要读取当前键入的文本`text`，这里的`onClick`随着组件重新渲染一次次地重新创建，每次都会是不一样的引用，这显然带来了性能损耗，如果你想对其进行优化，你可能会这样做：

```
function Chat() {
  const [text, setText] = useState('');

  // 🟡 A different function whenever `text` changes
  const onClick = useCallback(() => {
    sendMessage(text);
  }, [text]);

  return <SendButton onClick={onClick} />;
}
```

### 通过 useCallback[3] 返回一个 memoized 回调函数。

> useCallback：返回一个 memoized[4] 回调函数。把内联回调函数及依赖项数组作为参数传入 `useCallback`，它将返回该回调函数的 `memoized` 版本，该回调函数仅在某个依赖项改变时才会更新。当你把回调函数传递给经过优化的并使用引用相等性去避免非必要渲染（例如 `shouldComponentUpdate`）的子组件时，它将非常有用。 `useCallback(fn, deps)` 相当于 `useMemo(() => fn, deps)`。

**最终使得`onClick`的引用始终不变**
**但是！**`onClcik`这个方法有需要保证每次都要拿到最新的、正确的text，所以他的`deps`中就自然是设置了`text`—— 坏了，“又回到最初的起点~”。随着每一次`keystroke`，`onClick`又变成了上面的情况：

> 🟡 Always a different function

但你又不能将其从`deps`中移除，移除了他就只能拿到`text`的初始值，失去了他本该有的功能...

## 小 useEvent 来给他整个活😎

`useEvent`就是为了解决此类问题，所以他干脆不要`deps`了，他就是一直返回一个相同的函数引用，哪怕`text`发生变化。**当然，保证它也能拿到最新的、正确的**`**text**`**。**

```
function Chat() {
  const [text, setText] = useState('');

  // ✅ Always the same function (even if `text` changes)
  const onClick = useEvent(() => {
    sendMessage(text);
  });

  return <SendButton onClick={onClick} />;
}
```

现在好了：

*   onClick 的引用始终是同一个
*   保证每次都能拿到最新的、正确的 `text`

* * *

当然还有其他一些场景，但是大致需求原理相同，就是不想让`A`因为`b`变化而总是重新加载，但是又因为要拿到`b`恰当的值，所以`deps`中必须`b`，导致不得不重新加载，掉进了“圈圈圆圆圈圈~”的陷阱。更多场景这里就不再赘述。更多案例可查看文末的学习资源~

## 好活好活🍔

总而言之，用`useEvent`给他裹上就是香，就是可以同时达到上面两个效果：

*   引用不变
*   拿到恰当的值

# 这是咋做到的🌝

说了这么多，我们来看看他这是咋做到的
他**大概**是这么个形状：（不是源码就长这样的意思嗷）

```
// (!) Approximate behavior

function useEvent(handler) {
  const handlerRef = useRef(null);

  // In a real implementation, this would run before layout effects
  useLayoutEffect(() => {
    handlerRef.current = handler;
  });

  return useCallback((...args) => {
    // In a real implementation, this would throw if called during render
    const fn = handlerRef.current;
    return fn(...args);
  }, []);
}
```

## 先回顾几个Hook相关知识点：

### useRef

useRef[5]:

> useRef 返回一个可变的 ref 对象，其 .current 属性被初始化为传入的参数（initialValue）。返回的 ref 对象在组件的整个生命周期内持续存在。

这里通过 useRef 保存回调函数`handler`到`handlerRef.current`，然后再在 useCallback 中从`handlerRef.current`来取函数再调用，这样避免了直接调用，跳出了闭包陷阱。并且不出意外的话`handler`在整个生命周期内持续存在，也就是**只有一个引用**。

### useLayoutEffect

这个 useLayoutEffect[6] 可能没那么常用，我们来看看这是啥嘞

> 其函数签名与 useEffect 相同，但它会在所有的 DOM 变更之后同步调用 effect。可以使用它来读取 DOM 布局并同步触发重渲染。在浏览器执行绘制之前，useLayoutEffect 内部的更新计划将被同步刷新。

### useEffect

回顾一下 useEffect[7]

> 默认情况下，effect 将在每轮渲染结束后执行

### 两者的区别

好了，现在我给你用一个字总结一下两者区别，`useLayoutEffect` 更“快”！这个“块”不是速度更快，而是他“抢跑”了哩。`useLayoutEffect` 是在`render`之前同步执行，`useEffect`在`render`之后异步执行，这里就是保证`useLayoutEffect` 里的回调肯定比`useEffect`更早前被调用、被执行。

### useCallback

#### 执行时机

前面说到

> `useCallback(fn, deps)` 相当于 `useMemo(() => fn, deps)`。

文档里是这样说 useMemo[8] 的：

> 记住，**传入 useMemo 的函数会在渲染期间执行**。请不要在这个函数内部执行与渲染无关的操作，诸如副作用这类的操作属于 useEffect 的适用范畴，而不是 useMemo。

也就是他是在`render`**时**执行的，也就是保证了赋值`handler`给`handlerRef.current`是在前面发生

#### 这里的作用

这里返回的是一个`useCallback`包裹后 `memoized`函数，其中从`handlerRef.current`中获取函数，并且`deps`为`[]`，也就是说他不会再次更新。

* * *

# 捋一捋🌊

回顾完知识点我们也就滤清了这个`useEvent`方法，一句话总结就是：它接收一个回调函数`handler`作为参数，**提供给你一个稳定的函数（始终只有一个引用）**并且调用时都是用的你传入的**最新的参数**`...args`——比如前面案例中的`text`，始终都是**最新的、正确的、恰当的。**
再结合一开始的案例，大概流程就是这样：


![](https://upload-images.jianshu.io/upload_images/10024246-2f48097fe3ff505f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

不过目前这个也是 5.4号刚刚提出，也就是昨天，在正式生产环境中使用应该还早，但我希望你还是能从本文有所收获滴~

> 🌊如果有所帮助，欢迎点赞关注，一起进步⛵
关于本文 来自：前舟
https://juejin.cn/post/7094186419500875812
