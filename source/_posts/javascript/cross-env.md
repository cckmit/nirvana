---
title: npm常用模块之cross-env使用
date: 2021-03-10 22:18:53
categories: 前端
---
**cross-env**这是一款运行跨平台设置和使用环境变量的脚本。

**为什么需要cross-env?**

`NODE_ENV=production`像这样设置环境变量时，大多数Windows命令提示符都会阻塞 。（`Windows`上的`Bash`是例外，它使用本机Bash。）同样，Windows和POSIX命令使用环境变量的方式也有所不同。对于POSIX，您可以使用：`$ENV_VAR` 和在Windows上可以使用`%ENV_VAR%`。
`cross-env`这样，您就可以使用单个命令，而不必担心为平台正确设置或使用环境变量。就像在POSIX系统上运行时一样，只需对其进行设置即可，并且`cross-env`会妥善设置。

在window下配置

```
#node中常用的到的环境变量是NODE_ENV，首先查看是否存在 
set NODE_ENV 

#如果不存在则添加环境变量 
set NODE_ENV=production 

#环境变量追加值 set 变量名=%变量名%;变量内容 
set path=%path%;C:\web;C:\Tools 

#某些时候需要删除环境变量 
set NODE_ENV=

```

在linux下配置

```
#node中常用的到的环境变量是NODE_ENV，首先查看是否存在
echo $NODE_ENV

#如果不存在则添加环境变量
export NODE_ENV=production

#环境变量追加值
export path=$path:/home/download:/usr/local/

#某些时候需要删除环境变量
unset NODE_ENV

#某些时候需要显示所有的环境变量
env

```

[更多使用文档请点击访问cross-env工具官网](https://www.npmjs.com/package/cross-env)。

## 安装

一键安装不多解释

```
npm install --save-dev cross-env

```

> 警告！确保在安装软件包时可以正确拼写，以免错误安装恶意软件

> 注意：cross-env的版本6仅支持Node.js 8和更高版本，才能在Node.js 7或更低版​​本的安装版本5上使用`npm install --save-dev cross-env@5`

## 使用

我在npm脚本中使用了它：

```
{
  "scripts": {
    "build": "cross-env NODE_ENV=production webpack --config build/webpack.config.js"
  }
}

```

最终，执行的命令（使用[cross-spawn](https://www.npmjs.com/package/cross-spawn)）为：

```
webpack --config build/webpack.config.js

```

该`NODE_ENV`环境变量将被设置`cross-env`

您也可以将命令拆分为多个命令，或者将环境变量声明与实际命令执行分开。您可以这样操作：

```
{
  "scripts": {
    "parentScript": "cross-env GREET=\"Joe\" npm run childScript",
    "childScript": "cross-env-shell \"echo Hello $GREET\""
  }
}

```

其中`childScript`包含要执行的实际命令并`parentScript`设置要使用的环境变量。然后，而不是运行`childScript`，而是运行父项。这对于使用不同的env变量启动同一命令或当环境变量太长而无法将所有内容放在一行中时非常有用。这也意味着`$GREET`即使在通常要求使用Windows的Windows上，也可以使用`env var`语法`%GREET%`。

如果您在美元符号前加上奇数个反斜杠，则不会替换表达式语句。请注意，这意味着在转义JSON字符串后使用了反斜杠。`"FOO=\\$BAR"`将不会被替换。 `"FOO=\\\\$BAR"`虽然会被替换。

最后，如果要传递JSON字符串（例如，使用`ts-loader`时），则可以执行以下操作：

```
{
  "scripts": {
    "test": "cross-env TS_NODE_COMPILER_OPTIONS={\\\"module\\\":\\\"commonjs\\\"} node some_file.test.ts"
  }
}

```

要特别注意三倍反斜线`(\\\)`之前的 双引号`(")`和缺少的单引号`(')`。为了同时在Windows和UNIX上运行，必须同时满足这两个条件。

`cross-env` 与 `cross-env-shell`
该`cross-env`模块公开了两个容器：`cross-env`和`cross-env-shell`。第一个使用来执行命令[cross-spawn](https://www.npmjs.com/package/cross-spawn)，第二个使用`shellNode`的选项`spawn`。

主要用例`cross-env-shell`是当您需要在整个嵌入式命令行脚本中而不是仅在一个命令中设置环境变量时。

例如，如果您希望将环境变量应用于一系列串联的命令，则需要将其用引号引起来并使用 `cross-env-shell`代替`cross-env`。

```
{
  "scripts": {
    "greet": "cross-env-shell GREETING=Hi NAME=Joe \"echo $GREETING && echo $NAME\""
  }
}

```

经验法则是：如果要传递给`cross-env`包含要解释的特殊`Shell`字符的命令，请使用 `cross-env-shell`。否则请坚持`cross-env`。

在Windows上`cross-env-shell`，如果要 在程序内部处理[信号事件](https://nodejs.org/api/process.html#process_signal_events)，则 需要使用。常见的情况是当您想要捕获`SIGINT`通过`Ctrl + C`在命令行界面上按调用的 事件时。
