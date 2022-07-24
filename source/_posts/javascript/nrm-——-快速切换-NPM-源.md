---
title: nrm-——-快速切换NPM源
date: 2021-03-11 21:12:40
categories: 前端
---

不同地区访问不同的镜像速度可能有差异，然后各个镜像各自都可能有少数包暂时没有同步，因此，有时候需要切换 NPM 镜像。相比每次切换时都手动指定相应参数，使用***nrm ***要方便的多。
npm registry 管理工具 [nrm](https://github.com/Pana/nrm), 能够查看和切换当前使用的registry.

###Install
```
$ npm install -g nrm
```
###列出可选的源
```
$ nrm ls

* npm ---- https://registry.npmjs.org/
  cnpm --- http://r.cnpmjs.org/
  eu ----- http://registry.npmjs.eu/
  au ----- http://registry.npmjs.org.au/
  sl ----- http://npm.strongloop.com/
  nj ----- https://registry.nodejitsu.com/
```
带 ```*```的是当前使用的源，上面的输出表明当前源是官方源。

###切换
切换到cnpm
```
$ nrm use cnpm //switch registry to cnpm

    Registry has been set to: http://r.cnpmjs.org/
```
### 增加源

你可以增加定制的源，特别适用于添加企业内部的私有源。[私有源可以使用cnpmjs架设](http://segmentfault.com/a/1190000000368906)。

```
nrm add  <registry> <url> [home]

```

### 删除源

```
nrm del <registry>

```

### 测试速度

你还可以通过 `nrm test` 测试相应源的响应时间。

例如，测试官方源的响应时间：

```
; nrm test npm                                                                                                                               

  npm ---- 1328ms

```

测试所有源的响应时间：

```
; nrm test                                                                                                                                   

  npm ---- 891ms
  cnpm --- 1213ms
  eu ----- 3859ms
  au ----- 1073ms
  sl ----- 4150ms
  nj ----- 8008ms

```

注意，为了取得较准确的结果，可以考虑多次测试取平均值。
## 项目主页

[github.com/Pana/nrm](https://github.com/Pana/nrm)
