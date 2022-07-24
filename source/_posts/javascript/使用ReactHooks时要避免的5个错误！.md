---
title: 使用React Hooks 时要避免的5个错误！
date: 2021-06-10 16:51:15
categories: 前端
---
本文已经过原作者 Shadeed 授权翻译。

很有可能你已经读过很多关于如何使用React Hook 的文章。但有时候，知道何时不使用与知道如何使用同样重要。

在这篇文章中，主要介绍一下 React hooks 错误使用方式，以及如何解决它们。

*   不要更改 Hook 调用顺序
*   不要使用过时状态
*   不要创建过时的闭包
*   不要将状态用于基础结构数据
*   不要忘记清理副作用

### 1.不要更改 Hook 调用顺序

在写这篇文章的前几天，我编写了一个通过id获取游戏信息的组件，下面是一个简单的版本 `FetchGame`：

```
function FetchGame({ id }) {
  if (!id) {
    return 'Please select a game to fetch';
  }

  const [game, setGame] = useState({ 
    name: '',
    description: '' 
  });

  useEffect(() => {
    const fetchGame = async () => {
      const response = await fetch(`/api/game/${id}`);
      const fetchedGame = await response.json();
      setGame(fetchedGame);
    };
    fetchGame();
  }, [id]);

  return (
    <div> <div>Name: {game.name}</div> <div>Description: {game.description}</div> </div>
  ); 
```

组件`FetchGame` 接收 `id`（即要获取的游戏的ID）。`useEffect()`  在`await fetch(/game/${id})`提取游戏信息并将其保存到状态变量`game`中。

