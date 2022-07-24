---
title: 前端模块化：AMD、CMD、CommonJS、ES6
date: 2022-03-02 16:28:03
categories: 前端
---
## 一、什么是promise，及其作用
`Promise`是ES6中的一个内置对象，实际是一个构造函数，是JS中进行异步编程的新的解决方案。
- 特点：
① 三种状态：`pending`（进行中）、`resolved`（已完成）、`rejected`（已失败）。只有异步操作的结果可以决定当前是哪一种状态，任何其他操作都不能改变这个状态。
② 两种状态的转化：其一，从`pending`（进行中）到`resolved`（已完成）。其二，从`pending`（进行中）到`rejected`（已失败）。只有这两种形式的转变。
③ `Promise`构造函数的原型对象上，有`then`()和`catch`()等方法，`then`()第一个参数接收`resolved()`传来的数据，`catch()`第一个参数接收`rejected()`传来的数据
- 作用：
① 通常用来解决异步调用问题
② 解决多层回调嵌套的方案
③ 提高代码可读性、更便于维护

## 二 、Promise 基本流程
![](https://upload-images.jianshu.io/upload_images/10024246-148a5595491593dd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 三、Promise的链式调用
我们可以用 `promise.then()`，`promise.catch()` 和 `promise.finally() `这些方法将进一步的操作与一个变为已敲定状态的 `promise` 关联起来。这些方法还会返回一个新生成的` promise` 对象，这个对象可以被非强制性的用来做链式调用，就像这样：
```
const myPromise =
  (new Promise(myExecutorFunc))
  .then(handleFulfilledA,handleRejectedA)
  .then(handleFulfilledB,handleRejectedB)
  .then(handleFulfilledC,handleRejectedC);

// 或者，这样可能会更好...

const myPromise =
  (new Promise(myExecutorFunc))
  .then(handleFulfilledA)
  .then(handleFulfilledB)
  .then(handleFulfilledC)
  .catch(handleRejectedAny);
```
过早地处理被拒绝的 `promise `会对之后` promise `的链式调用造成影响。不过有时候我们因为需要马上处理一个错误也只能这样做。另一方面，在没有迫切需要的情况下，可以在最后一个`.catch() `语句时再进行错误处理，这种做法更加简单。
## 四、Promise 的基本使用
示例，如果当前时间是偶数就代表成功，否则代表失败

```
// 1. 创建一个新的Promise对象
const p = new Promise((resolve, reject) => { // 执行器函数，同步执行
    // 2. 执行异步操作任务
    setTimeout(() => {
        const time = Date.now() // 如果当前时间是偶数就代表成功，否则代表失败
        // 3.1 如果成功了，调用resolve(value)
        if (time % 2 === 0) {
            resolve('成功的数据，value = ' + time)
        } else {
            // 3.2 如果失败了，调用reject(reason)
            reject('失败的数据，reason = ' + time)
        }

    }, 1000);
})

p.then(value => {
    // 接受得到成功的value数据，专业术语：onResolved
    console.log('成功的回调', value)
}, reason => {
    // 接受得到失败的reason数据，专业术语：onRejected
    console.log('失败的回调', reason)
})
```
## 五、Promise几种常用方法
##### 　　5.1 Promise.all()

　　将多个Promise封装成一个新的Promise，成功时返回的是一个结果数组，失败时，返回的是最先rejected状态的值。

　　使用场景：一次发送多个请求并根据请求顺序获取和使用数据
```
let promise1 = Promise.resolve(1);
  let promise2 = new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('promise2');
    }, 2000);
  })
  let promise3 = new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('promise3');
    }, 1000);
  })
  let promise4 = new Promise((resolve, reject) => {
    setTimeout(() => {
      reject('promise4');
    }, 1000);
  })
  let promise5 = Promise.reject('promise5');

 Promise.all([promise1, promise2, promise3]).then((values) => {
　　//三个promise都是成功的，所以会输出一个数组
    console.log(values)　　　　// [1, "promise2", "promise3"]
  }) 
  Promise.all([promise1, promise2, promise3, promise4]).then((values) => {
    console.log(values)　　
  }, (reason) => {
　　//promise4是失败的，所以只会返回promise4的失败结果
    console.log(reason)　　　　//promise4
  })

  Promise.all([promise1, promise2, promise3, promise4, promise5]).then((values) => {
    console.log(values)
  }, (reason) => {
　　//promise4个promise5都是失败的，但是promise5比promise4最先失败，所以返回promise5的失败结果
    console.log(reason)　　　　//promise5
  })
```
##### 　　5.2 Promise.race()

　　返回一个 promise，一旦迭代器中的某个promise解决或拒绝，返回的 promise就会解决或拒绝。

　　简单来说，就是多个Promise中，哪个状态先变为成功或者失败，就返回哪个Promise的值

```
let promise1 = new Promise((resolve, reject) => {
　　//setTimeout第三个以后的参数是作为第一个`func()`的参数传进去,promise1作为resolve的参数
    setTimeout(resolve, 500, 'promise1');
  });

  let promise2 = new Promise((resolve, reject) => {
    setTimeout(resolve, 100, 'promise2');
  });

  let promise3 = new Promise((resolve, reject) => {
    setTimeout(reject, 500, 'promise3');
  });

  let promise4 = new Promise((resolve, reject) => {
    setTimeout(reject, 100, 'promise4');
  });

  Promise.race([promise1, promise2]).then((value) => {
    console.log(value);     //promise2 100 <promise1 500  所以输出： promise2
  });  

  Promise.race([promise3, promise4]).then((value) => {
  }, (rejected) => {
    console.log(rejected)   //promise4 100 < promise3 500  所以输出： promise4
  });

  Promise.race([promise2, promise3]).then((value) => {
    console.log(value)      //promise2 100 < promise3 500   所以会走成功状态，输出： promise2
  }, (rejected) => {
    console.log(rejected)
  });

  Promise.race([promise1, promise4]).then((value) => {
    console.log(value)
  }, (rejected) => {
    console.log(rejected)   //promise4 100 < promise1 500   所以会走失败状态，输出： promise4
  });
```
##### 5.3  Promise.any()
`Promise.any()` 接收一个`Promise`可迭代对象，只要其中的一个 `promise` 成功，就返回那个已经成功的 `promise` 。如果可迭代对象中没有一个 `promise` 成功（即所有的 `promises` 都失败/拒绝），就返回一个失败的 `promise `和`AggregateError`类型的实例，它是 `Error` 的一个子类，用于把单一的错误集合在一起。本质上，这个方法和`Promise.all()`是相反的。
>这个方法用于返回第一个成功的 `promise` 。只要有一个 `promise` 成功此方法就会终止，它不会等待其他的 `promise` 全部完成。
不像 Promise.all()会返回一组完成值那样（resolved values），我们只能得到一个成功值（假设至少有一个 `promise` 完成）。当我们只需要一个 `promise` 成功，而不关心是哪一个成功时此方法很有用的。
同时, 也不像 Promise.race()总是返回第一个结果值（resolved/reject）那样，这个方法返回的是第一个* 成功的* 值。这个方法将会忽略掉所有被拒绝的 `promise`，直到第一个 `promise` 成功。
```

const pErr = new Promise((resolve, reject) => {
  reject("总是失败");
});

const pSlow = new Promise((resolve, reject) => {
  setTimeout(resolve, 500, "最终完成");
});

const pFast = new Promise((resolve, reject) => {
  setTimeout(resolve, 100, "很快完成");
});

Promise.any([pErr, pSlow, pFast]).then((value) => {
  console.log(value);
  // pFast fulfils first
})
// 期望输出: "很快完成"
```
##### 5.4  Promise.allSettled()
`Promise.allSettled()`方法返回一个在所有给定的promise都已经`fulfilled`或`rejected`后的promise，并带有一个对象数组，每个对象表示对应的promise结果。
当您有多个彼此不依赖的异步任务成功完成时，或者您总是想知道每个`promise`的结果时，通常使用它。
相比之下，`Promise.all()` 更适合彼此相互依赖或者在其中任何一个`reject`时立即结束。
```
const promise1 = Promise.resolve(3);
const promise2 = new Promise((resolve, reject) => setTimeout(reject, 100, 'foo'));
const promises = [promise1, promise2];

Promise.allSettled(promises).
  then((results) => results.forEach((result) => console.log(result.status)));

// expected output:
// "fulfilled"
// "rejected"
```
## 六、什么是Async/Await，及其作用
① async/await是ES7新特性
② async/await是写异步代码的新方式，以前的方法有回调函数和Promise
③ async/await是基于Promise实现的，它不能用于普通的回调函数
④ async/await与Promise一样，是非阻塞的
⑤ async/await使得异步代码看起来像同步代码，这正是它的魔力所在

**async function** 用来定义一个返回 **AsyncFunction** 对象的异步函数。异步函数是指通过事件循环异步执行的函数，它会通过一个隐式的 **Promise** 返回其结果，。如果你在代码中使用了异步函数，就会发现它的语法和结构会更像是标准的同步函数。

**await** 操作符用于等待一个 **Promise** 对象。它只能在异步函数 **async function** 中使用。

#####  6.1  async 函数

**async** 函数的返回值为 Promise 对象，**async** 函数返回的 Promise 的结果由函数执行的结果决定
```
async function fn1() {
    return 1
}
const result = fn1()
console.log(result) // Promise { 1 }
```
既然是 Promise 对象，那么我们用 then 来调用，并抛出错误，执行 **onRejected() **且 reason 为错误信息为“我是错误”

```
async function fn1() {
    // return 1
    // return Promise.resolve(1)
    // return Promise.reject(2)
    throw '我是错误'
}
fn1().then(
    value => { console.log('onResolved()', value) },
    reason => { console.log('onRejected()', reason) } // onRejected() 我是错误
)
```

##### **6.2 await 表达式**

**await** 右侧的表达式一般为 **promise** 对象, 但也可以是其它的值

1.  如果表达式是 promise 对象, await 返回的是 promise 成功的值
2.  如果表达式是其它值, 直接将此值作为 await 的返回值

```
function fn2() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(1000)
        }, 1000);
    })
}
function fn4() { return 6 }
async function fn3() {
    // const value = await fn2() // await 右侧表达式为Promise，得到的结果就是Promise成功的value
    // const value = await '还可以这样'
    const value = await fn4()
    console.log('value', value)
}
fn3() // value 6
```

**await** 必须写在 **async** 函数中, 但 **async** 函数中可以没有 **await**，如果 **await** 的 Promise 失败了, 就会抛出异常, 需要通过 **try...catch** 捕获处理。

```
function fn2() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            // resolve(1000)
            reject(1000)
        }, 1000);
    })
}
async function fn3() {
    try {
        const value = await fn2()
    } catch (error) {
        console.log('得到失败的结果', error)
    }
}
fn3() // 得到失败的结果 1000
```
##  七、区别：
##### 1）简洁的代码
使用async函数可以让代码简洁很多，不需要像Promise一样需要些then，不需要写匿名函数处理Promise的resolve值，也不需要定义多余的data变量，还避免了嵌套代码。
##### 2） 错误处理：
Promise 中不能自定义使用 try/catch 进行错误捕获，但是在 Async/await 中可以像处理同步代码处理错误
```
//#Promise
const makeRequest = () => {
  try {
    getJSON()
      .then(result => {
        // this parse may fail
        const data = JSON.parse(result)
        console.log(data)
      })
      // uncomment this block to handle asynchronous errors
      // .catch((err) => {
      //   console.log(err)
      // })
  } catch (err) {
    console.log(err)
  }
}
//#Async/await
const makeRequest = async () => {
  try {
    // this parse may fail
    const data = JSON.parse(await getJSON())
    console.log(data)
  } catch (err) {
    console.log(err)
  }
}
```
##### 3）条件语句
条件语句也和错误捕获是一样的，在 Async 中也可以像平时一般使用条件语句
```
//#Promise
const makeRequest = () => {
  return getJSON()
    .then(data => {
      if (data.needsAnotherRequest) {
        return makeAnotherRequest(data)
          .then(moreData => {
            console.log(moreData)
            return moreData
          })
      } else {
        console.log(data)
        return data
      }
    })
}

//#Async
const makeRequest = async () => {
  const data = await getJSON()
  if (data.needsAnotherRequest) {
    const moreData = await makeAnotherRequest(data);
    console.log(moreData)
    return moreData
  } else {
    console.log(data)
    return data    
  }
}
```
##### 4）中间值
在一些场景中，也许需要 promise1 去触发 promise2 再去触发 promise3，这个时候代码应该是这样的
```
const makeRequest = () => {
  return promise1()
    .then(value1 => {
      // do something
      return promise2(value1)
        .then(value2 => {
          // do something          
          return promise3(value1, value2)
        })
    })
}
```
如过 promise3 不需要 value1，嵌套将会变得简单。如果你有强迫症，则将值1&2使用 promise.all() 分装起来。
```
const makeRequest = () => {
  return promise1()
    .then(value1 => {
      // do something
      return Promise.all([value1, promise2(value1)])
    })
    .then(([value1, value2]) => {
      // do something          
      return promise3(value1, value2)
    })
}
```
但是使用 Async 就会变得很简单
```
const makeRequest = async () => {
  const value1 = await promise1()
  const value2 = await promise2(value1)
  return promise3(value1, value2)
}
```
##### 错误栈
如过 Promise 连续调用，对于错误的处理是很麻烦的。你无法知道错误出在哪里。
```
const makeRequest = () => {
  return callAPromise()
    .then(() => callAPromise())
    .then(() => callAPromise())
    .then(() => callAPromise())
    .then(() => callAPromise())
    .then(() => {
      throw new Error("oops");
    })
}

makeRequest()
  .catch(err => {
    console.log(err);
    // output
    // Error: oops at callAPromise.then.then.then.then.then (index.js:8:13)
  })
```
但是对于 Async 就不一样了
```
const makeRequest = async () => {
  await callAPromise()
  await callAPromise()
  await callAPromise()
  await callAPromise()
  await callAPromise()
  throw new Error("oops");
}

makeRequest()
  .catch(err => {
    console.log(err);
    // output
    // Error: oops at makeRequest (index.js:7:9)
  })
```
##### 6）调试
async/await能够使得代码调试更简单。2个理由使得调试Promise变得非常痛苦:
- 《1》不能在返回表达式的箭头函数中设置断点
- 《2》如果你在.then代码块中设置断点，使用Step Over快捷键，调试器不会跳到下一个.then，因为它只会跳过异步代码。

使用await/async时，你不再需要那么多箭头函数，这样你就可以像调试同步代码一样跳过await语句。
