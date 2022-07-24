---
title: chrome 开发者工具 - local overrides
date: 2022-03-07 16:28:03
categories: 前端
---
#### 使用chrome 作为本地网络服务

chrome 65+ 新功能， 使用我们自己的本地资源覆盖网页所使用的资源，可以使用本地css文件覆盖网页的css文件，修改样式。

将本地的文件夹映射到网络，在chrome开发者功能里面对css 样式的修改，都会直接改动本地文件，页面重新加载，使用的资源也是本地资源，达到持久化的效果。

然后就是，使用 local override 功能，来搭建一个本地的静态网页服务器，搭建过程非常简单，根据原文中的步骤（假设访问的域名 chromeserver.com）：

1.  搭建local overrider的根目录， C:/Dev/Web/chromelocal，
2.  在根目录中新建文件夹，以 chromeserver.com 命名，进入该文件目录，新建一个 index.html
3.  打开chrome 开发者工具， **sources** --> **Overrides** --> 勾选 **Enable Local Overriders** --> 点击 **Select folder for overrides** ,选择文件 C:/Dev/Web/chromelocal
4.  结果图 ：
![](https://upload-images.jianshu.io/upload_images/10024246-f0887031a6fe3eff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**在打开了 开发者工具的tab中**，访问 http://chromeserver.com/，就可以看到页面了。

**扩展：**

#### 1\. 设置持久化。

经过上面的设置后，尝试打开其他网页， 在Elements 面板中进行的样式，

![](https://upload-images.jianshu.io/upload_images/10024246-41cb2cd8dec8fdaa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后，在sources 中，就会自动生成对应域名的文件（在本地磁盘上新建文件）
![](https://upload-images.jianshu.io/upload_images/10024246-61105f4d338efa93.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

新开tab 中重新打开页面，修改依然生效，已经将本地的资源文件映射到了网络，跟上面工作区设置持久化的效果类似。

**在chrome 中点击对应index.css文件，可以在右侧面板中，直接修改未格式化的文件**
![](https://upload-images.jianshu.io/upload_images/10024246-511b17a32125e20d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

或者在本地磁盘中，使用IDE打开文件 C:/Dev/Web/chromelocal/static.segmentfault.com/v-5badf55c/index/css/index.css,格式化文件后，再修改里面的内容，也可以自动刷新，显示文件更改。

#### 2\. 模拟接口数据

对于返回json 数据的接口，可以利用该功能，简单模拟返回数据。

比如：
api 为 http://www.xxx.com/api/v1/list对应在chromelocal 目录下，新建文件 www.xxx.com/api/v1/list，list 文件中的内容，与正常接口返回格式相同，
![](https://upload-images.jianshu.io/upload_images/10024246-2f8c13b8906a9478.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对象或者数组类型，从而覆盖掉原接口请求。

#### 相关功能

[workspaces](https://developer.chrome.com/docs/devtools/workspaces/) ，chrome 很早就引入了 workspaces ， 这个功能允许把DevTools 当做IDE使用，
只要在选择了 Add folder to workspace 后，允许资源访问，在chrome DevTools 中的改变，相应也会保存在本地版本中，跟使用文本编辑器类似的类似的功能。

在 Chrome 63 [What's New In DevTools](https://developers.google.com/web/updates/2017/10/devtools-release-notes#workspaces)中 提及 workspace2.0 发布，增强用户体验，不过延期了，
