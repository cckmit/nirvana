---
title: react18已经来了
date: 2022-01-07 14:32:19
categories: 前端
---
## react18他来了

react17都还没捂热，react18突然就来了，没有一点点防备，react官网已经放出react18的介绍了[官宣介绍](https://reactjs.org/blog/2021/06/08/the-plan-for-react-18.html)。说了这么多，我们接下来看下官方多react18的介绍吧。

## 新特新

### 1.Automatic batching

这个特性简单来说，就是自动批量更新，对于熟悉react的同学来说，对于下面这段代码的渲染执行一定不会陌生：

```
function App() {
  const [count, setCount] = useState(0);
  const [flag, setFlag] = useState(false);

  function handleClick() {
    setCount(c => c + 1); // Does not re-render yet
    setFlag(f => !f); // Does not re-render yet
    // React will only re-render once at the end (that's batching!)
  }

  return (
    <div>
      <button onClick={handleClick}>Next</button>
      <h1 style={{ color: flag ? "blue" : "black" }}>{count}</h1>
    </div>
  );
}
复制代码
```

这段代码我直接拷贝Dan Abramov对于Automatic batching特性说明的演示代码（下面也是-_-），从注释来看，handleClick只会触发一次渲染，为什么这么设计呢，用Dan的例子解释：饭店的服务生不会你点了一个菜就会去通知厨房吧，而是点完了之后再一次性告诉厨房，这么做的一个最大好处就是性能更好，接下来再看下一段代码：

```
function App() {
  const [count, setCount] = useState(0);
  const [flag, setFlag] = useState(false);

  function handleClick() {
    fetchSomething().then(() => {
      // React 17 and earlier does NOT batch these:
      setCount(c => c + 1); // Causes a re-render
      setFlag(f => !f); // Causes a re-render
    });
  }

  return (
    <div>
      <button onClick={handleClick}>Next</button>
      <h1 style={{ color: flag ? "blue" : "black" }}>{count}</h1>
    </div>
  );
}
复制代码
```

这一次会渲染两次，如果你深入react原理了解，也不会很差异这种结果。简单来说，异步任务执行的时候其实已经不在react的上下文环境了，react内部是通过一个标识来标记是否需要批量更新的，render开始，标记为true，commit之后，标记为false，异步任务再执行，其实是在commit之后的，那么由于标记为false，因此就不走批量了。大概就是这么个意思，有兴趣的可以去深挖一下其中的实现细节。不光是setTimeout，用dan的描述来说，`Updates inside of promises, setTimeout, native event handlers, or any other event were not batched in React by default`,因此promise/setTimeout/原生事件这些触发的渲染都不会走批量，当然这些都是在18版本之前，18版本是怎样的呢，还是上代码吧：

```
setTimeout(() => {
  setCount(c => c + 1);
  setFlag(f => !f);
  // React will only re-render once at the end (that's batching!)
}, 1000);
复制代码
```

这里的异步任务就完成了自动的批量更新，当然在18版本要开启这个功能的话，需要使用ReactDOM.createRoot去挂载我们的应用，如果我们还是使用ReactDOM.render的话，就不会启用Automatic batching了。 Automatic batching看起来很香，但是如果在一些场景我不想用呢，官方也提供了解决办法，请看例子：

```
import { flushSync } from 'react-dom'; // Note: react-dom, not react

function handleClick() {
  flushSync(() => {
    setCounter(c => c + 1);
  });
  // React has updated the DOM by now
  flushSync(() => {
    setFlag(f => !f);
  });
  // React has updated the DOM by now
}
复制代码
```

react work group还对Automatic batching做了很多的探讨，包括对class/hooks的影响，有兴趣的可以[点击这里查看](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Freactwg%2Freact-18%2Fdiscussions%2F21 "https://github.com/reactwg/react-18/discussions/21")。

看完对Automatic batching的说明后，还想补充一点，react18版本前的blocking和concurrent模式其实已经支持`Automatic batching of multiple setStates`了，大家可以去试试效果。最后再说一下unstable_batchedUpdates这个api，在18版本之前，如果我们要手动批量，需要借助它去实现，在18版本会依然支持。

### 2.startTransition

这是一个崭新的api，干什么的呢，就是让我们的应用交互更加丝滑，用来提升体验的，首先我们来了解一下它要解决一个什么样的问题。

官方工作小组里面的讨论描述了一个场景，就是一个输入框，接收用户输入，然后去筛选列表项，场景很常见，但是会有一个性能隐患，输入这个操作可能会触发大量的更新，导致页面卡顿，给用户直观的感受就是输入框有点卡，不能实时显示输入的字符了，那么要怎么破呢，这让我想到了react的并发渲染模式，不就是要来解决这种优先级的问题么，但是遗憾的是一直处于实验当中，还不能安心用在工作中，不扯远了，还是回到这个api上来，通过代码我们对比一下：

```
// Urgent: Show what was typed
setInputValue(input);

// Not urgent: Show the results
setSearchQuery(input);
复制代码
```

```
import { startTransition } from 'react';

// Urgent: Show what was typed
setInputValue(input);

// Mark any state updates inside as transitions
startTransition(() => {
  // Transition: Show the results
  setSearchQuery(input);
});
复制代码
```

代码片段1就是我们的常用写法，用户输入就更新输入框的状态值进行实时显示输入，然后去筛选列表，setInputValue和setSearchQuery是同时执行的；代码片段2就使用了startTransition这个api，将setSearchQuery包裹其中，实现手动的渲染任务优先级排列，那么此时setInputValue的更新就高于setSearchQuery，因此用户的输入响应就能得到保证，从而实现丝滑的体验，官方还将其于setTimeout进行了对比，这里就不展开介绍了，大家[点击这里查看](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Freactwg%2Freact-18%2Fdiscussions%2F41 "https://github.com/reactwg/react-18/discussions/41")。

### 3.New Suspense SSR Architecture

react18对SSR的性能进行了新的改进，引入了pipeToNodeWritable这个新的API，这个API可以替换renderToString，同时renderToNodeStream被标记为Deprecated，为什么会有这些改动呢，官方给出了解释：

```
renderToString: Keeps working (with limited Suspense support).
renderToNodeStream: Deprecated (with full Suspense support, but without streaming).
pipeToNodeWritable: New and recommended (with full Suspense support and streaming)
复制代码
```

出现了两个需要注意的单词：Suspense和streaming，Suspense这个组件在16.6.0被正式提出来，以前主要配合React.lazy用来异步加载组件的，而streaming就是指的React Server Components，现在react18对这两者的支持就更加完善了，因此react18的SSR将让用户更快的看见界面，更早的交互。 react18的SSR相比以前的SSR，有啥优势呢，那我们先看下传统的SSR流程吧：

```
On the server, fetch data for the entire app.
Then, on the server, render the entire app to HTML and send it in the response.
Then, on the client, load the JavaScript code for the entire app.
Then, on the client, connect the JavaScript logic to the server-generated HTML for the entire app (this is “hydration”).
复制代码
```

以上四步必须严格按照流程一步步来，就像waterfall一样，如果其中哪一步慢了，就会阻塞后面的流程，这样用户就需要忍受更长的白屏时间，因此如果将上面四个步骤打散成一个个小的任务单元，那么先完成的任务就可以尽快呈现到用户面前，这样体验自然就更优了，这也是react18对Suspense和streaming改进的动力所在，dan对这一部分有详细的介绍，点[这里](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Freactwg%2Freact-18%2Fdiscussions%2F37 "https://github.com/reactwg/react-18/discussions/37")了解更多。

## 渐进式升级

每当版本发生了重大更新，升级就是每个框架必须要考虑的一件事情，对于开发者来讲，升级不能对我现有的项目造成影响吧，放心，react考虑得比我们更多，其实vue也是一样，都提供了渐进式平滑得升级策略。react官方说了，放心升级吧，只需对应用程序代码进行很少的更改或不做任何更改，其实不光是react18，react16到17，对于升级其实都是成本很小的。

## 社区和react18工作小组

React 18 Working Group也是随着react18版本的到来而成立的一个组织，工作职能跟w3c的working group类似，作为联系社区与react18的桥梁，充分吸收来自各方的意见和讨论，大家有兴趣可以去了解一下。

## 发布计划

```
React 18 Library Alpha (Available now)
React 18 Public Beta (Months)
React 18 RC (Months)
React 18 (2-4 weeks after RC)
复制代码
```

官方推出的发布计划，大家拭目以待吧。

>作者：putao
链接：https://juejin.cn/post/6971655993390317598
来源：稀土掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
