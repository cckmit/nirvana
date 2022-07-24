---
title: ts-node直接运行typescript文件
date: 2021-03-11 21:12:40
categories: 前端
tags: [TypeScript]
---

  正常ts文件都要编译成JS文件才能运行，但是在开发时有时需要运行ts文件，但是每次都要配置package.json，还要安装，后来看到ts-node可以在vscode上运行ts文件。

### 安装环境
```
//全局安装typescript和typescript-node
npm install -g typescript
npm install -g typescript-node
```
  安装完成后就可以不用手动去编译成js文件，可以直接运行ts文件
```
//foo.ts
let foo = {
  baz: {
    a: 1
  }
}
console.log(foo);
```
### 执行命令***ts-node ****.ts***
```
$ ts-node foo.ts
{ baz: { a: 1 } }
```
