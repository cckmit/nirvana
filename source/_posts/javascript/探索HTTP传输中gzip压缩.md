---
title: 探索HTTP传输中gzip压缩
date: 2021-03-11 21:12:40
categories: 前端
---

### 为什么要开启gZip

我们给某人发送邮件时，我们在传输之前把自己的文件压缩一下，接收方收到文件后再去解压获取文件。这中操作对于我们来说都已经司空见惯。我们压缩文件的目的就是为了把传输文件的体积减小，加快传输速度。我们在 `http` 传输中开启 `gZip` 的目的也是如此，但是一般文章介绍 `gZip` 时候总是结合一些服务端配置(`nginx`)或者构建工具插件(`webpack`)来说，列出一大堆配置让人看的云里雾里，以至于到最后还没搞懂 `为什么用`，`怎么用` 这些问题。

### http 与 gZip

我们下面去探讨一下这些问题

#### `gZip` 文件怎么通讯

我们传输压缩文件给别人时候一般都带着后缀名 `.rar`, `.zip`之类，对方在拿到文件后根据相应的后缀名选择不同的解压方式然后去解压文件。我们在 `http` 传输时候解压文件的这个角色的扮演者就是我们使用的浏览器，但是浏览器怎么分辨这个文件是什么格式，应该用什么格式去解压呢？

在 `http／1.0` 协议中关于服务端发送的数据可以配置一个 `Content-Encoding` 字段，这个字段用于说明数据的压缩方法

```
Content-Encoding: gzip
Content-Encoding: compress
Content-Encoding: deflate
```

客户端在接受到返回的数据后去检查对应字段的信息，然后根据对应的格式去做相应的解码。客户端在请求时，可以用 `Accept-Encoding` 字段说明自己接受哪些压缩方法。

```
Accept-Encoding: gzip, deflate
```

我们在浏览器的控制台中可以看到请求的相关信息

