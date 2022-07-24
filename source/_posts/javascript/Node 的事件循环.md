---
title: Node 的事件循环
date: 2022-03-12 16:28:03
categories: 前端
tags: [node]
---
node的事件循环比浏览器复杂很多。由6个宏任务队列+6个微任务队列组成。

宏任务按照优先级从高到低依次是：

![](https://upload-images.jianshu.io/upload_images/10024246-7fe43d8c0020d833.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其执行规律是：在一个宏任务队列全部执行完毕后，去清空一次微任务队列，然后到下一个等级的宏任务队列，以此往复。一个宏任务队列搭配一个微任务队列。

六个等级的宏任务全部执行完成，才是一轮循环。

其中需要关注的是：Timers、Poll、Check阶段，因为我们所写的代码大多属于这三个阶段。

- 1.  Timers：定时器setTimeout/setInterval；
- 2.  Poll ：获取新的 I/O 事件, 例如操作读取文件等；
- 3.  Check：setImmediate回调函数在这里执行；

除此之外，node端微任务也有优先级先后：

- 1.  process.nextTick;
- 2.  promise.then 等;

清空微任务队列时，会先执行process.nextTick，然后才是微任务队列中的其他。

下面这段代码可以佐证浏览器和node的差异：

```
console.log('Script开始')
setTimeout(() => {
  console.log('第一个回调函数，宏任务1')
  Promise.resolve().then(function() {
    console.log('第四个回调函数，微任务2')
  })
}, 0)
setTimeout(() => {
  console.log('第二个回调函数，宏任务2')
  Promise.resolve().then(function() {
    console.log('第五个回调函数，微任务3')
  })
}, 0)
Promise.resolve().then(function() {
  console.log('第三个回调函数，微任务1')
})
console.log('Script结束')
```

```
node端：
Script开始
Script结束
第三个回调函数，微任务1
第一个回调函数，宏任务1
第二个回调函数，宏任务2
第四个回调函数，微任务2
第五个回调函数，微任务3

浏览器
Script开始
Script结束
第三个回调函数，微任务1
第一个回调函数，宏任务1
第四个回调函数，微任务2
第二个回调函数，宏任务2
第五个回调函数，微任务3
```

可以看出，在node端要等当前等级的所有宏任务完成，才能轮到微任务：`第四个回调函数，微任务2`在两个setTimeout完成后才打印。

因为浏览器执行时是一个宏任务+一个微任务队列，而node是一整个宏任务队列+一个微任务队列。

## node11.x 前后版本差异

node11.x 之前，其事件循环的规则就如上文所述：先取出完一整个宏任务队列中全部任务，然后执行一个微任务队列。

但在11.x 之后，node端的事件循环变得和浏览器类似：先执行一个宏任务，然后是一个微任务队列。但依然保留了宏任务队列和微任务队列的优先级。

可以用下面的Demo佐证：

```
console.log('Script开始')
setTimeout(() => {
  console.log('宏任务1（setTimeout)')
  Promise.resolve().then(() => {
    console.log('微任务promise2')
  })
}, 0)
setImmediate(() => {
  console.log('宏任务2')
})
setTimeout(() => {
  console.log('宏任务3（setTimeout)')
}, 0)
console.log('Script结束')
Promise.resolve().then(() => {
  console.log('微任务promise1')
})
process.nextTick(() => {
  console.log('微任务nextTick')
})

```

在 node11.x 之前运行：

```
Script开始
Script结束
微任务nextTick
微任务promise1
宏任务1（setTimeout)
宏任务3（setTimeout)
微任务promise2
宏任务2（setImmediate)
```

在 node11.x 之后运行：

```
Script开始
Script结束
微任务nextTick
微任务promise1
宏任务1（setTimeout)
微任务promise2
宏任务3（setTimeout)
宏任务2（setImmediate)
```

可以发现，在不同的node环境下：

1.  微任务队列中process.nextTick都有更高优先级，即使它后进入微任务队列，也会先打印`微任务nextTick`再`微任务promise1`;
2.  宏任务setTimeout比setImmediate优先级更高，`宏任务2(setImmediate)`是三个宏任务中最后打印的；
3.  在node11.x之前，微任务队列要等当前优先级的所有宏任务先执行完，在两个setTimeout之后才打印`微任务promise2`；在node11.x之后，微任务队列只用等当前这一个宏任务先执行完。

