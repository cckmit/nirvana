---
title: Error--Can't resolve 'fs' in （webpack 2配置CSS-loadeers）
date: 2021-03-11 21:12:40
categories: webpack
---

#### 前言，在之前有配置过webpack 2 中css loaders，但是怎么也不正确

报错：

```
ERROR in ./~/convert-source-map/index.js
Module not found: Error: Can't resolve 'fs' in '/Users/ceshi/Desktop/webpack/node_modules/convert-source-map'
 @ ./~/convert-source-map/index.js 2:9-22
 @ ./~/css-loader/lib/css-base.js
 @ ./~/css-loader!./app/style.css
 @ ./app/style.css
 @ ./app/main.js
```

![](https://upload-images.jianshu.io/upload_images/10024246-e3392ce8d53748bc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


解决方法，修改webpack的配置文件，增加一句代码

```
 node: {
        fs: 'empty'
    },
```

![](https://upload-images.jianshu.io/upload_images/10024246-525a508c7db21758.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
