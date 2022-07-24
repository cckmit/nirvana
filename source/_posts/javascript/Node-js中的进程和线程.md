---
title: Node-js中的进程和线程
date: 2022-07-19 16:33:52
categories: 前端
tags: [node]
---
### 一、进程和线程

#### 1.1、专业性文字定义

*   进程（Process），进程是计算机中的程序关于某数据集合上的一次运行活动，是系统进行资源分配和调度的基本单位，是操作系统结构的基础，进程是线程的容器。
*   线程（Thread），线程是操作系统能够进行运算调度的最小单位，被包含在进程之中，是进程中的实际运作单位。

#### 1.2、通俗理解

以上描述比较硬，看完可能也没看懂，还不利于理解记忆。那么我们举个简单的例子：

假设你是某个快递站点的一名小哥，起初这个站点负责的区域住户不多，收取件都是你一个人。给张三家送完件，再去李四家取件，事情得一件件做，这叫**单线程，所有的工作都得按顺序执行**。
后来这个区域住户多了，站点给这个区域分配了多个小哥，还有个小组长，你们可以为更多的住户服务了，这叫**多线程**，小组长是**主线程**，每个小哥都是一个**线程**。
快递站点使用的小推车等工具，是站点提供的，小哥们都可以使用，并不仅供某一个人，这叫**多线程资源共享。**
站点小推车目前只有一个，大家都需要使用，这叫**冲突**。解决的方法有很多，排队等待或者等其他小哥用完后的通知，这叫**线程同步**。

![](https://upload-images.jianshu.io/upload_images/10024246-fd6c5d352903c98b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

总公司有很多站点，各个站点的运营模式几乎一模一样，这叫**多进程**。总公司叫**主进程**，各个站点叫**子进程**。
总公司和站点之间，以及各个站点互相之间，小推车都是相互独立的，不能混用，这叫**进程间不共享资源**。各站点间可以通过电话等方式联系，这叫**管道**。各站点间还有其他协同手段，便于完成更大的计算任务，这叫**进程间同步**。

还可以看看阮一峰的 [进程与线程的一个简单解释](https://link.segmentfault.com/?enc=DM9hHjS4qhCXlxSn6R%2BsQg%3D%3D.GpHoLnkLkjIOCsyI4tlGZ4ffDqCcgaU1EbnaS4Fwhv1FwETieufYGWKnBNQvoLqX9PHwvQvceerEj4qVRpc8n0BpCuseKTLM8lSBzcxgk%2Fw%3D)。

### 二、Node.js 中的进程和线程

Node.js 是单线程服务，事件驱动和非阻塞 I/O 模型的语言特性，使得 Node.js 高效和轻量。优势在于免去了频繁切换线程和资源冲突；擅长 I/O 密集型操作（底层模块 libuv 通过多线程调用操作系统提供的异步 I/O 能力进行多任务的执行），但是对于服务端的 Node.js，可能每秒有上百个请求需要处理，当面对 CPU 密集型请求时，因为是单线程模式，难免会造成阻塞。

#### 2.1、Node.js 阻塞

我们利用 Koa 简单地搭建一个 Web 服务，用斐波那契数列方法来模拟一下 Node.js 处理 CPU 密集型的计算任务：

> 斐波那契数列，也称黄金分割数列，这个数列从第三项开始，每一项都等于前两项只和：0、1、1、2、3、5、8、13、21、......

```
// app.js
const Koa = require('koa')
const router = require('koa-router')()
const app = new Koa()

// 用来测试是否被阻塞
router.get('/test', (ctx) => {
    ctx.body = {
        pid: process.pid,
        msg: 'Hello World'
    }
})
router.get('/fibo', (ctx) => {
    const { num = 38 } = ctx.query
    const start = Date.now()
    // 斐波那契数列
    const fibo = (n) => {
        return n > 1 ? fibo(n - 1) + fibo(n - 2) : 1
    }
    fibo(num)

    ctx.body = {
        pid: process.pid,
        duration: Date.now() - start
    }
})

app.use(router.routes())
app.listen(9000, () => {
    console.log('Server is running on 9000')
})
```
执行 `node app.js` 启动服务，用 Postman 发送请求，可以看到，计算 38 次耗费了 617ms，换而言之，因为执行了一个 CPU 密集型的计算任务，所以 Node.js 主线程被阻塞了六百多毫秒。如果同时处理更多的请求，或者计算任务更复杂，那么在这些请求之后的所有请求都会被延迟执行。

![](https://upload-images.jianshu.io/upload_images/10024246-0193ab4df02a8832.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们再新建一个 axios.js 用来模拟发送多次请求，此时将 app.js 中的 fibo 计算次数改为 43，用来模拟更复杂的计算任务：

```
// axios.js
const axios = require('axios')

const start = Date.now()
const fn = (url) => {
    axios.get(`http://127.0.0.1:9000/${ url }`).then((res) => {
        console.log(res.data, `耗时: ${ Date.now() - start }ms`)
    })
}

