---
title: 什么是PNPM？
date: 2021-11-12 16:13:30
category: javascript
---

## 什么是PNPM？

> Fast, disk space efficient package manager

本质上他是一个包管理工具，和npm/yarn没有区别，主要优势在于

- 包安装速度极快
- 磁盘空间利用效率高

使用方法：

```
npm i pnpm -g
```





## 优势

### 一、速度快

![](https://upload-images.jianshu.io/upload_images/10024246-83a5f0645167c5e1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 二、高效利用磁盘空间

##### 硬链接(Hard Link)

    硬连接指通过索引节点来进行连接。在Linux的文件系统中，保存在磁盘分区中的文件不管是什么类型都给它分配一个编号，称为索引节点号(Inode Index)。在Linux中，多个文件名指向同一索引节点是存在的。一般这种连接就是硬连接。硬连接的作用是允许一个文件拥有多个有效路径名，这样用户就可以建立硬连接到重要文件，以防止“误删”的功能。其原因如上所述，因为对应该目录的索引节点有一个以上的连接。只删除一个连接并不影响索引节点本身和其它的连接，只有当最后一个连接被删除后，文件的数据块及目录的连接才会被释放。也就是说，文件真正删除的条件是与之相关的所有硬连接文件均被删除。

##### 软连接(Symbolic Link)

    另外一种连接称之为符号连接，也叫软连接。软链接文件有类似于Windows的快捷方式。它实际上是一个特殊的文件。在符号连接中，文件实际上是一个文本文件，其中包含的有另一文件的位置信息。



pnpm 内部使用`基于内容寻址`的文件系统来存储磁盘上所有的文件：

- 不会重复安装同一个包。使用` npm/yarn ` 的时候，如果100个包依赖` lodash ` ，那么就可能安装了100次` lodash ` ，磁盘中就有100个地方写入了这部分代码。但是` pnpm `会只在一个地方写入这部分代码，后面使用会直接使用` hard link ` 
- 即使一个包的不同版本，pnpm 也会极大程度地复用之前版本的代码。举个例子，比如 lodash 有 100 个文件，更新版本之后多了一个文件，那么磁盘当中并不会重新写入 101 个文件，而是保留原来的 100 个文件的 `hardlink`，仅仅写入那`一个新增的文件`。

### 三、支持monorepo

monorepo 的宗旨就是用一个 git 仓库来管理多个子项目，所有的子项目都存放在根目录的`packages`目录下，那么一个子项目就代表一个`package`。

 monorepo 管理工具` lerna` 

项目参考：` babel ` 

### 四、安全

之前在使用 npm/yarn 的时候，由于 node_module 的扁平结构，如果 A 依赖 B， B 依赖 C，那么 A 当中是可以直接使用 C 的，但问题是 A 当中并没有声明 C 这个依赖。因此会出现这种非法访问的情况。 但 pnpm 自创了一套依赖管理方式，很好地解决了这个问题，保证了安全性。



## 依赖管理

pnpm 会在全局的 store 目录里存储项目 `node_modules` 文件的 `hard links` 。因为这样一个机制，导致每次安装依赖的时候，如果是个相同的依赖，有好多项目都用到这个依赖，那么这个依赖实际上最优情况(即版本相同)只用安装一次。

#### 回归一下 node_modules 结构历史：

**第一阶段：npm@3 之前版本**

```text
node_modules
└─ foo
   ├─ index.js
   ├─ package.json
   └─ node_modules
      └─ bar
         ├─ index.js
         └─ package.json
```

- 依赖树层级太深，会导致 Windows 上的目录路径过长问题
- 相同包在不同的依赖项中需要时，会存在多个相同副本

**第二阶段：npm@3 版本，扁平化处理**

所有的依赖都被拍平到`node_modules`目录下，不再有很深层次的嵌套关系。这样在安装新的包时，根据 node require 机制，会不停往上级的`node_modules`当中去找，如果找到相同版本的包就不会重新安装，解决了大量包重复安装的问题，而且依赖层级也不会太深。

```text
node_modules
├─ foo
|  ├─ index.js
|  └─ package.json
└─ bar
   ├─ index.js
   └─ package.json
```

但还是存在一些问题

- 依赖结构的**不确定性**。
- 扁平化算法本身的**复杂性**很高，耗时较长。
- 项目中仍然可以**非法访问**没有声明过依赖的包



这就是为什么会产生依赖结构的`不确定`问题，也是 `lock 文件`诞生的原因，无论是`package-lock.json`(npm 5.x才出现)还是`yarn.lock`，都是为了保证 install 之后都产生确定的`node_modules`结构。

尽管如此，npm/yarn 本身还是存在`扁平化算法复杂`和`package 非法访问`的问题，影响性能和安全。



**第三阶段：pnpm**

由于扁平化算法的极其复杂，以及会存在多项目间相同依赖副本的情况。pnpm 在尝试解决这些问题时，放弃了扁平化处理 node_modules 的方式。而是采用 **硬链+软链** 方式。

```text
node_modules
├─ .pnpm
|  ├─ foo@1.0.0/node_modules/foo
|  |  └─ index.js
|  └─ bar@2.0.0/node_modules/bar
├─ foo -> .pnpm/foo@1.0.0/node_modules/foo
└─ bar -> .pnpm/bar@2.0.0/node_modules/bar
```

node_modules 根目录中的包只是一个符号链接。`require('foo')` 将执行 `node_modules/.pnpm/foo@1.0.0/node_modules/foo/indexjs` 中的文件（这里是硬链接），而不是 `node_modules/foo/index.js` 中的文件。

**这种布局结构的一大好处是只有真正在依赖项中（package.json dependences）的包才能访问**

**举个🌰**

安装一个`express` 依赖，会在 node_modules 中形成这样两个目录结构:

```text
node_modules/express/...
node_modules/.pnpm/express@4.17.1/node_modules/xxx
```

其中第一个路径是 nodejs 正常寻找路径会去找的一个目录，如果去查看这个目录下的内容，会发现里面连个 `node_modules` 文件都没有：

```text
▾ express
    ▸ lib
      History.md
      index.js
      LICENSE
      package.json
      Readme.md
```

实际上这个文件只是个软连接，它会形成一个到第二个目录的一个软连接(类似于软件的快捷方式)，这样 node 在找路径的时候，最终会找到 .pnpm 这个目录下的内容。

其中这个 `.pnpm` 是个虚拟磁盘目录，然后 express 这个依赖的一些依赖会被平铺到 `.pnpm/express@4.17.1/node_modules/` 这个目录下面，这样保证了依赖能够 require 到，同时也不会形成很深的依赖层级。

在保证了 nodejs 能找到依赖路径的基础上，同时也很大程度上保证了依赖能很好的被放在一起。



## 目前不适用的场景

前面有提到关于 pnpm 的主要问题在于 symlink(软链接)在一些场景下会存在兼容的问题，可以参考作者在 nodejs 那边开的一个 discussion：[https://github.com/nodejs/node/discussions/37509](https://link.zhihu.com/?target=https%3A//github.com/nodejs/node/discussions/37509)

在里面作者提到了目前 nodejs 软连接不能适用的一些场景，希望 nodejs 能提供一种 link 方式而不是使用软连接，同时也提到了 pnpm 目前因为软连接而不能使用的场景:

- Electron 应用无法使用 pnpm
- 部署在 [lambda](https://link.zhihu.com/?target=https%3A//docs.aws.amazon.com/lambda/latest/dg/gettingstarted-package.html) 上的应用无法使用 pnpm

一些 nodejs 基础库不支持 symlink 的情况导致使用 pnpm 无法正常工作，不过这些库在迭代更新之后也会支持这一特性。



## 总结

pnpm 方式的实现精髓

1. 通过软链的形式，使得 require 可以正常引用；同时对非真正依赖的项目做隔离（避免引用依赖的混乱）
2. `.pnpm` 的存在避免了循环引用和层级过深的问题（都在其第一层）
3. 硬链使得不同项目相同依赖只存在一个副本，减少磁盘空间



## 未来会做的一些事情

### 脱离 nodejs

具体可以参考 [https://github.com/pnpm/pnpm/discussions/3434](https://link.zhihu.com/?target=https%3A//github.com/pnpm/pnpm/discussions/3434)

- 安装 pnpm 的， 可以基本上脱离掉 nodejs 这个 runtime 去进行安装使用。
- 可以通过 pnpm 来使用不同版本的 nodejs 来去做依赖安装，类似于 nvm 提供的功能。

目前该特性其实已经到了 beta 版本，可以参考 [https://www.npmjs.com/package/@pnpm/beta](https://link.zhihu.com/?target=https%3A//www.npmjs.com/package/%40pnpm/beta) 这个包。管理不同版本的 nodejs 功能可以参考 env 这个子命令: [https://pnpm.io/cli/env](https://link.zhihu.com/?target=https%3A//pnpm.io/cli/env)

### 使用 rust 写一些模块

具体可以看 [https://github.com/pnpm/pnpm/discussions/3419](https://link.zhihu.com/?target=https%3A//github.com/pnpm/pnpm/discussions/3419) 这个 discussion 讨论的内容，大概就是作者希望给 pnpm 的一些子命令提供一些 rust 的 cli wrapper 来做提升性能使用。



