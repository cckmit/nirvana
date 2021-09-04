---
title: 如何解决-React-useEffect()-的无限循环
date: 2021-06-10 16:28:03
category: javascript
---

`useEffect()` 主要用来管理副作用，比如通过网络抓取、直接操作 DOM、启动和结束计时器。

虽然`useEffect()` 和 `useState`(管理状态的方法)是最常用的钩子之一，但需要一些时间来熟悉和正确使用。

使用`useEffect()`时，你可能会遇到一个陷阱，那就是组件渲染的无限循环。在这篇文章中，会讲一下产生无限循环的常见场景以及如何避免它们。

### 1\. 无限循环和副作用更新状态

假设我们有一个功能组件，该组件里面有一个 `input` 元素，组件是功能是计算 `input` 更改的次数。

我们给这个组件取名为 `CountInputChanges`，大概的内容如下：

```
function CountInputChanges() {
  const [value, setValue] = useState('');
  const [count, setCount] = useState(-1);

 useEffect(() => setCount(count + 1));
  const onChange = ({ target }) => setValue(target.value);

  return (
    <div>
 <input type="text" value={value} onChange={onChange} />
 <div>Number of changes: {count}</div>
 </div>
  )
}
```

`<input type =“ text” value = {value} onChange = {onChange} />`是受控组件。`value`变量保存着 `input` 输入的值，当用户输入输入时，`onChange`事件处理程序更新 `value` 状态。

这里使用`useEffect()`更新`count`变量。每次由于用户输入而导致组件重新渲染时，`useEffect(() => setCount(count + 1))`就会更新计数器。

因为`useEffect(() => setCount(count + 1))`是在没有依赖参数的情况下使用的，所以`()=> setCount(count + 1)`会在每次渲染组件后执行回调。

