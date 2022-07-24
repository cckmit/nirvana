---
title: 五种可视化方案分析 webpack 打包性能瓶颈
date: 2021-04-21 14:30:50
categories: webpack
---
### 一、测量构建时间

优化 webpack 构建速度的第一步是知道将精力集中在哪里。我们可以通过 `speed-measure-webpack-plugin` 测量你的 webpack 构建期间各个阶段花费的时间：

**步骤一：安装 speed-measure-webpack-plugin**

```
npm install speed-measure-webpack-plugin --save-dev
复制代码
```

**步骤二：配置**

```
// 分析打包时间
const SpeedMeasurePlugin = require("speed-measure-webpack-plugin");
const smp = new SpeedMeasurePlugin();
// ...
module.exports = smp.wrap(prodWebpackConfig)
复制代码
```

![](https://upload-images.jianshu.io/upload_images/10024246-f71fb869cd81189a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


它能够：

*   分析整个打包总耗时；
*   每个插件和 loader 的耗时情况；

方便开发人员定位打包耗时瓶颈。

### 二、分析包内容

webpack-bundle-analyzer 扫描 bundle 并构建其内部内容的可视化。使用此可视化来查找大的或不必要的依赖项。

**步骤一：安装 webpack-bundle-analyzer**

```
npm install webpack-bundle-analyzer --save-dev
复制代码
```

**步骤二：配置**

```
// 分析包内容
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;
module.exports = {
  plugins: [
    // 开启 BundleAnalyzerPlugin
    new BundleAnalyzerPlugin(),
  ],
};
复制代码
```

一般运行在生产版本中，该插件将在浏览器中打开统计信息结果页面。

![](https://upload-images.jianshu.io/upload_images/10024246-55abc35c2b173da6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**注意：webpack4 在 `production` 环境下默认启动了 `ModuleConcatenationPlugin` （预编译所有模块到一个闭包中，提升代码在浏览器中的执行速度），它可能会合并webpack-bundle-analyzer 输出中的模块的一部分，从而使报告不太详细。 如果你使用此插件，请在分析过程中将其禁用。设置如下：**

```
module.exports = {
  // ...
  optimization: {
    // 
    concatenateModules: false,
  }
};
复制代码
```

具体来说，使用 webpack-bundle-analyzer 能可视化的反映：

*   打包出的文件中都包含了什么；
*   每个文件的尺寸在总体中的占比，哪些文件尺寸大，思考一下，为什么那么大，是否有替换方案，是否使用了它包含的所有代码；
*   模块之间的包含关系；
*   是否有重复的依赖项，是否存在一个库在多个文件中重复？ 或者捆绑包中是否具有同一库的多个版本？
*   是否有相似的依赖库， 尝试使用一种依赖库实现相似的功能。
*   每个文件的压缩后的大小。

### 三、在线分析工具

如果你对在本地安装插件进行测量分析包方式感觉不爽的话，这里将会推荐几款在线测量工具，但都需要 webpack 统计文件。

#### 1\. 生成统计文件 `stats.json`

使用 webpack 编译源代码时，用户可以生成一个包含有关模块统计信息的 JSON 文件。 这些统计信息可用于分析应用程序的依赖关系图以及优化编译速度。 该文件通常是按以下方式生成的：

在 webpack 启动时带入参数：`--profile --json > stats.json` ，例如：

```
webpack --config webpack.config.prod.js --profile --json > stats.json
复制代码
```

*   `--profile`：记录下构建过程中的耗时信息
*   `--json`：以 JSON 的格式输出构建结果，最后只输出一个 `.json` 文件，这个文件（`stats.json`）中包括所有构建相关的信息
*   `> stats.json` ：是 UNIX/Linux 系统中的管道命令、含义是把 `webpack --profile --json` 输出的内容通过管道输出到 `stats.json` 文件中

执行命令后，会在项目中多出一个 `stats.json` 文件，接下来将 `stats.json` 文件上传到以下在线工具上，以可视化分析包的组成。

常用的在线工具有：

*   官方可视化分析工具 Webapck Analyse：生成一个图表，让你可视化了解项目的依赖关系、模块大小及耗时等；
*   Webpack Visualizer：生成一个饼状图，可视化 bundle 内容；
*   webpack bundle optimize helper：此工具会分析你的 bundle，并为你提供可操作的改进措施建议，以减少 bundle 体积大小。

#### 2\. 官方可视化分析工具 Webapck Analyse

[Webapck Analyse](http://webpack.github.io/analyse/) 是一个在线 Web 应用，它为你提供了对包的更全面的分析，并且它绘制了项目中所有依赖模块的图形，这对于依赖关系较少的项目非常有用。

打开 [Webapck Analyse](http://webpack.github.io/analyse/) ，上传 `stats.json`：

![](https://upload-images.jianshu.io/upload_images/10024246-cdf2f57c826f8d8a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


Webpack Analyse 不会把你选择的 `stats.json` 文件发达到服务器，而是在浏览器本地解析，你不用担心自己的代码为此而泄露。 选择文件后，你马上就能如下的效果图：

![](https://upload-images.jianshu.io/upload_images/10024246-b4a1cc7f4ec92539.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**Modules** ：展示所有的模块，每个模块对应一个文件。并且还包含所有模块之间的依赖关系图、模块路径、模块ID、模块所属 Chunk、模块大小；

![](https://upload-images.jianshu.io/upload_images/10024246-2cf996c3528f9bcb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**Chunks** ：展示所有的代码块，一个代码块中包含多个模块。并且还包含代码块的ID、名称、大小、每个代码块包含的模块数量，以及代码块之间的依赖关系图；

**Assets** ：展示所有输出的文件资源，包括 `.js`、`.css`、图片等。并且还包括文件名称、大小、该文件来自哪个代码块；

![](https://upload-images.jianshu.io/upload_images/10024246-bddc32d51392a614.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**Warnings** ：展示构建过程中出现的所有警告信息；

![](https://upload-images.jianshu.io/upload_images/10024246-210b137a0478fa88.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**Errors** ：展示构建过程中出现的所有错误信息；
![](https://upload-images.jianshu.io/upload_images/10024246-8a479c1eef699870.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**Hints** ：展示处理每个模块的过程中的耗时。

![](https://upload-images.jianshu.io/upload_images/10024246-8f41bdba3a5b0d37.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 3\. Webpack Visualizer 分析工具

可视化并分析您的Webpack捆绑包，以查看哪些模块正在占用空间，哪些可能是重复的。

它既可作为插件使用，也可以在线使用，是一种较新的工具。

**方式一：作为插件使用**

```
npm install webpack-visualizer-plugin --dev-save
复制代码
```

配置：

```
// 分析包内容
const Visualizer = require('webpack-visualizer-plugin');
module.exports = {
  plugins: [
    // 开启 Visualizer
    // 默认输出为 stats.html，这里为 statistics.html
    new Visualizer({
      filename: './statistics.html'
    })
  ],
};
复制代码
```

然后在浏览器中直接打开 `statistics.html` 就可以看到分析结果了：

![](https://upload-images.jianshu.io/upload_images/10024246-4cecd9f30e511265.gif?imageMogr2/auto-orient/strip)


**方式二：在线使用**

打开 [chrisbateman.github.io/webpack-vis…](http://chrisbateman.github.io/webpack-visualizer/) ，上传 `stats.json` 既可看到分析结果。

#### 4\. webpack bundle optimize helper 分析工具

打开 [webpack.jakoblind.no/optimize/](https://webpack.jakoblind.no/optimize/) ，上传 `stats.json` 既可看到分析结果及优化建议：

![](https://upload-images.jianshu.io/upload_images/10024246-432920dbb121086a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 四、总结

我们总共介绍了以下 `5` 种测量工具，每种工具都提供了对包分析的不同视角，例如：

*   在开发过程中，当向项目引入新包时，我个人经常使用 Webpack Visualizer ，饼状图给出了关于大小的比例的即时反馈；
*   在分析每次构建版本的耗时情况时，相对于其它 `4` 中，`speed-measure-webpack-plugin` 能够获取每个插件和 loader 的耗时情况，帮助你将注意力集中在需要优化的插件上；
*   `webpack-bundle-analyzer` 能够将 bundle 内容展示为便捷的、交互式、可缩放的树状图形式；
*   Webapck Analyse 相对于其它 `4` 种，能够对包进行全方位的分析；
*   相对于其它 `4` 种，webpack bundle optimize helper 能提供可操作的改进措施建议；

>作者：前端瓶子君
链接：https://juejin.cn/post/6844904056985485320

