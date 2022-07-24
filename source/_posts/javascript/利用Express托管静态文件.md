---
title: 利用Express托管静态文件
date: 2021-06-10 16:43:47
categories: 前端
---
为了提供诸如图像、CSS 文件和 JavaScript 文件之类的静态文件，请使用 Express 中的 `express.static` 内置中间件函数。

此函数特征如下：

```
express.static(root, [options])

```

The `root` argument specifies the root directory from which to serve static assets. For more information on the `options` argument, see [express.static](https://www.expressjs.com.cn/4x/api.html#express.static).

例如，通过如下代码就可以将 `public` 目录下的图片、CSS 文件、JavaScript 文件对外开放访问了：

```
app.use(express.static('public'))

```

现在，你就可以访问 `public` 目录中的所有文件了：

```
http://localhost:3000/images/kitten.jpg
http://localhost:3000/css/style.css
http://localhost:3000/js/app.js
http://localhost:3000/images/bg.png
http://localhost:3000/hello.html

```

Express 在静态目录查找文件，因此，存放静态文件的目录名不会出现在 URL 中。

如果要使用多个静态资源目录，请多次调用 `express.static` 中间件函数：

```
app.use(express.static('public'))
app.use(express.static('files'))

```

访问静态资源文件时，`express.static` 中间件函数会根据目录的添加顺序查找所需的文件。

注意：For best results, [use a reverse proxy](https://www.expressjs.com.cn/en/advanced/best-practice-performance.html#use-a-reverse-proxy) cache to improve performance of serving static assets.

To create a virtual path prefix (where the path does not actually exist in the file system) for files that are served by the `express.static` function, [specify a mount path](https://www.expressjs.com.cn/4x/api.html#app.use) for the static directory, as shown below:

```
app.use('/static', express.static('public'))

```

现在，你就可以通过带有 `/static` 前缀地址来访问 `public` 目录中的文件了。

```
http://localhost:3000/static/images/kitten.jpg
http://localhost:3000/static/css/style.css
http://localhost:3000/static/js/app.js
http://localhost:3000/static/images/bg.png
http://localhost:3000/static/hello.html

```

然而，the path that you provide to the `express.static` function is relative to the directory from where you launch your `node` process. If you run the express app from another directory, it’s safer to use the absolute path of the directory that you want to serve:

```
app.use('/static', express.static(path.join(__dirname, 'public')))

```

欲了解更多关于 `serve-static` 函数及其参数的知识，请参考 [serve-static](https://www.expressjs.com.cn/resources/middleware/serve-static.html)。
