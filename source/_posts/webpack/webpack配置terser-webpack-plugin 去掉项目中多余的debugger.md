---
title: webpack配置terser-webpack-plugin 去掉项目中多余的debugger
date: 2021-03-11 21:12:40
categories: webpack
---

## 入门
如果你使用的是 webpack v5 或以上版本，你不需要安装这个插件。webpack v5 自带最新的 `terser-webpack-plugin`。如果使用 webpack v4，则必须安装 `terser-webpack-plugin` v4 的版本。

首先，你需要安装 `terser-webpack-plugin`：

```
$ npm install terser-webpack-plugin --save-dev

```

然后将插件添加到你的 `webpack` 配置文件中，直接在`optimization中`配置。例如：

**webpack.config.js**
```
const TerserPlugin = require('terser-webpack-plugin');
module.exports = {
  	optimization: {
	    minimize: true,
	    minimizer: [new TerserPlugin({
	    	test: /\.js(\?.*)?$/i,    //匹配参与压缩的文件
	    	parallel: true,    //使用多进程并发运行
	    	terserOptions: {    //Terser 压缩配置
	    		output:{comments: false}
	    	},
	    	extractComments: true,    //将注释剥离到单独的文件中
                compress: {//console删除
                    pure_funcs: ["console.log"]
                }
	    })],
  	},
};
```
## 选项 [](https://webpack.docschina.org/plugins/terser-webpack-plugin/#options)

### `test` [](https://webpack.docschina.org/plugins/terser-webpack-plugin/#test)

类型： `String|RegExp|Array<String|RegExp>` 默认值：`/\.m?js(\?.*)?$/i`

用来匹配需要压缩的文件。

**webpack.config.js**

```
module.exports = {
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        test: /\.js(\?.*)?$/i,
      }),
    ],
  },
};
```

### `include` [](https://webpack.docschina.org/plugins/terser-webpack-plugin/#include)

类型： `String|RegExp|Array<String|RegExp>` 默认值： `undefined`

匹配参与压缩的文件。

**webpack.config.js**

```
module.exports = {
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        include: /\/includes/,
      }),
    ],
  },
};
```

### `exclude` [](https://webpack.docschina.org/plugins/terser-webpack-plugin/#exclude)

类型： `String|RegExp|Array<String|RegExp>` 默认值： `undefined`

匹配不需要压缩的文件。

**webpack.config.js**

```
module.exports = {
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        exclude: /\/excludes/,
      }),
    ],
  },
};
```

### `parallel` [](https://webpack.docschina.org/plugins/terser-webpack-plugin/#parallel)

类型： `Boolean|Number` 默认值： `true`

使用多进程并发运行以提高构建速度。 并发运行的默认数量： `os.cpus().length - 1` 。

> 并发运行可以显著提高构建速度，因此**强烈建议添加此配置** 。