fn('test')
fn('fibo?num=43')
fn('test')
```

![](https://upload-images.jianshu.io/upload_images/10024246-5386d5cce50ebad2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到，当请求需要执行 CPU 密集型的计算任务时，后续的请求都被阻塞等待，这类请求一多，服务基本就阻塞卡死了。对于这种不足，Node.js 一直在弥补。

#### 2.2、master-worker

master-worker 模式是一种并行模式，核心思想是：系统有两个及以上的进程或线程协同工作时，master 负责接收和分配并整合任务，worker 负责处理任务。
![](https://upload-images.jianshu.io/upload_images/10024246-913261fb57b82579.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 2.3、多线程

线程是 CPU 调度的一个基本单位，只能同时执行一个线程的任务，同一个线程也只能被一个 CPU 调用。如果使用的是多核 CPU，那么将无法充分利用 CPU 的性能。

多线程带给我们灵活的编程方式，但是需要学习更多的 Api 知识，在编写更多代码的同时也存在着更多的风险，线程的切换和锁也会增加系统资源的开销。

*   [worker_threads 工作线程](https://link.segmentfault.com/?enc=B7hjggHAF0QIWAmWwTFI%2Fg%3D%3D.WHhU7SKWjIi0%2B9dgjL4CQRbBOpZnTDAzp%2FxPyZcOvn7Z2vjaP3Gr1btDtg%2BvYwCA)，给 Node.js 提供了真正的多线程能力。

worker_threads 是 Node.js 提供的一种多线程 Api。对于执行 CPU 密集型的计算任务很有用，对 I/O 密集型的操作帮助不大，因为 Node.js 内置的异步 I/O 操作比 worker_threads 更高效。worker_threads 中的 Worker，parentPort 主要用于子线程和主线程的消息交互。

将 app.js 稍微改动下，将 CPU 密集型的计算任务交给子线程计算：

```
// app.js
const Koa = require('koa')
const router = require('koa-router')()
const { Worker } = require('worker_threads')
const app = new Koa()

// 用来测试是否被阻塞
router.get('/test', (ctx) => {
    ctx.body = {
        pid: process.pid,
        msg: 'Hello World'
    }
})
router.get('/fibo', async (ctx) => {
    const { num = 38 } = ctx.query
    ctx.body = await asyncFibo(num)
})

const asyncFibo = (num) => {
    return new Promise((resolve, reject) => {
        // 创建 worker 线程并传递数据
        const worker = new Worker('./fibo.js', { workerData: { num } })
        // 主线程监听子线程发送的消息
        worker.on('message', resolve)
        worker.on('error', reject)
        worker.on('exit', (code) => {
            if (code !== 0) reject(new Error(`Worker stopped with exit code ${code}`))
        })
    })
}

app.use(router.routes())
app.listen(9000, () => {
    console.log('Server is running on 9000')
})
```

新增 fibo.js 文件，用来处理复杂计算任务：

```
const { workerData, parentPort } = require('worker_threads')
const { num } = workerData

const start = Date.now()
// 斐波那契数列
const fibo = (n) => {
    return n > 1 ? fibo(n - 1) + fibo(n - 2) : 1
}
fibo(num)

parentPort.postMessage({
    pid: process.pid,
    duration: Date.now() - start
})
```
执行上文的 axios.js，此时将 app.js 中的 fibo 计算次数改为 43，用来模拟更复杂的计算任务：

![](https://upload-images.jianshu.io/upload_images/10024246-e389658e92296c3d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到，将 CPU 密集型的计算任务交给子线程处理时，主线程不再被阻塞，只需等待子线程处理完成后，主线程接收子线程返回的结果即可，其他请求不再受影响。
上述代码是演示创建 worker 线程的过程和效果，实际开发中，请使用线程池来代替上述操作，因为频繁创建线程也会有资源的开销。

> 线程是 CPU 调度的一个基本单位，只能同时执行一个线程的任务，同一个线程也只能被一个 CPU 调用。

我们再回味下，本小节开头提到的线程和 CPU 的描述，此时由于是新的线程，可以在其他 CPU 核心上执行，可以更充分的利用多核 CPU。

#### 2.4、多进程

Node.js 为了能充分利用 CPU 的多核能力，提供了 cluster 模块，cluster 可以通过一个父进程管理多个子进程的方式来实现集群的功能。

*   [child_process 子进程](https://link.segmentfault.com/?enc=VavKZ0QQCIMf0a6KApPVEQ%3D%3D.BaGu2yCEljE2zNYukfqR%2BA6sUbMpmyum0Grt4JaWeYUucR51l6uT%2Bv0crQuUBagj)，衍生新的 Node.js 进程并使用建立的 IPC 通信通道调用指定的模块。
*   [cluster 集群](https://link.segmentfault.com/?enc=IWUhRjs92zYC7bVR3BfNJw%3D%3D.Ne82vqoFo8oQGLJnQyMyyyo8VvDxof7sjSiwEJ1EjVrShTKbcNSEba5KQxYLxlH5)，可以创建共享服务器端口的子进程，工作进程使用 child_process 的 fork 方法衍生。

cluster 底层就是 child_process，master 进程做总控，启动 1 个 agent 进程和 n 个 worker 进程，agent 进程处理一些公共事务，比如日志等；worker 进程使用建立的 IPC（Inter-Process Communication）通信通道和 master 进程通信，和 master 进程共享服务端口。
![](https://upload-images.jianshu.io/upload_images/10024246-2b30d5d2c589e999.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

新增 fibo-10.js，模拟发送 10 次请求：

```
// fibo-10.js
const axios = require('axios')

