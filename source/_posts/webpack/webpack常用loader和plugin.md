---
title: webpack常用loader和plugin
date: 2022-05-26 16:46:33
categories: webpack
---
## Loader

### 简介

webpack中提供了一种处理多种文件格式的机制，这便是Loader，我们可以把Loader当成一个转换器，它可以将某种格式的文件转换成Wwebpack支持打包的模块。

在Webpack中，一切皆模块，我们常见的Javascript、CSS、Less、Typescript、Jsx、图片等文件都是模块，不同模块的加载是通过模块加载器来统一管理的，当我们需要使用不同的 Loader 来解析不同类型的文件时，我们可以在module.rules字段下配置相关规则。

#### loader特点

*   loader 本质上是一个函数，output=loader(input) // input可为工程源文件的字符串，也可是上一个loader转化后的结果；
*   第一个 loader 的传入参数只有一个：资源文件(resource file)的内容；
*   loader支持链式调用，webpack打包时是按照数组从后往前的顺序将资源交给loader处理的。
*   支持同步或异步函数。

#### 代码结构

代码结构通常如下：

```
// source：资源输入，对于第一个执行的 loader 为资源文件的内容；后续执行的 loader 则为前一个 loader 的执行结果
// sourceMap: 可选参数，代码的 sourcemap 结构
// data: 可选参数，其它需要在 Loader 链中传递的信息，比如 posthtml/posthtml-loader 就会通过这个参数传递参数的 AST 对象
const loaderUtils = require('loader-utils');

module.exports = function(source, sourceMap?, data?) {
  // 获取到用户给当前 Loader 传入的 options
  const options = loaderUtils.getOptions(this);
  // TODO： 此处为转换source的逻辑
  return source;
};
```
### 常用的Loader

#### 1\. babel-loader

babel-loader基于babel，用于解析JavaScript文件。babel有丰富的预设和插件，babel的配置可以直接写到options里或者单独写道配置文件里。

Babel是一个Javscript编译器，可以将高级语法(主要是ECMAScript 2015+ )编译成浏览器支持的低版本语法，它可以帮助你用最新版本的Javascript写代码，提高开发效率。

webpack通过babel-loader使用Babel。

****用法****：

```
 # 环境要求:
webpack 4.x || 5.x | babel-loader 8.x | babel 7.x
 # 安装依赖包:
npm install -D babel-loader @babel/core @babel/preset-env webpack
```

然后，我们需要建立一个Babel配置文件来指定编译的规则。

Babel配置里的两大核心：插件数组(plugins) 和 预设数组(presets)。

Babel 的预设（preset）可以被看作是一组Babel插件的集合，由一系列插件组成。

****常用预设：****

*   @babel/preset-env              ES2015+ 语法
*   @babel/preset-typescript    TypeScript
*   @babel/preset-react            React
*   @babel/preset-flow              Flow

****插件和预设的执行顺序：****

*   插件比预设先执行
*   插件执行顺序是插件数组从前向后执行
*   预设执行顺序是预设数组从后向前执行

****webpack配置代码：****

```
// webpack.config.js
module: {
  rules: [
    {
      test: /\.m?js$/,
      exclude: /node_modules/,
      use: {
        loader: 'babel-loader',
        options: {
          presets: [
            ['@babel/preset-env', { targets: "defaults" }]
          ],
          plugins: ['@babel/plugin-proposal-class-properties'],
          // 缓存 loader 的执行结果到指定目录，默认为node_modules/.cache/babel-loader，之后的 webpack 构建，将会尝试读取缓存
          cacheDirectory: true,
        }
      }
    }
  ]
}
```

以上options参数也可单独写到配置文件里，许多其他工具都有类似的配置文件：ESLint (.eslintrc)、Prettier (.prettierrc)。

配置文件我们一般只需要配置 presets(预设数组) 和 plugins(插件数组) ，其他一般也用不到，代码示例如下：

```
// babel.config.js
module.exports = (api) => {
    return {
        presets: [
            '@babel/preset-react',
            [
                '@babel/preset-env', {
                    useBuiltIns: 'usage',
                    corejs: '2',
                    targets: {
                        chrome: '58',
                        ie: '10'
                    }
                }
            ]
        ],
        plugins: [
            '@babel/plugin-transform-react-jsx',
            '@babel/plugin-proposal-class-properties'
        ]
    };
};
```

