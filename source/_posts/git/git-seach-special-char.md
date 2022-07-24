---
title: 如何在Github中搜索特殊字符
date: 2021-03-09 21:21:13
categories: git
---
github仓库中搜索包含特殊字符的字符串(例如" example.com"或" Example：")
## 方法
使用空格代替字符，然后将搜索词放在引号中。 它会给您最接近的结果。
我有一个库调用sg.createNode，所以我搜索了"sg createNote"，其中大多数返回的结果包括sg.createNote。

## 结果

![](https://upload-images.jianshu.io/upload_images/10024246-e3184421d579a0d4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>暂时没有找到能够精准匹配特殊字符的方法，但是已经基本够用了