> 如果你使用 **Circle CI** 或任何其他不提供 CPU 实际可用数量的环境，则需要显式设置 CPU 数量，以避免 `Error: Call retries were exceeded`（请参阅 [#143](https://github.com/webpack-contrib/terser-webpack-plugin/issues/143)，[#202](https://github.com/webpack-contrib/terser-webpack-plugin/issues/202) ）。

#### `Boolean` [](https://webpack.docschina.org/plugins/terser-webpack-plugin/#boolean)

启用/禁用多进程并发运行功能。

**webpack.config.js**

```
module.exports = {
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        parallel: true,
      }),
    ],
  },
};
```

#### `Number` [](https://webpack.docschina.org/plugins/terser-webpack-plugin/#number)

启用多进程并发运行并设置并发运行次数。

**webpack.config.js**

```
module.exports = {
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        parallel: 4,
      }),
    ],
  },
};
```

### `minify` [](https://webpack.docschina.org/plugins/terser-webpack-plugin/#minify)

类型： `Function` 默认值： `undefined`

允许你自定义压缩函数。 默认情况下，插件使用 [terser](https://github.com/terser-js/terser) 库。 对于使用和测试未发布的版本或 fork 的代码很帮助。

> ⚠️ **启用 `parallel` 选项时，在 `minify` 函数内部只能使用 `require`** 。

**webpack.config.js**

```
module.exports = {
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        terserOptions: {
          myCustomOption: true,
        },
        // Can be async
        minify: (file, sourceMap, minimizerOptions) => {
          // The `minimizerOptions` option contains option from the `terserOptions` option
          // You can use `minimizerOptions.myCustomOption`
          const extractedComments = [];

          // Custom logic for extract comments

          const { map, code } = require("uglify-module") // Or require('./path/to/uglify-module')
            .minify(file, {
              /* Your options for minification */
            });

          return { map, code, extractedComments };
        },
      }),
    ],
  },
};
```

### `terserOptions` [](https://webpack.docschina.org/plugins/terser-webpack-plugin/#terseroptions)

类型： `Object` 默认值： [默认](https://github.com/terser-js/terser#minify-options)

Terser 压缩[配置](https://github.com/terser-js/terser#minify-options) 。

**webpack.config.js**

```
module.exports = {
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        terserOptions: {
          ecma: undefined,
          parse: {},
          compress: {},
          mangle: true, // Note `mangle.properties` is `false` by default.
          module: false,
          // Deprecated
          output: null,
          format: null,
          toplevel: false,
          nameCache: null,
          ie8: false,
          keep_classnames: undefined,
          keep_fnames: false,
          safari10: false,
        },
      }),
    ],
  },
};
```

### `extractComments` [](https://webpack.docschina.org/plugins/terser-webpack-plugin/#extractcomments)

类型： `Boolean|String|RegExp|Function<(node, comment) -> Boolean|Object>|Object` 默认值： `true`

是否将注释剥离到单独的文件中（请参阅[详细信息](https://github.com/webpack/webpack/commit/71933e979e51c533b432658d5e37917f9e71595a)）。 默认情况下，仅剥离 `/^\**!|@preserve|@license|@cc_on/i` 正则表达式匹配的注释，其余注释会删除。 如果原始文件名为 `foo.js` ，则注释将存储到 `foo.js.LICENSE.txt` 。 `terserOptions.format.comments` 选项指定是否保留注释，即可以在剥离其他注释时保留一些注释，甚至保留已剥离的注释。

#### `Boolean` [](https://webpack.docschina.org/plugins/terser-webpack-plugin/#boolean)

启用/禁用剥离注释功能。

**webpack.config.js**

```
module.exports = {
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        extractComments: true,
      }),
    ],
  },
};
```

#### `String` [](https://webpack.docschina.org/plugins/terser-webpack-plugin/#string)

剥离 `all` 或 `some` （使用 `/^\**!|@preserve|@license|@cc_on/i` 正则表达式进行匹配）注释。

**webpack.config.js**

```
module.exports = {
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        extractComments: "all",
      }),
    ],
  },
};
```

#### `RegExp` [](https://webpack.docschina.org/plugins/terser-webpack-plugin/#regexp)

与指定表达式匹配的所有注释将会被剥离到单独的文件中。

**webpack.config.js**

```
module.exports = {
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        extractComments: /@extract/i,
      }),
    ],
  },
};
```

#### `Function<(node, comment) -> Boolean>` [](https://webpack.docschina.org/plugins/terser-webpack-plugin/#Functionnode-comment---Boolean-%EF%BC%83functionnode-comment-----boolean-functionnode-comment---boolean-%EF%BC%83functionnode-comment-----boolean)

与指定表达式匹配的所有注释将会被剥离到单独的文件中。

**webpack.config.js**

```
module.exports = {
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        extractComments: (astNode, comment) => {
          if (/@extract/i.test(comment.value)) {
            return true;
          }

          return false;
        },
      }),
    ],
  },
};
```

#### `Object` [](https://webpack.docschina.org/plugins/terser-webpack-plugin/#object)

允许自定义剥离注释的条件，指定剥离的文件名和标题。

**webpack.config.js**

```
module.exports = {
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        extractComments: {
          condition: /^\**!|@preserve|@license|@cc_on/i,
          filename: (fileData) => {
            // The "fileData" argument contains object with "filename", "basename", "query" and "hash"
            return `${fileData.filename}.LICENSE.txt${fileData.query}`;
          },
          banner: (licenseFile) => {
            return `License information can be found in ${licenseFile}`;
          },
        },
      }),
    ],
  },
};
```

##### `condition` [](https://webpack.docschina.org/plugins/terser-webpack-plugin/#condition)

类型： `Boolean|String|RegExp|Function<(node, comment) -> Boolean|Object>`

自定义需要剥离的注释。

**webpack.config.js**

```
module.exports = {
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        extractComments: {
          condition: "some",
          filename: (fileData) => {
            // The "fileData" argument contains object with "filename", "basename", "query" and "hash"
            return `${fileData.filename}.LICENSE.txt${fileData.query}`;
          },
          banner: (licenseFile) => {
            return `License information can be found in ${licenseFile}`;
          },
        },
      }),
    ],
  },
};
```

##### `filename` [](https://webpack.docschina.org/plugins/terser-webpack-plugin/#filename)

类型： `String|Function<(string) -> String>` 默认值： `[file].LICENSE.txt [query]`

可用的占位符： `[file]` ， `[query]` 和 `[filebase]` （webpack 5 使用 `[base]` ）。

剥离出来的注释将被存储到的文件的文件名。 默认是将后缀 `.LICENSE.txt` 附加到原始文件名。

> ⚠️我们强烈建议使用 `txt` 扩展名。

使用 `js` / `cjs` / `mjs` 扩展名可能会与现有资源文件冲突，从而导致代码运行出错。

**webpack.config.js**

```
module.exports = {
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        extractComments: {
          condition: /^\**!|@preserve|@license|@cc_on/i,
          filename: "extracted-comments.js",
          banner: (licenseFile) => {
            return `License information can be found in ${licenseFile}`;
          },
        },
      }),
    ],
  },
};
```

##### `banner` [](https://webpack.docschina.org/plugins/terser-webpack-plugin/#banner)

类型： `Boolean|String|Function<(string) -> String>` 默认值： `/*! For license information please see ${commentsFile} */`

指向剥离文件的标语文本将被添加到原始文件的顶部。 可以为 `false` （无标题）， `String` 或一个函数：`Function<(string) -> String>` ，该函数将被使用存储剥离的注释的文件名来调用。 标语内容将被合并到注释中。

**webpack.config.js**

```
module.exports = {
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        extractComments: {
          condition: true,
          filename: (fileData) => {
            // The "fileData" argument contains object with "filename", "basename", "query" and "hash"
            return `${fileData.filename}.LICENSE.txt${fileData.query}`;
          },
          banner: (commentsFile) => {
            return `My custom banner about license information ${commentsFile}`;
          },
        },
      }),
    ],
  },
};
```

## 示例 [](https://webpack.docschina.org/plugins/terser-webpack-plugin/#examples)

### 保留注释 [](https://webpack.docschina.org/plugins/terser-webpack-plugin/#preserve-comments)

剥离所有有效的注释（即 `/^\**!|@preserve|@license|@cc_on/i` ）并保留 `/@license/i` 注释。

**webpack.config.js**

```
module.exports = {
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        terserOptions: {
          format: {
            comments: /@license/i,
          },
        },
        extractComments: true,
      }),
    ],
  },
};
```

### 删除注释 [](https://webpack.docschina.org/plugins/terser-webpack-plugin/#remove-comments)

如果要在构建时去除注释，请使用以下配置：

**webpack.config.js**

```
module.exports = {
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        terserOptions: {
          format: {
            comments: false,
          },
        },
        extractComments: false,
      }),
    ],
  },
};
```

### 自定义压缩函数 [](https://webpack.docschina.org/plugins/terser-webpack-plugin/#custom-minify-function)

覆盖默认的 minify 函数 - 使用 `uglify-js` 进行压缩。

**webpack.config.js**

```
module.exports = {
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        minify: (file, sourceMap) => {
          // https://github.com/mishoo/UglifyJS2#minify-options
          const uglifyJsOptions = {
            /* your `uglify-js` package options */
          };

          if (sourceMap) {
            uglifyJsOptions.sourceMap = {
              content: sourceMap,
            };
          }

          return require("uglify-js").minify(file, uglifyJsOptions);
        },
      }),
    ],
  },
};
```
