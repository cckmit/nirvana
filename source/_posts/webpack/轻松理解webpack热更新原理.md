---
title: 轻松理解webpack热更新原理
date: 2021-02-24 21:12:40
categories: webpack
---
> 苍耳mtjj https://juejin.im/post/5de0cfe46fb9a071665d3df0

## **一、前言 - webpack热更新**

> `Hot Module Replacement`，简称`HMR`，无需完全刷新整个页面的同时，更新模块。`HMR`的好处，在日常开发工作中体会颇深：**节省宝贵的开发时间、提升开发体验**。

刷新我们一般分为两种：

*   一种是页面刷新，不保留页面状态，就是简单粗暴，直接`window.location.reload()`。
*   另一种是基于`WDS (Webpack-dev-server)`的模块热替换，只需要局部刷新页面上发生变化的模块，同时可以保留当前的页面状态，比如复选框的选中状态、输入框的输入等。

`HMR`作为一个`Webpack`内置的功能，可以通过`HotModuleReplacementPlugin`或`--hot`开启。那么，`HMR`到底是怎么实现热更新的呢？下面让我们来了解一下吧！

## **二、webpack的编译构建过程**

项目启动后，进行构建打包，控制台会输出构建过程，我们可以观察到生成了一个 **Hash值**：`a93fd735d02d98633356`。
![](https://upload-images.jianshu.io/upload_images/10024246-6d2f5425bf7da424.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后，在我们每次修改代码保存后，控制台都会出现 `Compiling…`字样，触发新的编译中...可以在控制台中观察到：

*   **新的Hash值**：`a61bdd6e82294ed06fa3`
*   **新的json文件**：`a93fd735d02d98633356.hot-update.json`
*   **新的js文件**：`index.a93fd735d02d98633356.hot-update.js`
![](https://upload-images.jianshu.io/upload_images/10024246-0c90e436d80d9a0e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

首先，我们知道`Hash`值代表每一次编译的标识。其次，根据新生成文件名可以发现，上次输出的`Hash`值会作为本次编译新生成的文件标识。依次类推，本次输出的`Hash`值会被作为下次热更新的标识。

然后看一下，新生成的文件是什么？每次修改代码，紧接着触发重新编译，然后浏览器就会发出 2 次请求。请求的便是本次新生成的 2 个文件。如下：

![](https://upload-images.jianshu.io/upload_images/10024246-483919da04108923.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

首先看`json`文件，返回的结果中，`h`代表本次新生成的`Hash`值，用于下次文件热更新请求的前缀。`c`表示当前要热更新的文件对应的是`index`模块。再看下生成的`js`文件，那就是本次修改的代码，重新编译打包后的。

![](https://upload-images.jianshu.io/upload_images/10024246-d757fe02ea725128.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

还有一种情况是，如果没有任何代码改动，直接保存文件，控制台也会输出编译打包信息的。

*   **新的Hash值**：`d2e4208eca62aa1c5389`
*   **新的json文件**：`a61bdd6e82294ed06fa3.hot-update.json`

![](https://upload-images.jianshu.io/upload_images/10024246-321d0cf07a4b7d0d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

但是我们发现，并没有生成新的`js`文件，因为没有改动任何代码，同时浏览器发出的请求，可以看到`c`值为空，代表本次没有需要更新的代码。

![](https://upload-images.jianshu.io/upload_images/10024246-3c21ec10c942b727.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

小声说下，`webapck`以前的版本这种情况`hash`值是不会变的，后面可能出于什么原因改版了。细节不用在意，了解原理才是真谛!!!

最后思考下?，浏览器是如何知道本地代码重新编译了，并迅速请求了新生成的文件？是谁告知了浏览器？浏览器获得这些文件又是如何热更新成功的？那让我们带着疑问看下热更新的过程，从源码的角度看原理。

## **三、热更新实现原理**

相信大家都会配置`webpack-dev-server`热更新，我就不示意例子了。自己网上查下即可。接下来我们就来看下`webpack-dev-server`是如何实现热更新的？（源码都是精简过的，第一行会注明代码路径，看完最好结合源码食用一次）。

### **1\. webpack-dev-server启动本地服务**

我们根据`webpack-dev-server`的`package.json`中的`bin`命令，可以找到命令的入口文件`bin/webpack-dev-server.js`。

```
    // node_modules/webpack-dev-server/bin/webpack-dev-server.js// 生成webpack编译主引擎 compilerlet compiler = webpack(config);

    // 启动本地服务let server = new Server(compiler, options, log);
    server.listen(options.port, options.host, (err) => {
        if (err) {throw err};
    });
```

本地服务代码：

```
    // node_modules/webpack-dev-server/lib/Server.jsclassServer{
        constructor() {
            this.setupApp();
            this.createServer();
        }

        setupApp() {
            // 依赖了expressthis.app = new express();
        }

        createServer() {
            this.listeningApp = http.createServer(this.app);
        }
        listen(port, hostname, fn) {
            returnthis.listeningApp.listen(port, hostname, (err) => {
                // 启动express服务后，启动websocket服务this.createSocketServer();
            }
        }
    }
```

这一小节代码主要做了三件事：

*   启动`webpack`，生成`compiler`实例。`compiler`上有很多方法，比如可以启动 `webpack` 所有**编译**工作，以及**监听**本地文件的变化。
*   使用`express`框架启动本地`server`，让浏览器可以请求本地的**静态资源**。
*   本地`server`启动之后，再去启动`websocket`服务，如果不了解`websocket`，建议简单了解一下websocket速成。通过`websocket`，可以建立本地服务和浏览器的双向通信。这样就可以实现当本地文件发生变化，立马告知浏览器可以热更新代码啦！

上述代码主要干了三件事，但是源码在启动服务前又做了很多事，接下来便看看`webpack-dev-server/lib/Server.js`还做了哪些事？

### **2\. 修改webpack.config.js的entry配置**

启动本地服务前，调用了`updateCompiler(this.compiler)`方法。这个方法中有 2 段关键性代码。一个是获取`websocket`客户端代码路径，另一个是根据配置获取`webpack`热更新代码路径。

```
    // 获取websocket客户端代码const clientEntry = `${require.resolve(
        '../../client/'
    )}?${domain}${sockHost}${sockPath}${sockPort}`;

    // 根据配置获取热更新代码let hotEntry;
    if (options.hotOnly) {
        hotEntry = require.resolve('webpack/hot/only-dev-server');
    } elseif (options.hot) {
        hotEntry = require.resolve('webpack/hot/dev-server');
    }
```

修改后的`webpack`入口配置如下：

```
    // 修改后的entry入口
    { entry:
        { index:
            [
                // 上面获取的clientEntry'xxx/node_modules/webpack-dev-server/client/index.js?http://localhost:8080',
                // 上面获取的hotEntry'xxx/node_modules/webpack/hot/dev-server.js',
                // 开发配置的入口'./src/index.js'
        	],
        },
    }
```

为什么要新增了 2 个文件？在入口默默增加了 2 个文件，那就意味会一同打包到`bundle`文件中去，也就是线上运行时。

**（1）webpack-dev-server/client/index.js**

首先这个文件用于`websocket`的，因为`websoket`是双向通信，如果不了解`websocket`，建议简单了解一下websocket速成。我们在第 1 步 `webpack-dev-server`初始化 的过程中，启动的是本地服务端的`websocket`。那客户端也就是我们的浏览器，浏览器还没有和服务端通信的代码呢？总不能让开发者去写吧hhhhhh。因此我们需要把`websocket`客户端通信代码偷偷塞到我们的代码中。客户端具体的代码后面会在合适的时机细讲哦。

**（2）webpack/hot/dev-server.js**

这个文件主要是用于检查更新逻辑的，这里大家知道就好，代码后面会在合适的时机（**第5步**）细讲。

### **3\. 监听webpack编译结束**

修改好入口配置后，又调用了`setupHooks`方法。这个方法是用来注册监听事件的，监听每次`webpack`编译完成。

```
    // node_modules/webpack-dev-server/lib/Server.js// 绑定监听事件
    setupHooks() {
        const {done} = compiler.hooks;
        // 监听webpack的done钩子，tapable提供的监听方法
        done.tap('webpack-dev-server', (stats) => {
            this._sendStats(this.sockets, this.getStats(stats));
            this._stats = stats;
        });
    };
```

当监听到一次`webpack`编译结束，就会调用`_sendStats`方法通过`websocket`给浏览器发送通知，`ok`和`hash`事件，这样浏览器就可以拿到最新的`hash`值了，做检查更新逻辑。

```
    // 通过websoket给客户端发消息
    _sendStats() {
        this.sockWrite(sockets, 'hash', stats.hash);
        this.sockWrite(sockets, 'ok');
    }
```

### **4\. webpack监听文件变化**

每次修改代码，就会触发编译。说明我们还需要监听本地代码的变化，主要是通过`setupDevMiddleware`方法实现的。

这个方法主要执行了`webpack-dev-middleware`库。很多人分不清`webpack-dev-middleware`和`webpack-dev-server`的区别。其实就是因为`webpack-dev-server`只负责启动服务和前置准备工作，所有文件相关的操作都抽离到`webpack-dev-middleware`库了，主要是本地文件的**编译**和**输出**以及**监听**，无非就是职责的划分更清晰了。

那我们来看下`webpack-dev-middleware`源码里做了什么事:

```
    // node_modules/webpack-dev-middleware/index.js
    compiler.watch(options.watchOptions, (err) => {
        if (err) { /*错误处理*/ }
    });

    // 通过“memory-fs”库将打包后的文件写入内存
    setFs(context, compiler);
```

（1）调用了`compiler.watch`方法，在第 1 步中也提到过，`compiler`的强大。这个方法主要就做了 2 件事：

*   首先对本地文件代码进行编译打包，也就是`webpack`的一系列编译流程。
*   其次编译结束后，开启对本地文件的监听，当文件发生变化，重新编译，编译完成之后继续监听。

为什么代码的改动保存会自动编译，重新打包？这一系列的重新检测编译就归功于`compiler.watch`这个方法了。监听本地文件的变化主要是通过**文件的生成时间**是否有变化，这里就不细讲了。

（2）执行`setFs`方法，这个方法主要目的就是将编译后的文件打包到内存。这就是为什么在开发的过程中，你会发现`dist`目录没有打包后的代码，因为都在内存中。原因就在于访问内存中的代码比访问文件系统中的文件更快，而且也减少了代码写入文件的开销，这一切都归功于`memory-fs`。

### **5\. 浏览器接收到热更新的通知**

我们已经可以监听到文件的变化了，当文件发生变化，就触发重新编译。同时还监听了每次编译结束的事件。当监听到一次`webpack`编译结束，`_sendStats`方法就通过`websoket`给浏览器发送通知，检查下是否需要热更新。下面重点讲的就是`_sendStats`方法中的`ok`和`hash`事件都做了什么。

那浏览器是如何接收到`websocket`的消息呢？回忆下第 2 步骤增加的入口文件，也就是`websocket`客户端代码。

```
    'xxx/node_modules/webpack-dev-server/client/index.js?http://localhost:8080'
```

这个文件的代码会被打包到`bundle.js`中，运行在浏览器中。来看下这个文件的核心代码吧。

```
    // webpack-dev-server/client/index.jsvar socket = require('./socket');
    var onSocketMessage = {
        hash: functionhash(_hash) {
            // 更新currentHash值
            status.currentHash = _hash;
        },
        ok: functionok() {
            sendMessage('Ok');
            // 进行更新检查等操作
            reloadApp(options, status);
        },
    };
    // 连接服务地址socketUrl，?http://localhost:8080，本地服务地址
    socket(socketUrl, onSocketMessage);

    functionreloadApp() {
    	if (hot) {
            log.info('[WDS] App hot update...');

            // hotEmitter其实就是EventEmitter的实例var hotEmitter = require('webpack/hot/emitter');
            hotEmitter.emit('webpackHotUpdate', currentHash);
        }
    }
```

`socket`方法建立了`websocket`和服务端的连接，并注册了 2 个监听事件。

*   `hash`事件，更新最新一次打包后的`hash`值。
*   `ok`事件，进行热更新检查。

热更新检查事件是调用`reloadApp`方法。比较奇怪的是，这个方法又利用`node.js`的`EventEmitter`，发出`webpackHotUpdate`消息。这是为什么？为什么不直接进行检查更新呢？

个人理解就是为了更好的维护代码，以及职责划分的更明确。`websocket`仅仅用于客户端（浏览器）和服务端进行通信。而真正做事情的活还是交回给了`webpack`。

那`webpack`怎么做的呢？再来回忆下第 2 步。入口文件还有一个文件没有讲到，就是：

```
    'xxx/node_modules/webpack/hot/dev-server.js'
```

这个文件的代码同样会被打包到`bundle.js`中，运行在浏览器中。这个文件做了什么就显而易见了吧！先瞄一眼代码：

```
    // node_modules/webpack/hot/dev-server.jsvar check = functioncheck() {
        module.hot.check(true)
            .then(function(updatedModules) {
                // 容错，直接刷新页面if (!updatedModules) {
                    window.location.reload();
                    return;
                }

                // 热更新结束，打印信息if (upToDate()) {
                    log("info", "[HMR] App is up to date.");
                }
        })
            .catch(function(err) {
                window.location.reload();
            });
    };

    var hotEmitter = require("./emitter");
    hotEmitter.on("webpackHotUpdate", function(currentHash) {
        lastHash = currentHash;
        check();
    });
```

这里`webpack`监听到了`webpackHotUpdate`事件，并获取最新了最新的`hash`值，然后终于进行检查更新了。检查更新呢调用的是`module.hot.check`方法。那么问题又来了，`module.hot.check`又是哪里冒出来了的！答案是`HotModuleReplacementPlugin`搞得鬼。这里留个疑问，继续往下看。

### **6\. HotModuleReplacementPlugin**

前面好像一直是`webpack-dev-server`做的事，那`HotModuleReplacementPlugin`在热更新过程中又做了什么伟大的事业呢？

首先你可以对比下，配置热更新和不配置时`bundle.js`的区别。内存中看不到？直接执行`webpack`命令就可以看到生成的`bundle.js`文件啦。不要用`webpack-dev-server`启动就好了。

（1）没有配置的。

![](https://upload-images.jianshu.io/upload_images/10024246-3f953bbd497a0990.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

（2）配置了`HotModuleReplacementPlugin`或`--hot`的。

![](https://upload-images.jianshu.io/upload_images/10024246-a0344843dd99a7da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

哦~ 我们发现`moudle`新增了一个属性为`hot`，再看`hotCreateModule`方法。这不就找到`module.hot.check`是哪里冒出来的。

![](https://upload-images.jianshu.io/upload_images/10024246-77ee88d5ce388341.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

经过对比打包后的文件，`__webpack_require__`中的`moudle`以及代码行数的不同。我们都可以发现`HotModuleReplacementPlugin`原来也是默默的塞了很多代码到`bundle.js`中呀。这和第 2 步骤很是相似哦！为什么，因为检查更新是在浏览器中操作呀。这些代码必须在运行时的环境。

你也可以直接看浏览器`Sources`下的代码，会发现`webpack`和`plugin`偷偷加的代码都在哦。在这里调试也很方便。

![](https://upload-images.jianshu.io/upload_images/10024246-382e6f22e718f071.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`HotModuleReplacementPlugin`如何做到的？这里我就不讲了，因为这需要你对`tapable`以及`plugin`机制有一定了解，可以看下我写的文章Webpack插件机制之Tapable-源码解析。当然你也可以选择跳过，只关心热更新机制即可，毕竟信息量太大。

### **7\. moudle.hot.check 开始热更新**

通过第 6 步，我们就可以知道`moudle.hot.check`方法是如何来的啦。那都做了什么？之后的源码都是`HotModuleReplacementPlugin`塞入到`bundle.js`中的哦，我就不写文件路径了。

*   利用上一次保存的`hash`值，调用`hotDownloadManifest`发送`xxx/hash.hot-update.json`的`ajax`请求；
*   请求结果获取热更新模块，以及下次热更新的`Hash` 标识，并进入热更新准备阶段。

```
    hotAvailableFilesMap = update.c; // 需要更新的文件
    hotUpdateNewHash = update.h; // 更新下次热更新hash值
    hotSetStatus("prepare"); // 进入热更新准备状态
```

*   调用`hotDownloadUpdateChunk`发送`xxx/hash.hot-update.js` 请求，通过`JSONP`方式。

```
   functionhotDownloadUpdateChunk(chunkId) {
        var script = document.createElement("script");
        script.charset = "utf-8";
        script.src = __webpack_require__.p + "" + chunkId + "." + hotCurrentHash + ".hot-update.js";
        if (null) script.crossOrigin = null;
        document.head.appendChild(script);
     }
```

这个函数体为什么要单独拿出来，因为这里要解释下为什么使用`JSONP`获取最新代码？主要是因为`JSONP`获取的代码可以直接执行。为什么要直接执行？我们来回忆下`/hash.hot-update.js`的代码格式是怎么样的。

![](https://upload-images.jianshu.io/upload_images/10024246-8ab50b4ec1038d8f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以发现，新编译后的代码是在一个`webpackHotUpdate`函数体内部的。也就是要立即执行`webpackHotUpdate`这个方法。

再看下`webpackHotUpdate`这个方法。

```
    window["webpackHotUpdate"] = function (chunkId, moreModules) {
        hotAddUpdateChunk(chunkId, moreModules);
    } ;
```

*   `hotAddUpdateChunk`方法会把更新的模块`moreModules`赋值给全局全量`hotUpdate`。
*   `hotUpdateDownloaded`方法会调用`hotApply`进行代码的替换。

```
    functionhotAddUpdateChunk(chunkId, moreModules) {
        // 更新的模块moreModules赋值给全局全量hotUpdatefor (var moduleId in moreModules) {
            if (Object.prototype.hasOwnProperty.call(moreModules, moduleId)) {
    	    hotUpdate[moduleId] = moreModules[moduleId];
            }
        }
        // 调用hotApply进行模块的替换
        hotUpdateDownloaded();
    }
```

### **8\. hotApply 热更新模块替换**

热更新的核心逻辑就在`hotApply`方法了。`hotApply`代码有将近 400 行，还是挑重点讲了，看哭?

#### **①删除过期的模块，就是需要替换的模块**

通过`hotUpdate`可以找到旧模块

```
    var queue = outdatedModules.slice();
    while (queue.length > 0) {
        moduleId = queue.pop();
        // 从缓存中删除过期的模块module = installedModules[moduleId];
        // 删除过期的依赖delete outdatedDependencies[moduleId];

        // 存储了被删掉的模块id，便于更新代码
        outdatedSelfAcceptedModules.push({
            module: moduleId
        });
    }
```

#### **②将新的模块添加到 modules 中**

```
    appliedUpdate[moduleId] = hotUpdate[moduleId];
    for (moduleId in appliedUpdate) {
        if (Object.prototype.hasOwnProperty.call(appliedUpdate, moduleId)) {
            modules[moduleId] = appliedUpdate[moduleId];
        }
    }
```

#### **③通过__webpack_require__执行相关模块的代码**

```
   for (i = 0; i < outdatedSelfAcceptedModules.length; i++) {
        var item = outdatedSelfAcceptedModules[i];
        moduleId = item.module;
        try {
            // 执行最新的代码
            __webpack_require__(moduleId);
        } catch (err) {
            // ...容错处理
        }
    }

```

`hotApply`的确比较复杂，知道大概流程就好了，这一小节，要求你对webpack打包后的文件如何执行的有一些了解，大家可以自去看下。

## **四、总结**

还是以阅读源码的形式画的图，①-④的小标记，是文件发生变化的一个流程。

![](https://upload-images.jianshu.io/upload_images/10024246-bb48f4a9a70952b0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## **写在最后**

本次是以阅读源码的方式讲解原理，是因为觉得热更新这块涉及的知识量比较多。所以知识把关键性代码拿出来，因为每一个块细节说起来都能写一篇文章了，大家可以自己对着源码再理解下。

还是建议提前了解以下知识会更好理解热更新：

*   **websocket**：websocket基础知识了解
*   打包后的`bundle`文件如何运行的。
*   `webpack`启动流程，`webpack`生命周期。
*   **tapable**: Webpack插件机制之Tapable-源码解析

## **参考链接**

*   Webpack Hot Module Replacement 的原理解析
*   看完这篇，面试再也不怕被问 Webpack 热更新

参考的文章大家也可以看下，但是由于源码版本不同，所以不要太纠结与细节。

