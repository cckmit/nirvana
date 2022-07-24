---
title: nrm管理工具切换 NPM 镜像源
date: 2021-03-11 21:12:40
categories: 前端
---

由于 `npm原始源`会请求国外服务器，对于国内的开发者着实不友好，使用 `nrm`(全称：NPM registry manager) 可以快速地在不同 `npm镜像源`之间进行快速切换。
**github 地址:**[nrm -- NPM registry manager](https://github.com/Pana/nrm)

# 安装
[图片上传中...(image.png-72bb4a-1590129698542-0)]


```
> npm install -g nrm
```

> npm install packageName -g
> // 安装 npm 包的指令，-g 是 global 的缩写，意为全局安装

# 使用

执行 `nrm ls` 命令可查看所有可切换的 `npm镜像源` , 带 `*` 为当前正在使用的 `npm镜像源`。

```
>nrm ls
  npm ---- https://registry.npmjs.org/
  cnpm --- http://r.cnpmjs.org/
* taobao - https://registry.npm.taobao.org/
  nj ----- https://registry.nodejitsu.com/
  rednpm - http://registry.mirror.cqupt.edu.cn/
  npmMirror  https://skimdb.npmjs.com/registry/
  edunpm - http://registry.enpmjs.org/
```

执行 `nrm use npmImageName` 即可切换到列表中存在的 `npm镜像源`，使用淘宝镜像为例：

```
>nrm use taobao
Registry has been set to: https://registry.npm.taobao.org/
```

此后再调用 `npm install` 下载任意包都会使用新的 `npm镜像源`进行下载。