打开演示(https://codesandbox.io/s/hooks-order-warning-rdxpg?file=/pages/index.js) 。组件正确地执行获取操作，并使用获取的数据更新状态。但是看看tab Eslint警告: 有 Hook 执行顺序不正确的问题。

![图片](https://upload-images.jianshu.io/upload_images/10024246-147e46cb421a86ae?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

问题发生在这一判断：

```
function FetchGame({ id }) {
 if (!id) { return 'Please select a game to fetch'; }  
   // ...
}
```

当`id`为空时，组件渲染`'Please select a game to fetch'`并退出，不调用任何 Hook。

但是，如果 `id`不为空（例如等于'1'），则会调用`useState()`和 `useEffect()`。

有条件地执行 Hook 可能会导致难以调试的意外错误。React Hook的内部工作方式要求组件在渲染之间总是以相同的顺序调用 Hook。

这正是钩子的第一条规则:不要在循环、条件或嵌套函数内调用 Hook。

解决方法就是将条件判断放到 Hook 后面：

```
function FetchGame({ id }) {
  const [game, setGame] = useState({ 
    name: '',
    description: '' 
  });

  useEffect(() => {
    const fetchGame = async () => {
      const response = await fetch(`/api/game/${id}`);
      const fetchedGame = await response.json();
      setGame(fetchedGame);
    };
 if (id) {      fetchGame();     }  }, [id]);

 if (!id) { return 'Please select a game to fetch'; }
  return (
    <div>
 <div>Name: {game.name}</div>
 <div>Description: {game.description}</div>
 </div>
  );
}
```

现在，无论`id`是否为空，`useState()`和`useEffect()` 总是以相同的顺序被调用，这就是 Hook 应该始终被调用的方式。

### 2.不要使用过时状态

下面的组件`MyIncreaser`在单击按钮时增加状态变量`count`:

```
function MyIncreaser() {
  const [count, setCount] = useState(0);

  const increase = useCallback(() => {
    setCount(count + 1);
  }, [count]);

  const handleClick = () {
 increase(); increase(); increase();  };

  return (
    <>
 <button onClick={handleClick}>Increase</button>
 <div>Counter: {count}</div>
 </>
  );
}
```

这里有趣一点的是，`handleClick`调用了3次状态更新。

现在，在打开演示之前，问一个问题：如果单击一次按钮，计数器是否增加`3`？

打开演示（https://codesandbox.io/s/stale-variable-jo32q?file=/src/index.js），点击按钮一次，看看结果。

不好意思，即使在`handleClick()`中3次调用了`increase()`，计数也只增加了`1`。

问题在于`setCount(count + 1)`状态更新器。当按钮被点击时，React调用`setCount(count + 1)`3次

```
 const handleClick = () {
    increase();
    increase();
    increase();
  };

// 等价:

  const handleClick = () {
    setCount(count + 1);
    // count variable is now stale
    setCount(count + 1);
    setCount(count + 1);
  };
```

`setCount(count + 1)`的第一次调用正确地将计数器更新为`count + 1 = 0 + 1 = 1`。但是，接下来的两次`setCount(count + 1)`调用也将计数设置为`1`，因为它们使用了过时的`stale`状态。

通过使用函数方式更新状态来解决过时的状态。我们用`setCount(count => count + 1)`代替`setCount(count + 1)`：

```
function MyIncreaser() {
  const [count, setCount] = useState(0);

  const increase = useCallback(() => {
 setCount(count => count + 1);  }, []);

  const handleClick = () {
    increase();
    increase();
    increase();
  };

  return (
    <>
 <button onClick={handleClick}>Increase</button>
 <div>Counter: {count}</div>
 </>
  );
}
```

这里有一个好规则可以避免遇到过时的变量：

如果你使用当前状态来计算下一个状态，总是使用函数方式来更新状态:`setValue(prevValue => prevValue + someResult)`。

### 3.不要创建过时的闭包

React Hook 很大程序上依赖于闭包的概念。依赖闭包是它们如此富有表现力的原因。

JavaScript 中的闭包是从其词法作用域捕获变量的函数。不管闭包在哪里执行，它总是可以从定义它的地方访问变量。

当使用 Hook 接受回调作为参数时(如`useEffect(callback, deps)`， `useCallback(callback, deps))`，你可能会创建一个过时的闭包，一个捕获了过时的状态或变量的闭包。

我们来看看一个使用`useEffect(callback, deps)` 而忘记正确设置依赖关系时创建的过时闭包的例子。

在组件`<WatchCount>`中，`useEffect()`每2秒打印一次`count`的值

```
const [count, setCount] = useState(0);

  useEffect(function() {
    setInterval(function log() {
      console.log(`Count is: ${count}`);
    }, 2000);
  }, []);

  const handleClick = () => setCount(count => count + 1);

  return (
    <> <button onClick={handleClick}>Increase</button> <div>Counter: {count}</div> </>
  );
}
```

打开演示(https://codesandbox.io/s/stale-variable-jo32q?file=/src/index.js)，点击按钮。在控制台查看，每2秒打印的都 是 `Count is: 0`,，不管`count`状态变量的实际值是多少。

为啥这样子？

第一次渲染时， `log` 函数捕获到的 `count` 的值为 `0`。

之后，当按钮被单击并且`count`增加时，`setInterval`取到的 `count` 值仍然是从初始渲染中捕获`count`为0的值。`log` 函数是一个过时的闭包，因为它捕获了一个过时的状态变量`count`。

解决方案是让`useEffect()`知道闭包`log`依赖于`count`，并正确重置计时器

```
function WatchCount() {
  const [count, setCount] = useState(0);

  useEffect(function() {
    const id = setInterval(function log() {
      console.log(`Count is: ${count}`);
    }, 2000);
 return () => clearInterval(id); }, [count]);
  const handleClick = () => setCount(count => count + 1);

  return (
    <>
 <button onClick={handleClick}>Increase</button>
 <div>Counter: {count}</div>
 </>
  );
}
```

正确设置依赖关系后，一旦`count`发生变化，`useEffect()`就会更新`setInterval()`的闭包。

为了防止闭包捕获旧值：确保提供给 Hook 的回调函数中使用依赖项。

### 4.不要将状态用于基础结构数据

有一次，我需要在状态更新上调用副作用，在第一个渲染不用调用副作用。`useEffect(callback, deps)`总是在挂载组件后调用回调函数:所以我想避免这种情况。

我找到了以下的解决方案

```
function MyComponent() {
  const [isFirst, setIsFirst] = useState(true);
  const [count, setCount] = useState(0);

  useEffect(() => {
    if (isFirst) {
      setIsFirst(false);
      return;
    }
    console.log('The counter increased!');
  }, [count]);

  return (
    <button onClick={() => setCount(count => count + 1)}> Increase </button>
  );
}
```

状态变量`isFirst`用来判断是否是第一次渲染。一旦更新`setIsFirst(false)`，就会出现另一个无缘无故的重新渲染。

保持`count`状态是有意义的，因为界面需要渲染 count 的值。但是，`isFirst`不能直接用于计算输出。

是否为第一个渲染的信息不应存储在该状态中。基础结构数据，例如有关渲染周期（即首次渲染，渲染数量），计时器ID（`setTimeout()`，`setInterval()`），对DOM元素的直接引用等详细信息，应使用引用`useRef()`进行存储和更新。

我们将有关首次渲染的信息存储到 Ref 中：

```
 const isFirstRef = useRef(true);  const [count, setCount] = useState(0);

  useEffect(() => {
    if (isFirstRef.current) {
      isFirstRef.current = false;
      return;
    }
    console.log('The counter increased!');
  }, [count]);

  return (
    <button onClick={() => setCounter(count => count + 1)}>
 Increase
 </button>
  );
}
```

`isFirstRef`是一个引用，用于保存是否为组件的第一个渲染的信息。`isFirstRef.current`属性用于访问和更新引用的值。

**重要说明：更新参考`isFirstRef.current = false`不会触发重新渲染。**

### 5.不要忘记清理副作用

很多副作用，比如获取请求或使用`setTimeout()`这样的计时器，都是异步的。

如果组件卸载或不再需要该副作用的结果，请不要忘记清理该副作用。

下面的组件有一个按钮。当按钮被点击时，计数器每秒钟延迟增加`1`：

```
function DelayedIncreaser() {  const [count, setCount] = useState(0);  const [incrfunction DelayedIncreaser() {
  const [count, setCount] = useState(0);
  const [increase, setShouldIncrease] = useState(false);

  useEffect(() => {
    if (increase) {
      setInterval(() => {
        setCount(count => count + 1)
      }, 1000);
    }
  }, [increase]);

  return (
    <> <button onClick={() => setShouldIncrease(true)}> Start increasing </button> <div>Count: {count}</div> </>
  );
}

```

打开演示（https://codesandbox.io/s/unmounted-state-update-n1d3u?file=/src/index.js），点击开始按钮。正如预期的那样，状态变量`count`每秒钟都会增加。

在进行递增操作时，单击`umount` 按钮，卸载组件。React会在控制台中警告更新卸载组件的状态。

![图片](https://upload-images.jianshu.io/upload_images/10024246-35c8a5691d0d6056?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

修复`DelayedIncreaser`很简单：只需从`useEffect()`的回调中返回清除函数：

```
 // ...

  useEffect(() => {
    if (increase) {
      const id = setInterval(() => {
        setCount(count => count + 1)
      }, 1000);
 return () => clearInterval(id);    }
  }, [increase]);

  // ...
}
```

也就是说，每次编写副作用代码时，都要问自己它是否应该清理。计时器，频繁请求(如上传文件)，sockets 几乎总是需要清理。

### 6\. 总结

从React钩子开始的最好方法是学习如何使用它们。

但你也会遇到这样的情况:你无法理解为什么他们的行为与你预期的不同。知道如何使用React Hook还不够:你还应该知道何时不使用它们。

首先不要做的是有条件地渲染 Hook 或改变 Hook 调用的顺序。无论Props 或状态值是什么，React都期望组件总是以相同的顺序调用Hook。

要避免的第二件事是使用过时的状态值。要避免过时 状态，请使用函数方式更新状态。

不要忘记指出接受回调函数作为参数的 Hook 的依赖关系:例如`useEffect(callback, deps)`，`useCallback(callback, deps`),这可以解决过时闭包问题。

不要将基础结构数据（例如有关组件渲染周期，`setTimeout()`或`setInterval()`）存储到状态中。经验法则是将此类数据保存在 Ref 中。

最后，别忘了清除你的副作用。

* * *

>作者：Shadeed    来源：dmitripavlutin原文：https://dmitripavlutin.com/react-hooks-mistakes-to-avoid/