const url = `http://127.0.0.1:9000/fibo?num=38`
const start = Date.now()

for (let i = 0; i < 10; i++) {
    axios.get(url).then((res) => {
        console.log(res.data, `耗时: ${ Date.now() - start }ms`)
    })
}
```
可以看到，只使用了一个进程，10 个请求慢慢阻塞，累计耗时 15 秒：

![](https://upload-images.jianshu.io/upload_images/10024246-6434694df7476ac2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

接下来，将 app.js 稍微改动下，引入 cluster 模块：

```
// app.js
const cluster = require('cluster')
const http = require('http')
const numCPUs = require('os').cpus().length
// const numCPUs = 10 // worker 进程的数量一般和 CPU 核心数相同
const Koa = require('koa')
const router = require('koa-router')()
const app = new Koa()

// 用来测试是否被阻塞
router.get('/test', (ctx) => {
    ctx.body = {
        pid: process.pid,
        msg: 'Hello World'
    }
})
router.get('/fibo', (ctx) => {
    const { num = 38 } = ctx.query
    const start = Date.now()
    // 斐波那契数列
    const fibo = (n) => {
        return n > 1 ? fibo(n - 1) + fibo(n - 2) : 1
    }
    fibo(num)

    ctx.body = {
        pid: process.pid,
        duration: Date.now() - start
    }
})
app.use(router.routes())

if (cluster.isMaster) {
    console.log(`Master ${process.pid} is running`)

    // 衍生 worker 进程
    for (let i = 0; i < numCPUs; i++) {
        cluster.fork()
    }

    cluster.on('exit', (worker, code, signal) => {
        console.log(`worker ${worker.process.pid} died`)
    })
} else {
    app.listen(9000)
    console.log(`Worker ${process.pid} started`)
}
```
执行 `node app.js` 启动服务，可以看到，cluster 帮我们创建了 1 个 master 进程和 4 个 worker 进程：
![](https://upload-images.jianshu.io/upload_images/10024246-eb193c831c83d8b5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-6791ea1f4200198c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过 fibo-10.js 模拟发送 10 次请求，可以看到，四个进程处理 10 个请求耗时近 9 秒：

![](https://upload-images.jianshu.io/upload_images/10024246-c80581a19abc2fd1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当启动 10 个 worker 进程时，看看效果：

![](https://upload-images.jianshu.io/upload_images/10024246-fabd0d8e515ccb7e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

仅需不到 3 秒，不过进程的数量也不是无限的。在日常开发中，worker 进程的数量一般和 CPU 核心数相同。

#### 2.5、多进程说明

开启多进程不全是为了处理高并发，而是为了解决 Node.js 对于多核 CPU 利用率不足的问题。
由父进程通过 fork 方法衍生出来的子进程拥有和父进程一样的资源，但是各自独立，互相之间资源不共享。通常根据 CPU 核心数来设置进程数量，因为系统资源是有限的。

### 三、总结

1、大部分通过多线程解决 CPU 密集型计算任务的方案都可以通过多进程方案来替代；
2、Node.js 虽然异步，但是不代表不会阻塞，CPU 密集型任务最好不要在主线程处理，保证主线程的畅通；
3、不要一味的追求高性能和高并发，达到系统需要即可，高效、敏捷才是项目需要的，这也是 Node.js 轻量的特点。
4、Node.js 中的进程和线程还有很多概念在文章中提到了但没展开细讲或没提到的，比如：Node.js 底层 I/O 的 libuv、IPC 通信通道、多进程如何守护、进程间资源不共享如何处理定时任务、agent 进程等；
5、以上代码可在 [https://github.com/liuxy0551/node-process-thread](https://link.segmentfault.com/?enc=a5h6oL5R1T%2BWxCQMBkjQQA%3D%3D.TG%2ByWB0WIgqakYnK34fG5ESH%2FoG2jA7Lk1N2%2F7flWfAXnpb0YrLfTHvxdW3BYSoPCSaBi9kZHSznYBbyxbeoxQ%3D%3D) 查看。