你觉得这样写会有问题吗？打开演示自己试试看：[https://codesandbox.io/s/infi...](https://codesandbox.io/s/infinite-loop-9rb8c?file=/src/App.js)

运行了会发现`count`状态变量不受控制地增加，即使没有在`input`中输入任何东西，这是一个无限循环。

![](https://upload-images.jianshu.io/upload_images/10024246-37120de4f3d11d83.gif?imageMogr2/auto-orient/strip)


问题在于`useEffect()`的使用方式：

`useEffect(() => setCount(count + 1));`

它生成一个无限循环的组件重新渲染。

在初始渲染之后，`useEffect()`执行更新状态的副作用回调函数。状态更新触发重新渲染。重新渲染之后，`useEffect()`执行副作用回调并再次更新状态，这将再次触发重新渲染。

![](https://upload-images.jianshu.io/upload_images/10024246-f16a1491c27f578e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 1.1通过依赖来解决

无限循环可以通过正确管理`useEffect(callback, dependencies)`依赖项参数来修复。

因为我们希望`count`在值更改时增加，所以可以简单地将`value`作为副作用的依赖项。

```
import { useEffect, useState } from 'react';

function CountInputChanges() {
  const [value, setValue] = useState('');
  const [count, setCount] = useState(-1);

 useEffect(() => setCount(count + 1), [value]);
  const onChange = ({ target }) => setValue(target.value);

  return (
    <div>
 <input type="text" value={value} onChange={onChange} />
 <div>Number of changes: {count}</div>
 </div>
  );
}
```

添加`[value]`作为`useEffect`的依赖，这样只有当`[value]`发生变化时，计数状态变量才会更新。这样做可以解决无限循环。

![](https://upload-images.jianshu.io/upload_images/10024246-ff0573e934c797a4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 1.2 使用 ref

除了依赖，我们还可以通过 [useRef()](https://reactjs.org/docs/hooks-reference.html#useref) 来解决这个问题。

其思想是更新 Ref 不会触发组件的重新渲染。

```
import { useEffect, useState, useRef } from "react";

function CountInputChanges() {
  const [value, setValue] = useState("");
  const countRef = useRef(0);

 useEffect(() => countRef.current++);
  const onChange = ({ target }) => setValue(target.value);

  return (
    <div>
 <input type="text" value={value} onChange={onChange} />
 <div>Number of changes: {countRef.current}</div>
 </div>
  );
}
```

`useEffect(() => countRef.current++)` 每次由于`value`的变化而重新渲染后，`countRef.current++`就会返回。引用更改本身不会触发组件重新渲染。

![](https://upload-images.jianshu.io/upload_images/10024246-57a1134f55aadb94.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 2\. 无限循环和新对象引用

即使正确设置了`useEffect()`依赖关系，使用对象作为依赖关系时也要小心。

例如，下面的组件`CountSecrets`监听用户在`input`中输入的单词，一旦用户输入特殊单词`'secret'`，统计 'secret' 的次数就会加 1。
```
import { useEffect, useState } from "react";

function CountSecrets() {
  const [secret, setSecret] = useState({ value: "", countSecrets: 0 });

  useEffect(() => {
    if (secret.value === 'secret') {
 setSecret(s => ({...s, countSecrets: s.countSecrets + 1}));    }
 }, [secret]);
  const onChange = ({ target }) => {
    setSecret(s => ({ ...s, value: target.value }));
  };

  return (
    <div>
 <input type="text" value={secret.value} onChange={onChange} />
 <div>Number of secrets: {secret.countSecrets}</div>
 </div>
  );
}
```

打开演示（[https://codesandbox.io/s/infi...](https://codesandbox.io/s/infinite-loop-obj-dependency-7t26v?file=/src/App.js)）自己试试，当前输入 `secret`，`secret.countSecrets`的值就开始不受控制地增长。

这是一个无限循环问题。

为什么会这样？

`secret`对象被用作`useEffect(..., [secret])`。在副作用回调函数中，只要输入值等于`secret`，就会调用更新函数

```
setSecret(s => ({...s, countSecrets: s.countSecrets + 1}));
```

这会增加`countSecrets`的值，但也会创建一个新对象。

`secret`现在是一个新对象，依赖关系也发生了变化。所以`useEffect(..., [secret])`再次调用更新状态和再次创建新的`secret`对象的副作用，以此类推。

JavaScript 中的两个对象只有在引用完全相同的对象时才相等。

#### 2.1 避免将对象作为依赖项

解决由循环创建新对象而产生的无限循环问题的最好方法是避免在`useEffect()`的`dependencies`参数中使用对象引用。
```
let count = 0;

useEffect(() => {
  // some logic
}, [count]); // Good!
```
```
let myObject = {
  prop: 'Value'
};

useEffect(() => {
  // some logic
}, [myObject]); // Not good!
useEffect(() => {
  // some logic
}, [myObject.prop]); // Good!
```

修复`<CountSecrets>`组件的无限循环问题，可以将`useEffect(..., [secret]))` 变为 `useEffect(..., [secret.value])`。

仅在`secret.value`更改时调用副作用回调就足够了，下面是修复后的代码：

```
import { useEffect, useState } from "react";

function CountSecrets() {
  const [secret, setSecret] = useState({ value: "", countSecrets: 0 });

  useEffect(() => {
    if (secret.value === 'secret') {
      setSecret(s => ({...s, countSecrets: s.countSecrets + 1}));
    }
 }, [secret.value]);
  const onChange = ({ target }) => {
    setSecret(s => ({ ...s, value: target.value }));
  };

  return (
    <div>
 <input type="text" value={secret.value} onChange={onChange} />
 <div>Number of secrets: {secret.countSecrets}</div>
 </div>
  );
}
```

### 3 总结

`useEffect(callback, deps)`是在组件渲染后执行`callback(副作用)`的 Hook。如果不注意副作用的作用，可能会触发组件渲染的无限循环。

生成无限循环的常见情况是在副作用中更新状态，没有指定任何依赖参数

```
useEffect(() => {
  // Infinite loop!
  setState(count + 1);
});
```

避免无限循环的一种有效方法是正确设置依赖项：

```
useEffect(() => {
  // No infinite loop
  setState(count + 1);
}, [whenToUpdateValue]);
```

另外，也可以使用 Ref，更新 Ref 不会触发重新渲染：

```
useEffect(() => {
  // No infinite loop
  countRef.current++;
});
```

无限循环的另一种常见方法是使用对象作为`useEffect()`的依赖项，并在副作用中更新该对象(有效地创建一个新对象)

```
useEffect(() => {
  // Infinite loop!
  setObject({
    ...object,
    prop: 'newValue'
  })
}, [object]);
```
避免使用对象作为依赖项，只使用特定的属性(最终结果应该是一个原始值)：

```
useEffect(() => {
  // No infinite loop
  setObject({
    ...object,
    prop: 'newValue'
  })
}, [object.whenToUpdateProp]);
```

当使用`useEffect()`时，你还知道有其它方式会引起无限循环陷阱吗？