![](https://upload-images.jianshu.io/upload_images/10024246-e5ec64cbfb3c270e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



##### 兼容性

提到浏览器作为一个前端就不由自主的会想一个问题，会不会有浏览器不支持呢。`HTTP/1.0` 是1996年5月发布的。好消息是基本不用考虑兼容性的问题，几乎所有浏览器都支持它。值得一提的是 `ie6`的早起版本中存在一个会破坏 `gZip`的错误，后面 `ie6`本身在 `WinXP SP2` 中修复了这个问题，而且用这个版本的用户数量也很少。

#### 谁去压缩文件

这件事看起来貌似只能服务端来做，我们在网上看到最多的也是诸如 `nginx` 开启 `gZip` 配置之类的文章，但是现在前端流行 `spa` 应用, 用 `react`, `vue` 之类的框架时候总伴随这一套自己的脚手架,一般用 `webpack` 作为打包工具，其中可以配置插件 如[compression-webpack-plugin](https://github.com/webpack-contrib/compression-webpack-plugin) 可以让我们把生成文件进行 `gZip` 等压缩并生成对应的压缩文件，而我们应用在构架时候有可能也会在服务区和前端文件中放置一层 `node` 应用来进行接口鉴权和文件转发。`nodejs`中我们熟悉的`express` 框架中也有一个[compression](https://github.com/expressjs/compression) 中间件，可以开启`gZip`,一时间看的人眼花缭乱，到底应该用谁怎么用呢？

##### 服务端响应请求时候压缩

其实 `nginx` 压缩和 `node` 框架中用中间件去压缩都是一样的，当我们点击网页发送一个请求时候，我们的服务端会找到对应的文件，然后对文件进行压缩返回压缩后的内容【*当然可以利用缓存减少压缩次数*】，并配置好我们上面提到的 `Content-Encoding` 信息。对于一些应用在构架时候并没有上游代理层，比如服务端就一层 `node` 就可以直接用自己本身的压缩插件对文件进行压缩，如果上游配有有 `nginx` 转发处理层，最好交给 `nginx` 来处理这些，因为它们有专门为此构建的内容，可以更好的利用缓存并减小开销（很多使用c语言编写的）。

我们看一些 `nginx` 中开启 `gZip` 压缩的一部分配置

```
# 开启gzip
gzip on;
# 启用gzip压缩的最小文件，小于设置值的文件将不会压缩
gzip_min_length 1k;
# gzip 压缩级别，1-10，数字越大压缩的越好，也越占用CPU时间，后面会有详细说明
gzip_comp_level 2;
# 进行压缩的文件类型。javascript有多种形式。其中的值可以在 mime.types 文件中找到。
gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript;

```

##### 应用构建时候压缩

既然服务端都可以做了为什么 `webpack` 在打包前端应用时候还有这样一个压缩插件呢，我们可以在上面 `nginx` 配置中看到 `gzip_comp_level 2` 这个配置项，上面也有注释写道 `1-10` 数字越大压缩效果越好，但是会耗费更多的CPU和时间，我们压缩文件除了减少文件体积大小外，也是为了减少传输时间，如果我们把压缩等级配置的很高，每次请求服务端都要压缩很久才回返回信息回来，不仅服务器开销会增大很多，请求方也会等的不耐烦。但是现在的 `spa` 应用既然文件都是打包生成的，那如果我们在打包时候就直接生成高压缩等级的文件，作为静态资源放在服务器上，接收到请求后直接把压缩的文件内容返回回去会怎么样呢？

`webpack` 的 `compression-webpack-plugin` 就是做这个事情的，配置起来也很简单只需要在装置中加入对应插件,简单配置如下

```
const CompressionWebpackPlugin = require('compression-webpack-plugin');

webpackConfig.plugins.push(
    new CompressionWebpackPlugin({
      asset: '[path].gz[query]',
      algorithm: 'gzip',
      test: new RegExp('\\.(js|css)/article>),
      threshold: 10240,
      minRatio: 0.8
    })
)
```

`webpack` 打包完成后生成打包文件外还会额外生成 `.gz` 后缀的压缩文件

[图片上传失败...(image-b14f20-1614231793629)] 

那么这个插件的压缩等级是多少呢，我们可以在[源码](https://github.com/webpack-contrib/compression-webpack-plugin/blob/master/src/index.js)中看到默认的 `level` 是 **9**

```
...
const zlib = require('zlib');
this.options.algorithm = zlib[this.options.algorithm];
...
this.options.compressionOptions = {
    level: options.level || 9,
    flush: options.flush
    ...
}
```

可以看到压缩使用的是 `zlib` 库，而 `zlib` 分级来说，默认是 6 ，最高的级别就是`9 Best compression (also zlib.Z_BEST_COMPRESSION)`,因为我们只有在上线项目时候才回去打包构建一次，所以我们在构建时候使用最高级的压缩方式压缩多耗费一些时间对我们来说根本没任何损耗，而我们在服务器上也不用再去压缩文件，只需要找到相应已经压缩过的文件直接返回就可以了。

###### 服务端怎么找到这些文件

在应用层面解决这个问题还是比较简单的，比如上述压缩文件会产生`index.css`, `index.js`的压缩文件，在服务端简单处理可以判断这两个请求然后给予相对应的压缩文件。以 `node` 的 `express` 为例

```
...
app.get(['/index.js','/index.css'], function (req, res, next) {
  req.url = req.url + '.gz'
  res.set('Content-Encoding', 'gzip')
  res.setHeader("Content-Type", generateType(req.path)) // 这里要根据请求文件设置content-type
  next()
})
```

上面我们可以给请求返回 `gZip` 压缩后的数据了，当然上面的局限性太强也不可取，但是对于处理这个方面需求也已经有很多库存在,`express` 有 [express-static-gzip](https://github.com/tkoenig89/express-static-gzip) 插件 `koa` 的 `koa-static` 则默认自带对 `gZip` 文件的检测，基本原理就是对请求先检测 `.gz`后缀的文件是否存在，再去根据结果返回不同的内容。

#### 哪些文件可以被 gZip 压缩

`gZip` 可以压缩所有的文件，但是这不代表我们要对所有文件进行压缩，我们写的代码（`css,js`）之类的文件会有很好的压缩效果，但是图片之类文件则不会被 `gzip` 压缩太多，因为它们已经内置了一些压缩，一些文件（比如一些已经被压缩的像.zip文件那种）再去压缩可能会让生成的文件体积更大一些。当然已经很小的文件也没有去压缩的必要了。

### 实践

能开启 `gZip` 肯定是要开启的，具体使用在请求时候实时压缩还是在构建时候去生成压缩文件，就要看自己具体业务情况。

### 参考资料

*   [How are zlib, gzip and zip related? What do they have in common and how are they different?](https://stackoverflow.com/questions/20762094/how-are-zlib-gzip-and-zip-related-what-do-they-have-in-common-and-how-are-they/20765054#20765054)
*   [webpack gzip vs express gzip](https://stackoverflow.com/questions/38587698/webpack-gzip-vs-express-gzip?s=1%7C140.9128)
*   [What is gZip compression?](https://stackoverflow.com/questions/16691506/what-is-gzip-compression)
*   [HTTP 协议](http://www.ruanyifeng.com/blog/2016/08/http.html)

