---
title: npm命令记录
date: 2021-03-11 21:12:40
categories: 前端
---

## 强制清除npm指令
```
npm cache clean -f
```

## 设置下载源
>
>镜像使用方法（三种办法任意一种都能解决问题，建议使用第三种，将配>置写死，下次用的时候配置还在）:
>#### 1. 通过config命令
>npm config set registry [https://registry.npm.taobao.org]>(https://registry.npm.taobao.org/) npm info underscore （如果上面配置正确>这个命令会有字符串response）
>
>#### 2. 命令行指定
>
>npm --registry [https://registry.npm.taobao.org](https://registry.npm.taobao.org/) info underscore
>
>#### 3. 编辑 ~/.npmrc 加入下面内容
>
>registry = [https://registry.npm.taobao.org](https://registry.npm.taobao.org/)

