---
title: import()实现按需加载
date: 2022-03-27 16:28:03
categories: 前端
---
我们可以使用 `import` 语句初始化的加载依赖项

```
import defaultExport from "module-name";
import * as name from "module-name";
//...
```

但是静态引入的`import` 语句需要依赖于 `type="module"` 的`script`标签，而且有的时候我们希望可以根据条件来按需加载模块，比如以下场景：

*   当静态导入的模块很明显的降低了代码的加载速度且被使用的可能性很低，或者并不需要马上使用它
*   当静态导入的模块很明显的占用了大量系统内存且被使用的可能性很低
*   当被导入的模块，在加载时并不存在，需要异步获取
*   当被导入的模块有副作用，这些副作用只有在触发了某些条件才被需要时

这个时候我们就可以使用动态引入`import()`，它跟函数一样可以用于各种地方，返回的是一个 `promise`

基本使用如下两种形式

```
//形式 1
import('/modules/my-module.js')
  .then((module) => {
    // Do something with the module.
  });

 //形式2
let module = await import('/modules/my-module.js');
```

浏览器支持情况
![](https://upload-images.jianshu.io/upload_images/10024246-6eb56288c817511a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