****推荐阅读：****

*   [babel配置文件相关文档](https://www.babeljs.cn/docs/configuration)
*   [插件手册](https://github.com/jamiebuilds/babel-handbook/blob/master/translations/zh-Hans/plugin-handbook.md)

#### 2\. ts-loader

为webpack提供的 TypeScript loader，打包编译Typescript

****安装依赖：****
```
npm install ts-loader --save-dev
npm install typescript --dev
```

****webpack配置如下：****

```
// webpack.config.json
module.exports = {
  mode: "development",
  devtool: "inline-source-map",
  entry: "./app.ts",
  output: {
    filename: "bundle.js"
  },
  resolve: {
    // Add `.ts` and `.tsx` as a resolvable extension.
    extensions: [".ts", ".tsx", ".js"]
  },
  module: {
    rules: [
      // all files with a `.ts` or `.tsx` extension will be handled by `ts-loader`
      { test: /\.tsx?$/, loader: "ts-loader" }
    ]
  }
};
```

还需要typescript编译器的配置文件****tsconfig.json****：

```
{
  "compilerOptions": {
    // 目标语言的版本
    "target": "esnext",
    // 生成代码的模板标准
    "module": "esnext",
    "moduleResolution": "node",
    // 允许编译器编译JS，JSX文件
    "allowJS": true,
    // 允许在JS文件中报错，通常与allowJS一起使用
    "checkJs": true,
    "noEmit": true,
    // 是否生成source map文件
    "sourceMap": true,
    // 指定jsx模式
    "jsx": "react"
  },
  // 编译需要编译的文件或目录
  "include": [
    "src",
    "test"
  ],
  // 编译器需要排除的文件或文件夹
  "exclude": [
    "node_modules",
    "**/*.spec.ts"
  ]
}
```

更多配置请看 [官网](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html)

#### 3\. markdown-loader

markdown编译器和解析器

****用法：****

只需将 loader 添加到您的配置中，并设置 options。

****js代码里引入markdown文件：****

```
// file.js

import md from 'markdown-file.md';

console.log(md);
```

****webpack配置：****

```
// wenpack.config.js
const marked = require('marked');
const renderer = new marked.Renderer();

module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.md$/,
        use: [
            {
                loader: 'html-loader'
            },
            {
                loader: 'markdown-loader',
                options: {
                    pedantic: true,
                    renderer
                }
            }
        ]
      }
    ],
  },
};
```

#### 4\. raw-loader

可将文件作为字符串导入

```
// app.js
import txt from './file.txt';
// webpack.config.js
module.exports = {
  module: {
    rules: [
      {
        test: /\.txt$/,
        use: 'raw-loader'
      }
    ]
  }
}
```

#### 5\. file-loader

用于处理文件类型资源，如jpg，png等图片。返回值为publicPath为准

```
// file.js
import img from './webpack.png';
console.log(img); // 编译后：https://www.tencent.com/webpack_605dc7bf.png
// webpack.config.js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpe?g|gif)$/i,
        loader: 'file-loader',
        options: {
          name: '[name]_[hash:8].[ext]',
          publicPath: "https://www.tencent.com",
        },
      },
    ],
  },
};
```

css文件里的图片路径变成如下：

```
/* index.less */
.tag {
  background-color: red;
  background-image: url(./webpack.png);
}
/* 编译后：*/
background-image: url(https://www.tencent.com/webpack_605dc7bf.png);
```

#### 6\. url-loader: 

它与file-loader作用相似，也是处理图片的，只不过url-loader可以设置一个根据图片大小进行不同的操作，如果该图片大小大于指定的大小，则将图片进行打包资源，否则将图片转换为base64字符串合并到js文件里。

```
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|jpeg)$/,
        use: [
          {
            loader: 'url-loader',
            options: {
              name: '[name]_[hash:8].[ext]',
              // 这里单位为(b) 10240 => 10kb
              // 这里如果小于10kb则转换为base64打包进js文件，如果大于10kb则打包到对应目录
              limit: 10240,
            }
          }
        ]
      }
    ]
  }
}
```

#### 7\. svg-sprite-loader

会把引用的 svg文件 塞到一个个 symbol 中，合并成一个大的SVG sprite，使用时则通过 SVG 的 \<use> 传入图标 id 后渲染出图标。最后将这个大的 svg 放入 body 中。symbol的id如果不特别指定，就是你的文件名。

该loader可以搭配****svgo-loader**** 一起使用，svgo-loader是svg的优化器，它可以删除和修改SVG元素，折叠内容，移动属性等，具体不展开描述。感兴趣的可以移步 [官方介绍]https://github.com/svg/svgo-loader)。

****用途：可以用来开发统一的图标管理库。****

![](https://upload-images.jianshu.io/upload_images/10024246-8932a1e75c987793.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

****示例代码：****

```
// js文件里用法
import webpack from './webpack/webpack.svg';
const type = 'webpack';
const svg =  `<svg>
    <use xlink:href="#${type}"/>
  </svg>`;
const dom = `<div class="tag">
  ${svg}
  </div>`;
document.getElementById('react-app').innerHTML = dom;
// webpack.config.js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|jpeg)$/,
        use: [
          {
            test: /\.svg$/,
            use: [
                {
                  loader: 'svg-sprite-loader'
                },
                'svgo-loader'
            ]
          },
        ]
      }
    ]
  }
}
```

原理：利用 svg 的 symbol 元素，将每个 icon 包裹在 symbol 中，通过 use 使用该 symbol。

#### 8\. style-loader

通过注入\<style\>标签将CSS插入到DOM中

****注意：****

*   如果因为某些原因你需要将CSS提取为一个文件(即不要将CSS存储在JS模块中)，此时你需要使用插件 ****mini-css-extract-plugin****(后面的Pugin部分会介绍)；
*   对于development模式(包括 webpack-dev-server)你可以使用style-loader，因为它是通过\<style>\</style>标签的方式引入CSS的，加载会更快；
*   不要将 style-loader 和 mini-css-extract-plugin 针对同一个CSS模块一起使用！

代码示例见下文 postcss-loader

#### 9\. css-loader

仅处理css的各种加载语法(@import和url()函数等),就像 js 解析 import/require() 一样

代码示例见下文 postcss-loader

#### 10\. postcss-loader

PostCSS 是一个允许使用 JS 插件转换样式的工具。 这些插件可以检查（lint）你的 CSS，支持 CSS Variables 和 Mixins， 编译尚未被浏览器广泛支持的先进的 CSS 语法，内联图片，以及其它很多优秀的功能。

PostCSS 在业界被广泛地应用。PostCSS 的 ****autoprefixer**** 插件是最流行的 CSS 处理工具之一。

autoprefixer 添加了浏览器前缀，它使用 Can I Use 上面的数据。

****安装****

```npm install postcss-loader autoprefixer --save-dev```

****代码示例：****

```
// webpack.config.js
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const isDev = process.NODE_ENV === 'development';
module.exports = {
  module: {
    rules: [
      {
        test: /\.(css|less)$/,
        exclude: /node_modules/,
        use: [
          isDev ? 'style-loader' : MiniCssExtractPlugin.loader,
          {
            loader: 'css-loader',
            options: {
              importLoaders: 1,
            }
          },
          {
            loader: 'postcss-loader'
          },
          {
              loader: 'less-loader',
              options: {
                  lessOptions: {
                      javascriptEnabled: true
                  }
              }
          }
        ]
      }
    ]
  }
}
```

然后在项目根目录创建postcss.config.js，并且设置支持哪些浏览器，必须设置支持的浏览器才会自动添加添加浏览器兼容

```
module.exports = {
  plugins: [
    require('precss'),
    require('autoprefixer')({
      'browsers': [
        'defaults',
        'not ie < 11',
        'last 2 versions',
        '> 1%',
        'iOS 7',
        'last 3 iOS versions'
      ]
    })
  ]
}
```

截止到目前，PostCSS 有 200 多个插件。你可以在 [插件列表](https://github.com/postcss/postcss/blob/main/docs/plugins.md) 或 [搜索目录](https://www.postcss.parts/) 找到它们

了解更多请移步 [链接](https://github.com/postcss/postcss/blob/main/docs/README-cn.md)

#### 11\. less-loader

解析less，转换为css

****代码示例见上文 postcss-loader****

了解更多请移步 [链接](https://webpack.js.org/loaders/less-loader/)

## Plugin

### Plugin简介

> Webpack 就像一条生产线，要经过一系列处理流程后才能将源文件转换成输出结果。 这条生产线上的每个处理流程的职责都是单一的，多个流程之间有存在依赖关系，只有完成当前处理后才能交给下一个流程去处理。 插件就像是一个插入到生产线中的一个功能，在特定的时机对生产线上的资源做处理。
> 
> Webpack 通过 Tapable 来组织这条复杂的生产线。 Webpack 在运行过程中会广播事件，插件只需要监听它所关心的事件，就能加入到这条生产线中，去改变生产线的运作。 Webpack 的事件流机制保证了插件的有序性，使得整个系统扩展性很好。
> 
> ——「深入浅出 Webpack」

### 常用Plugin

#### 1\. copy-webpack-plugin

将已经存在的单个文件或整个目录复制到构建目录。

```
const CopyPlugin = require("copy-webpack-plugin");

module.exports = {
  plugins: [
    new CopyPlugin({
      patterns: [
        { 
          from: './template/page.html', 
          to: `${__dirname}/output/cp/page.html` 
        },
      ],
    }),
  ],
};
```

#### 2\. html-webpack-plugin

基本作用是生成html文件

*   单页应用可以生成一个html入口，多页应用可以配置多个html-webpack-plugin实例来生成多个页面入口
*   为html引入外部资源如script、link，将entry配置的相关入口chunk以及mini-css-extract-plugin抽取的css文件插入到基于该插件设置的template文件生成的html文件里面，具体的方式是link插入到head中，script插入到head或body中。

```
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: {
    news: [path.resolve(__dirname, '../src/news/index.js')],
    video: path.resolve(__dirname, '../src/video/index.js'),
  },
  plugins: [
    new HtmlWebpackPlugin({
      title: 'news page',
      // 生成的文件名称 相对于webpackConfig.output.path路径而言
      filename: 'pages/news.html',
      // 生成filename的文件模板
      template: path.resolve(__dirname, '../template/news/index.html'),
      chunks: ['news']

    }),
    new HtmlWebpackPlugin({
      title: 'video page',
      // 生成的文件名称
      filename: 'pages/video.html',
      // 生成filename的文件模板
      template: path.resolve(__dirname, '../template/video/index.html'),
      chunks: ['video']
    }),
  ]
};
```

#### 3\. clean-webpack-plugin

默认情况下，这个插件会删除webpack的output.path中的所有文件，以及每次成功重新构建后所有未使用的资源。

这个插件在生产环境用的频率非常高，因为生产环境经常会通过 hash 生成很多 bundle 文件，如果不进行清理的话每次都会生成新的，导致文件夹非常庞大。

```
const { CleanWebpackPlugin } = require('clean-webpack-plugin');
module.exports = {
    plugins: [
        new CleanWebpackPlugin(),
    ]
};
```

#### 4\. mini-css-extract-plugin

本插件会将 CSS 提取到单独的文件中，为每个包含 CSS 的 JS 文件创建一个 CSS 文件。

```
// 建议 mini-css-extract-plugin 与 css-loader 一起使用
// 将 loader 与 plugin 添加到 webpack 配置文件中
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  plugins: [new MiniCssExtractPlugin()],
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [MiniCssExtractPlugin.loader, 'css-loader'],

      }
    ],
  },
};
```

可以结合上文关于style-loader的介绍一起了解该插件。

#### 5\. webpack.HotModuleReplacementPlugin

模块热替换插件，除此之外还被称为 HMR。

该功能会在应用程序运行过程中，替换、添加或删除 模块，而无需重新加载整个页面。主要是通过以下几种方式，来显著加快开发速度:

*   保留在完全重新加载页面期间丢失的应用程序状态。
*   只更新变更内容，以节省宝贵的开发时间。
*   在源代码中 CSS/JS 产生修改时，会立刻在浏览器中进行更新，这几乎相当于在浏览器 devtools 直接更改样式。

****启动方式有2种：****

*   引入插件webpack.HotModuleReplacementPlugin 并且设置devServer.hot: true
*   命令行加 --hot参数

****package.json配置：****

```
{
  "scripts": {
    "start": "NODE_ENV=development webpack serve --progress --mode=development --config=scripts/dev.config.js --hot"
  }
}
```

****webpack的配置如下：****

```
// scripts/dev.config.js文件
const webpack = require('webpack');
const path = require('path');
const outputPath = path.resolve(__dirname, './output/public');

module.exports = {
  mode: 'development',
  entry: {
    preview: [
      './node_modules/webpack-dev-server/client/index.js?path=http://localhost:9000',
      path.resolve(__dirname, '../src/preview/index.js')
    ],
  },
  output: {
    filename: 'static/js/[name]/index.js',
    // 动态生成的chunk在输出时的文件名称
    chunkFilename: 'static/js/[name]/chunk_[chunkhash].js',
    path: outputPath
  },
  plugins: [
    // 大多数情况下不需要任何配置
    new webpack.HotModuleReplacementPlugin(),
  ],
  devServer: {
        // 仅在需要提供静态文件时才进行配置
        contentBase: outputPath,
        // publicPath: '', // 值默认为'/'
        compress: true,
        port: 9000,
        watchContentBase: true,
        hot: true,
        // 在服务器启动后打开浏览器
        open: true,
        // 指定打开浏览器时要浏览的页面
        openPage: ['pages/preview.html'],
        // 将产生的文件写入硬盘。 写入位置为 output.path 配置的目录
        writeToDisk: true,
    }
}
```
****注意：HMR 绝对不能被用在生产环境。****

#### 6\. webpack.DefinePlugin

创建一个在编译时可以配置的全局常量。这会对开发模式和生产模式的构建允许不同的行为非常有用。

因为这个插件直接执行文本替换，给定的值必须包含字符串本身内的实际引号。

通常，有两种方式来达到这个效果，使用'"production"', 或者使用 JSON.stringify('production')

```
// webpack.config.js
const isProd = process.env.NODE_ENV === 'production';
module.exports = {
  plugins: [
    new webpack.DefinePlugin({
      PAGE_URL: JSON.stringify(isProd
        ? 'https://www.tencent.com/page'
        : 'http://testsite.tencent.com/page'
      )
    }),
  ]
}

// 代码里面直接使用
console.log(PAGE_URL);
```
#### 7\. webpack-bundle-analyzer

可以看到项目各模块的大小，可以按需优化.一个webpack的bundle文件分析工具，将bundle文件以可交互缩放的treemap的形式展示。

```
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;

module.exports = {
  plugins: [
    new BundleAnalyzerPlugin()
  ]
}
```
****启动服务：****

*   生产环境查看：NODE_ENV=production npm run build
*   开发环境查看：NODE_ENV=development npm run start

****最终效果：****

![](https://upload-images.jianshu.io/upload_images/10024246-90b666b8d1f51ef5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


了解更多请移步 [链接](https://github.com/webpack-contrib/webpack-bundle-analyzer)

#### 8\. SplitChunksPlugin

代码分割。

```
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;

module.exports = {
  optimization: {
    splitChunks: {
      // 分隔符
      // automaticNameDelimiter: '~',
      // all, async, and initial
      chunks: 'all',
      // 它可以继承/覆盖上面 splitChunks 中所有的参数值，除此之外还额外提供了三个配置，分别为：test, priority 和 reuseExistingChunk
      cacheGroups: {
        vendors: {
          // 表示要过滤 modules，默认为所有的 modules，可匹配模块路径或 chunk 名字，当匹配的是 chunk 名字的时候，其里面的所有 modules 都会选中
          test: /[\\/]node_modules\/antd\//,
          // priority：表示抽取权重，数字越大表示优先级越高。因为一个 module 可能会满足多个 cacheGroups 的条件，那么抽取到哪个就由权重最高的说了算；
          // priority: 3,
          // reuseExistingChunk：表示是否使用已有的 chunk，如果为 true 则表示如果当前的 chunk 包含的模块已经被抽取出去了，那么将不会重新生成新的。
          reuseExistingChunk: true,
          name: 'antd'
        }
      }
    }
  },
}
```
