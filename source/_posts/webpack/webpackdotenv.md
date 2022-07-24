---
title: dotenv，从文件加载环境变量
date: 2021-04-14 15:34:17
categories: webpack
---
Node.js程序运行时加载不同的配置，比如开发环境和生产环境的数据数据库配置就可能不一样，使用 process.env.DB_HOST 环境变量，可以在Node.js程序内部方便获取参数信息。

但是，程序启动时，怎样将环境变量传递给程序，这可能会是一个相对麻烦的事情，因为这关系到操作系统层的配置问题。

## dotenv
dotenv是一个可以使得Node.js从文件中加载环境变量的库，使用dotenv，我们只需要将程序的环境变量配置写在.env文件中。
```
# .env file
DB_HOST=localhost
DB_USER=root
DB_PASS=s1mpl3
```
然后，在Node.js程序启动时运行：
```
require('dotenv').config()
```
接着，我们就可以在接下来的程序中方便地使用环境变量了：
```
const db = require('db')
db.connect({
  host: process.env.DB_HOST,
  username: process.env.DB_USER,
  password: process.env.DB_PASS
})
```