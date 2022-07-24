---
title: happy pack 原理解析
date: 2021-04-21 14:43:49
categories: webpack
---
## 前言

当 webpack 打包速度很慢的时候，我们想过很多办法去优化打包速度，**[happypack](https://segmentfault.com/a/1190000021037299?utm_source=tag-newest#)** 就是一个用来加速打包的插件。

本质上， **happypack** 是用通过 js 的多进程来实现打包加速，需要注意的是，创建子进程和子进程和主进程之间通信也是有开销的，当你的 **loader** 很慢的时候，可以加上 **happypack**，否则，可能会编译的更慢！

## happypack 加载入口

![](https://upload-images.jianshu.io/upload_images/10024246-69d6936ccc37b38f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
HappyPack位于webpack和您的主要源文件（例如JS源）之间，在该文件中，大量的加载程序发生转换。每次webpack解析模块时，HappyPack都会获取该模块及其所有依赖项，并将这些文件分发到多个工作程序“线程”。

### webpack 的配置

```
var HappyPack = require('happypack');
var happyThreadPool = HappyPack.ThreadPool({ size: os.cpus().length });

...
module: {
        rules: [
            {
                test: /\.(js?|tsx?|ts?)$/,
                include: [
                    path.resolve(__dirname, 'src'),
                ],
                use: [
                    {
                        loader: 'happypack/loader?id=happyBabel',
                    },
                ],
            },
...
plugins: [
        new HappyPack({
            id: 'happyBabel',
            loaders: ['babel-loader'],
            threadPool: happyThreadPool,
            verbose: true,
        })
]
...
```

可以看到 happypack 是通过 **[loader](https://segmentfault.com/a/1190000021037299?utm_source=tag-newest#)** 调用 **[plugin](https://segmentfault.com/a/1190000021037299?utm_source=tag-newest#)** 来打成插件的目的。**loader** 指向 **HappyLoader.js** , loader 执行的时候，根据 **?** 后面的 **id** 来找到对应的插件。

## happypack 解析过程

### 参数初始化

在我们指定的 happypack loader 加载之前，webpack 会根据 option 初始化配置信息之后再去执行 loader。

```
// HappyPlugin.js
this.name = 'HappyPack';
  this.state = {
    loaders: [],
    baseLoaderRequest: '',
    foregroundThreadPool: null,
    verbose: false,
    debug: false,
  };

  this.config = OptionParser(userConfig, {
    id:                       { type: 'string', default: '1' },
    compilerId:               { type: 'string', default: 'default' },
    tempDir:                  { deprecated: true },
    threads:                  { type: 'number', default: 3 },
    threadPool:               { type: 'object', default: null },
    verbose:                  { type: 'boolean', default: true },
    verboseWhenProfiling:     { type: 'boolean', default: false },
    debug:                    { type: 'boolean', default: process.env.DEBUG === '1' },
    enabled:                  { deprecated: true },
    // we don't want this to be documented / exposed since it's an
    // implementation detail + not having it on means a bug, but we're making it
    // configurable for testing purposes
    bufferedMessaging:        { type: 'boolean', default: process.platform === 'win32' },
    loaders:                  {
      validate: function(value) {
        if (!Array.isArray(value)) {
          return 'Loaders must be an array!';
        }
        else if (value.length === 0) {
          return 'You must specify at least one loader!';
        }
        else if (value.some(function(loader) {
          return typeof loader !== 'string' && !loader.path && !loader.loader;
        })) {
          return 'Loader must have a @path or @loader property or be a string.'
        }
      },
    }
  }, "HappyPack");

  this.id = this.config.id;
```

**OptionParser **方法是一个参数校验，初始化的方法
参数说明:

*   id: 在配置文件中设置的与 loader 关联的 id 首先会设置到实例上，为了后续 loader 与 plugin 能进行一对一匹配，在 *** HappyLoader.js** 中有相对应的判断
*   compilerId: 用于查找当前 webpack compioler 对象的上下文，默认 default
*   tmpDir: 存放打包缓存文件的位置
*   verbose: 是否输出过程日志
*   debug: 是否输出父子进程之间的 debug 信息
*   bufferedMessaging: 在 windows 系统中是否是通过 buffered 传输
*   loaders: 因为配置中文件的处理 loader 都指向了 happypack 提供的 loader ,这里配置的对应文件实际需要运行的 loader

### 进程池创建

```
// HappyPlugin.prototype.apply
this.threadPool = this.config.threadPool || HappyThreadPool({
    id: this.id,
    size: this.config.threads,
    verbose: this.state.verbose,
    debug: this.state.debug,
    bufferedMessaging: this.config.bufferedMessaging,
});
```

#### rpcHandler

在创建子进程之前会生成一个 rpcHandler 对象, rpcHandler 是 **HappyRPCHandler **的实例
*HappyRPCHandler.js*

```
function HappyRPCHandler() {
  this.activeLoaders = new SharedPtrMap();
  this.activeCompilers = new SharedPtrMap();
}
```

*SharedPtrMap* 给 *activeLoaders* 和 *activeCompilers* 定义了set、get 和 delete 方法，用于其他方法赋值

```
HappyRPCHandler.prototype.registerActiveCompiler = function(id, compiler) {
  this.activeCompilers.set(id || DEFAULT_COMPILER_ID, compiler);
};
HappyRPCHandler.prototype.unregisterActiveCompiler = function(id) {
  this.activeCompilers.delete(id || DEFAULT_COMPILER_ID);
};

HappyRPCHandler.prototype.registerActiveLoader = function(id, instance) {
  this.activeLoaders.set(id || DEFAULT_LOADER_ID, instance);
};

HappyRPCHandler.prototype.unregisterActiveLoader = function(id) {
  this.activeLoaders.delete(id || DEFAULT_LOADER_ID);
};
```

**rpcHandler** 的主要作用是: 绑定当前运行的 loader 与 compiler ，同时在文件中，针对 loader 与 compiler 定义调用接口:

```
COMPILER_RPCs = {
  resolve: function(compiler, payload, done) {
    var resolver = compiler.resolvers.normal;
    var resolve = compiler.resolvers.normal.resolve;
    ...
    resolve.call(resolver, payload.context, payload.context, payload.resource, done);
  },
};
```

```
LOADER_RPCS = {
  emitWarning: function(loader, payload) {
    loader.emitWarning(payload.message);
  },
  emitError: function(loader, payload) {
      loader.emitError(payload.message);
  },
  addDependency: function(loader, payload) {
      loader.addDependency(payload.file);
  },
  addContextDependency: function(loader, payload) {
      loader.addContextDependency(payload.file);
  },
};
```

#### 创建子进程

*HappyThreadPool.js*

```
  var threads = createThreads(config.size, rpcHandler, {
    id: config.id,
    verbose: config.verbose,
    debug: config.debug,
    buffered: config.hasOwnProperty('bufferedMessaging') ?
      config.bufferedMessaging :
      process.platform === 'win32',
  });
  ...
  ...
function createThreads(count, rpcHandler, config) {
  var set = []

  for (var threadId = 0; threadId < count; ++threadId) {
    var fullThreadId = config.id ? [ config.id, threadId ].join(':') : threadId;
    set.push(HappyThread(fullThreadId, rpcHandler, config));
  }

  return set;
}
```

**threads** 为 **HappyThread** 返回的操作子进程的对象

*HappyThread.js*

```
var WORKER_BIN = path.resolve(__dirname, 'HappyWorkerChannel.js');
....
 return {
    open: function(onReady) {
      var emitReady = Once(onReady);

      fd = fork(WORKER_BIN, [id, JSON.stringify({ buffered: config.buffered })], {
        // Do not pass through any arguments that were passed to the main
        // process (webpack or node) because they could have unwanted
        // side-effects, see issue #47
        execArgv: [],
      });

      fd.on('error', throwError);
      fd.on('exit', function(exitCode) {
        if (exitCode !== 0) {
          emitReady('HappyPack: worker exited abnormally with code ' + exitCode);
        }
      });

      fd.on('message', function(message) {

      });
    },

    configure: function(compilerId, compilerOptions, done) {

    },

    /**
     * @param {Object} params
     * @param {String} params.compiledPath
     * @param {Object} params.loaderContext
     *
     * @param {Function} done
     */
    compile: function(params, done) {

    },

    isOpen: function() {
      return !!fd;
    },

    close: function() {

    },
  };
}
```

到目前为止，子进程已经创建完成。

### 初始化

我们回到 **HappyPlugin.js**，

```
compiler.plugin('watch-run', function(_, done) {
    if (engageWatchMode() === fnOnce.ALREADY_CALLED) {
      done();
    }
    else {
      that.start(compiler, done);
    }
});

compiler.plugin('run', that.start.bind(that));

```

看到这里在 **run** 和 **watch-tun** 两个 **[钩子](https://segmentfault.com/a/1190000021037299?utm_source=tag-newest#)** 中调用了 this.start 进行初始化.

```
HappyPlugin.prototype.start = function(compiler, done) {
  var that = this;
  ...
  async.series([
    function resolveLoaders(callback) {},
    function launchAndConfigureThreads(callback) {},
    function announceReadiness(callback) {}
  ], done);
};
```

start函数通过 **[async.series](https://segmentfault.com/a/1190000021037299?utm_source=tag-newest#)** 将整个过程串联起来。

1） resolveLoaders
loader 解析，把 loaders 和 baseLoaderRequest 塞到 this.state 里面

```
function resolveLoaders(callback) {
      var normalLoaders = that.config.loaders.reduce(function(list, entry) {
        return list.concat(WebpackUtils.normalizeLoader(entry));
      }, []);

      var loaderPaths = normalLoaders.map(function(loader) {
        return loader.path;
      });

      WebpackUtils.resolveLoaders(compiler, loaderPaths, function(err, resolvedPaths) {
        if (err) return callback(err);

        var withResolvedPaths = normalLoaders.map(function(loader, index) {
          var resolvedPath = resolvedPaths[index];

          return Object.assign({}, loader, {
            path: resolvedPath,
            request: loader.query ? (loader.path + loader.query) : loader.path
          })
        })

        that.state.loaders = withResolvedPaths;
        that.state.baseLoaderRequest = withResolvedPaths.map(function(loader) {
          return loader.path + (loader.query || '');
        }).join('!');

        callback();
      });
    },
```

2） launchAndConfigureThreads
启动和初始化进程

```
that.threadPool.start(that.config.compilerId, compiler, serializedOptions, callback);
```

这里调用到了 *HappyThreadPool.js* 里的 **start** 方法
that.threadPool 是进程池创建得到的对象
参数说明：

*   that.config.compilerId: 用于查找当前 webpack compioler 对象的上下文，默认 default
*   compiler： 当前上下文compiler对象
*   serializedOptions: webpack的入参，例如 webpack.common.js 的参数
*   callback: 可以理解为下一步，**async** 库的使用方法

** 第一步：registerActiveCompiler: `RPCHandler` 绑定 `compiler`

```
rpcHandler.registerActiveCompiler(compilerId, compiler);
```

** 第二步: 找到当前没打开的子进程，调用 `open` fork 一份子进程
*HappyThreadPool.js*

```
async.parallel(threads.filter(not(send('isOpen'))).map(get('open'))
```

*HappyThread.js*

```
open: function(onReady) {
      var emitReady = Once(onReady);

      fd = fork(WORKER_BIN, [id, JSON.stringify({ buffered: config.buffered })], {
        execArgv: [],
      });

      fd.on('error', throwError);
      fd.on('exit', function(exitCode) {
        if (exitCode !== 0) {
          emitReady('HappyPack: worker exited abnormally with code ' + exitCode);
        }
      });

      fd.on('message', function(message) {
        // message 判断
      });
    },
```

子进程运行文件 `WORKER_BIN` 对应的是 `HappyWorkerChannel.js`

```
var HappyWorker = require('./HappyWorker');

if (process.argv[1] === __filename) {
  startAsWorker();
}

function startAsWorker() {
  HappyWorkerChannel(String(process.argv[2]), process);
}

function HappyWorkerChannel(id, fd, config) {
  var fakeCompilers = {};
  var workers = {};

  fd.on('message', accept)
  send({ name: 'READY' });

  function accept(message) {
   // 省略函数内容
  }
}
```

通过 *fd.on('message', accept)* 来监听主进程发送过来的消息

** 第三步: 子进程都生成之后，调用 `configure` 初始化

```
async.parallel(threads.map(function(thread) {
  return function(callback) {
    thread.configure(compilerId, compilerOptions, callback);
  }
}), done);
```

同样的，来到了 *HappyThread.js* 的 **configure **方法,

```
configure: function(compilerId, compilerOptions, done) {
  var messageId = generateMessageId();

  callbacks[messageId] = done;

  send({
    id: messageId,
    name: 'CONFIGURE',
    data: {
      compilerId: compilerId,
      compilerOptions: compilerOptions
    }
  });
},
```

给子进程发送了一条 `CONFIGURE` 消息，*HappyWorkerChannel* 接收到做了如下处理

```
findOrCreateFakeCompiler(message.data.compilerId)
    .configure(JSONSerializer.deserialize(message.data.compilerOptions));

send({
    id: message.id,
    name: 'CONFIGURE_DONE'
});
```

**findOrCreateFakeCompiler** 方法给 `workers` 和 `fakeCompiler` 赋值

*   fakeCompilers 是根据接收到的 messageId 作为key，来生成对应的模拟编译环境, 可以理解为为了模拟执行 loader 而模拟出来的上下文
*   workers 是对应的子进程

同时调用 `fakeCompiler` 的 configure 来初始化默认的 webpack 配置和编译上下文。

至此，happyPack 的初始化工作全部做完。

### 开始编译

#### 入口

在 webpack 流程中，在源码文件完成内容读取之后，开始进入到 loader 的编译执行阶段，这时 HappyLoader 作为编译逻辑入口，开始进行编译流程。

*HappyLoader*

```
function HappyLoader(sourceCode, sourceMap) {
  var query, compilerId, loaderId, remoteLoaderId, happyPlugin;
  var callback = this.async();
  var pluginList = locatePluginList(this);

  query = loaderUtils.getOptions(this) || {};
  compilerId = query.compilerId || DEFAULT_COMPILER_ID;
  loaderId = query.id || DEFAULT_LOADER_ID;
  remoteLoaderId = 'Loader::' + compilerId + loaderId.toString() + ':' + this.resource;

  happyPlugin = pluginList.filter(isHappy(loaderId))[0];

  happyPlugin.compile(this, addWebpack2Context(this, {
    compilerId: compilerId,
    context: this.context,
    minimize: this.minimize,
    remoteLoaderId: remoteLoaderId,
    request: happyPlugin.generateRequest(this.resource),
    resource: this.resource,
    resourcePath: this.resourcePath,
    resourceQuery: this.resourceQuery,
    sourceCode: sourceCode,
    sourceMap: sourceMap,
    target: this.target,
    useSourceMap: this._module.useSourceMap,
  }), callback);
}
```

根据 loader 配置 `？`后面的参数找到对应的插件。
同时将原本的 **loaderContext(this指向)** 对象的一些参数例如 **this.resource**、**this.resourcePath** 等透传到 **HappyPlugin.compile** 方法进行编译

#### 编译

编译的起始位置在 **HappyPlugin** 的 **compile** 方法

*HappyPlugin.js*

```
HappyPlugin.prototype.compile = function(loader, loaderContext, done) {
  var threadPool = this.state.foregroundThreadPool || this.threadPool;

  threadPool.compile(loaderContext.remoteLoaderId, loader, {
    loaders: this.state.loaders,
    loaderContext: loaderContext,
  }, function(err, result) {
    if (err) {
      done(ErrorSerializer.deserialize(err));
    }
    else {
      done(null,
        result.compiledSource || '',
        SourceMapSerializer.deserialize(result.compiledMap)
      );
    }
  });
};
```

这里调用了进程池的 compile
*HappyThreadPool.js*

```
compile: function(loaderId, loader, params, done) {
  var worker = getThread();

  rpcHandler.registerActiveLoader(loaderId, loader);

  worker.compile(params, function(message) {
    rpcHandler.unregisterActiveLoader(loaderId);

    done(message.error, message.data);
  });
},
```

这里第一步是给 **rpcHandler** 注册了当前的 loader 信息
第二步 通过**getThread**找到了对应的进程，调用了 *HappyThread.js*

```
//getThread
function RoundRobinThreadPool(threads) {
  var lastThreadId = 0;

  return function getThread() {
    var threadId = lastThreadId;

    lastThreadId++;

    if (lastThreadId >= threads.length) {
      lastThreadId = 0;
    }

    return threads[threadId];
  }
}
```

RoundRobinThreadPool 这里的递增取对应进程很巧妙
最终是由 *HappyThread.js* 给子进程发了一个 `COMPILE` 消息

```
// HappyThread.js
/**
 * @param {Object} params
 * @param {String} params.compiledPath
 * @param {Object} params.loaderContext
 *
 * @param {Function} done
 */
compile: function(params, done) {
  var messageId = generateMessageId();

  callbacks[messageId] = done;

  send({
    id: messageId,
    name: 'COMPILE',
    data: params,
  });
},
```

这里的 messageId 是个从 0 开始的递增数字，完成回调方法的存储注册，方便完成编译之后找到回调方法传递信息回主进程。同时在 thread 这一层，也是将参数透传给子进程执行编译。

子进程收到消息
*HappyWorkerChannel.js*

```
COMPILE: function(message) {
    getWorker(message.data.loaderContext.compilerId)
        .compile(message.data, function(err, data) {
          send({
            id: message.id,
            name: 'COMPILED',
            error: err,
            data: data
          });
        });
},
```

收到消息后，调用 **worker.compile**
*HappyWorker.js*

```
/**
 * @param  {Object} params
 * @param  {Object} params.loaderContext
 * @param  {String} params.loaderContext.sourceCode
 * @param  {?String|?Object} params.loaderContext.sourceMap
 * @param  {Array.<String>} params.loaders
 * @param  {Function} done
 */
HappyWorker.prototype.compile = function(params, done) {
  assert(typeof params.loaderContext.resourcePath === 'string',
    "ArgumentError: expected params.sourcePath to contain path to the source file."
  );

  assert(Array.isArray(params.loaders),
    "ArgumentError: expected params.loaders to contain a list of loaders."
  );

  applyLoaders({
    compiler: this._compiler,
    loaders: params.loaders,
    loaderContext: params.loaderContext,
  }, params.loaderContext.sourceCode, params.loaderContext.sourceMap, function(err, source, sourceMap) {
    if (err) {
      done(ErrorSerializer.serialize(err))
    }
    else {
      done(null, {
        compiledSource: source,
        compiledMap: SourceMapSerializer.serialize(sourceMap)
      });
    }
  });
};
```

在 HappyWorker.js 中的`compile`方法中，调用`applyLoaders`进行 loader 方法执行。`applyLoaders`是`happypack`中对`webpack`中 loader 执行过程进行模拟，对应 NormalModuleMixin.js 中的[`doBuild`](https://github.com/webpack/core/blob/master/lib/NormalModuleMixin.js#L49)方法。完成对文件的字符串处理编译。

根据`err`判断是否成功。如果判断成功，则将对应文件的编译后内容写入之前传递进来的`compiledPath`,反之，则会把错误内容写入。

关于 **loader** 内部的执行机制可以点[这里](https://segmentfault.com/a/1190000021037299?utm_source=tag-newest#)

#### 编译结束

当 webpack 整体编译流程结束后， happypack 开始进行一些善后工作

```
// HappyPlugin.js

compiler.plugin('done', that.stop.bind(that));

HappyPlugin.prototype.stop = function() {
  if (this.config.cache) {
    this.cache.save();
  }

  this.threadPool.stop();
};

```

#### 整体流程

结合webpack的编译，整体流程可以参考下图
![](https://upload-images.jianshu.io/upload_images/10024246-3f564e9018081005.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 后记

happypack 源码阅读需要对 webpack 有一定的了解，阅读难度主要在于webpack相关的api的了解。有兴趣的同学可以一起讨论
>转载自https://segmentfault.com/a/1190000021037299?utm_source=tag-newest
