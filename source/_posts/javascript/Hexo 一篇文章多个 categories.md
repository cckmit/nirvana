---
title: Hexo 一篇文章多个 categories
date: 2022-02-22 16:28:03
categories: 前端
---
## 前言

在很多情况下，我们希望在 Hexo 中写的一篇文章能够同时属于多个分类，例如我写了一篇介绍webpack的文章，我既想将放在 `前端`这个分类下，又想将它放在 `Webpack 专场`这个分类。

按照官方的解释，`categories` 这个选项有两种配置方法（其实有三种）。那我们就来讲讲这三种配置方法。

### 子分类

下面的分类配置会将该文章放到 `Sports/Baseball` 这个分类下。
```
categories:
  - Sports
  - Baseball
```

同样的作用还可以这样写：
```
categories: [Sports,Baseball]
```
上面两种写法最终的效果都是一样的，都是将文章放在了一个子分类目录下。

### 多个分类

如果我们的要求是将文章同时分到两个或者多个不同的类目下呢？官方给出的方法是：
```
categories:
  - [Sports]
  - [Baseball]
```

只需要用中括号将独立的分类括起来即可，这样上面的文章就会被分类在 `Sports` 和 `Baseball` 这两个不同的目录中了。

扩展一下，如果我们要将其分类到 `Sports/Baseball` 和 `Play` 两个不同的目录下（一个是子目录，一个是一级目录），我们该怎么写呢？如下：
```
categories:
  - [Sports,Baseball]
  - [Play]
```