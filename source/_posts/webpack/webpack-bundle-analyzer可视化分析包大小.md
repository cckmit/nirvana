---
title: webpack-bundle-analyzer可视化分析包大小
date: 2021-03-11 21:12:40
categories: webpack
---

前端开发现在由于使用node进行开发，会安装很多依赖，但是很多依赖的资源是不透明的，构建完成后，需要分析生产文件到底依赖那些资源，每个资源占据多少空间，这样才能有针对性的优化。所以需要有个方便分析包大小的工具，可以按需优化。
先看webpack-bundle-analyzer的效果图（官方效果图）：
![](https://upload-images.jianshu.io/upload_images/10024246-0a46514a70cd94d0.gif?imageMogr2/auto-orient/strip)
## 一、安装
```
npm install webpack-bundle-analyzer –save-dev
```
## 二、配置
```
// 分析包内容 
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin; 
module.exports = { 
plugins: [ 
// 开启 BundleAnalyzerPlugin 
new BundleAnalyzerPlugin(), 
    ], 
};   
```
该插件的默认配置，一般无需修改
 ```
new BundleAnalyzerPlugin({
  //  可以是`server`，`static`或`disabled`。
  //  在`server`模式下，分析器将启动HTTP服务器来显示软件包报告。
  //  在“静态”模式下，会生成带有报告的单个HTML文件。
  //  在`disabled`模式下，你可以使用这个插件来将`generateStatsFile`设置为`true`来生成Webpack Stats JSON文件。
  analyzerMode: 'server',
  //  将在“服务器”模式下使用的主机启动HTTP服务器。
  analyzerHost: '127.0.0.1',
  //  将在“服务器”模式下使用的端口启动HTTP服务器。
  analyzerPort: 8888, 
  //  路径捆绑，将在`static`模式下生成的报告文件。
  //  相对于捆绑输出目录。
  reportFilename: 'report.html',
  //  模块大小默认显示在报告中。
  //  应该是`stat`，`parsed`或者`gzip`中的一个。
  //  有关更多信息，请参见“定义”一节。
  defaultSizes: 'parsed',
  //  在默认浏览器中自动打开报告
  openAnalyzer: true,
  //  如果为true，则Webpack Stats JSON文件将在bundle输出目录中生成
  generateStatsFile: false, 
  //  如果`generateStatsFile`为`true`，将会生成Webpack Stats JSON文件的名字。
  //  相对于捆绑输出目录。
  statsFilename: 'stats.json',
  //  stats.toJson（）方法的选项。
  //  例如，您可以使用`source：false`选项排除统计文件中模块的来源。
  //  在这里查看更多选项：https：  //github.com/webpack/webpack/blob/webpack-1/lib/Stats.js#L21
  statsOptions: null,
  logLevel: 'info' // 日志级别。可以是'信息'，'警告'，'错误'或'沉默'。
})
```
##### 三、启动
在package.json的scripts里加入下面这句话，就可以npm run build之后看到webpack-bundle-analyzer的效果：
```
"analyz": "NODE_ENV=production npm_config_report=true npm run build"
```
浏览器打开127.0.0.1:8888地址可以看到效果图。

##### 四：模块功能：

- 意识到你的文件打包压缩后中真正的内容
- 找出哪些模块组成最大的大小
- 找到错误的模块
- 最好的事情是它支持缩小捆绑！它解析它们以获得实际大小的捆绑模块。它也显示他们的gzipped大小！
