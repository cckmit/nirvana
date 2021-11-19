---
title: showdoc介绍
date: 2021-10-22 16:13:30
category: javascript
---

`ShowDoc`一个非常适合团队的在线API文档工具，也支持用`docker`自建文档服务，不过为了方便演示，我直接用了平台在线服务。[官网地址](https://www.showdoc.com.cn/item/index)

可以使用`markdown`语法来写API文档、数据字典文档、技术文档、在线`excel`文档。但像我这种资深的懒人程序员，其实更看重的是`showdoc`的自动化生成文档的特性，它可以从代码注释中自动生成API文档，或者搭配`RunApi`客户端（类似postman的api调试工具）一边调试接口、一边自动生成文档。

下边从头演示下，来瞅瞅这玩意好用在哪？

![](https://upload-images.jianshu.io/upload_images/10024246-b89d01a6640ee5c0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 初识 ShowDoc

`ShowDoc`新建项目可选常规的API文档、在线表格、或者单页文档（不支持目录分层），允许对项目文档设置访问密码，自定义域名，这里并不是真正意义上的“域名”，只是在文档服务域名后加了一级目录，例如：

```www.showdoc.com.cn/程序员内点事```

![](https://upload-images.jianshu.io/upload_images/10024246-7daf2605c06485b2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以复制现有的项目，或直接导入`Postman`、`swagger`的API接口配置Json文件。提供的开放API是自动化生成文档的关键，先记住有`api_key`、`api_token`这两个属性，后边详细讲。

![](https://upload-images.jianshu.io/upload_images/10024246-870df9043df900f6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

进入项目后点击右上角 **+** 编辑文档，`ShowDoc`预置了几种文档模板，也可以把自定义的文档存为模板；支持在线`Mock`服务，提前定义好接口的数据格式，先提供在线临时接口，这样就可以和前端同步开发，后边无缝切换；还有个简单的API在线测试功能。

![](https://upload-images.jianshu.io/upload_images/10024246-d04a2a88b05bcae9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在线表格样式很简洁
![](https://upload-images.jianshu.io/upload_images/10024246-fcc2fe8ca7b1c84b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
导出文档有`word`、`Markdown`两种格式。
![](https://upload-images.jianshu.io/upload_images/10024246-a99053f13ecb5d1d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
支持版本控制，能看到每次修改的记录，回滚任意一个版本的修改。
![](https://upload-images.jianshu.io/upload_images/10024246-2570ff094e0409b1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在向别人分享在线文档时，如果不想将整个API目录都暴漏，可以选择进行单页面分享。
![](https://upload-images.jianshu.io/upload_images/10024246-5d7861a4ecc761ec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
看到这感觉`showdoc`很普通啊，好像没什么特别的地方，上边的这些文档都是需要我们手动书写的，比较繁琐不推荐这么搞，接下来咱们看看如何自动化生成文档。

### 自动生成文档

`showdoc`有三种自动生成API文档的方式：

*   使用Runapi工具自动生成（**推荐**）
*   使用程序代码注释自动生成
*   自动生成数据字典
*   自己写程序调用接口来生成

**Runapi工具**

`Runapi`是一个以接口为核心的开发测试工具（可以看做是`Postman`的精简版）。目前客户端支持`win`、`mac`、`linux`平台和在线版 ，包含接口测试、自动流程测试、Mock数据、项目协作等功能。

单纯的`Runapi`和`Postman`相比优势并不大，而与`showdoc`配合使用效率比较显著，用`runapi`测试接口的同时它将自动生成API文档到`showdoc`，也可共用`showdoc`的团队管理机制实现多人协作。

`Runapi`客户端可以创建带调试的API接口文档、或者Markdown格式的文档。

![](https://upload-images.jianshu.io/upload_images/10024246-be24ae37e2962fc7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

比如我们新建个项目“`程序员内点事`”，分别建三个接口“`点在`”、“`在看`”、“`关注`”，紧接着快速生成参数和响应结果数据并保存。

![](https://upload-images.jianshu.io/upload_images/10024246-88a93a8ee8d65e42.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击右上角的`文档链接`设置访问密码，不填默认是公开的，复制文档链接在浏览器中打开，看到API接口文档已经生成。runapi还有全局参数、环境隔离。其实`Postman`也支持这样的功能，不过毕竟不是国内产品，网络访问等方面很受限制。
![](https://upload-images.jianshu.io/upload_images/10024246-dd01e7c28467efaf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

还有一个比较好的地方，`Runapi`支持接口执行前后的脚本，比如响应数据的断言测试，弹框显示都挺好用的。

![](https://upload-images.jianshu.io/upload_images/10024246-bc132a64f08d1da9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 代码注释

把接口的信息写在注释里也可以自动生成文档到`showdoc`，但这种我并不太喜欢，主要是侵入性比较强，让代码的阅读性变的比较差，一坨坨看着很不爽。

```
   /**
    * showdoc
    * @catalog 测试文档/用户相关
    * @title 用户注册
    * @description 用户注册的接口
    * @method post
    * @url https://www.showdoc.com.cn/home/user/login
    * @param username 必选 string 用户名  
    * @param password 必选 string 密码  
    * @param name 可选 string 用户昵称  
    * @return {"error_code":0,"data":{"uid":"1","username":"12154545","name":"吴系挂","groupid":2,"reg_time":"1436864169","last_login_time":"0"}}
    * @return_param groupid int 用户组id
    * @return_param name string 用户昵称
    * @remark 这里是备注信息
    * @number 99
    */
    public Object register(){
```

这种方式的实现也比较简单，还记得前边的提到的`api_key`、`api_token`这两个属性嘛，现在派上用场了，下边我用windows环境演示。

首先本地要有git环境：

```
https://npm.taobao.org/mirrors/git-for-windows/v2.17.0.windows.1/Git-2.17.0-64-bit.exe
```

再下载showdoc官方提供的脚本

```https://www.showdoc.cc/script/showdoc_api.sh```

修改`showdoc_api.sh`，替换我们`api_key`和`api_token`变量值，URL如果没搭建自己的文档服务不用改。
![](https://upload-images.jianshu.io/upload_images/10024246-fa2a05ab5d54a185.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将`showdoc_api.sh`放在你的项目目录下，直接双击运行，脚本会自动递归扫描本目录和子目录的所有文本代码文件，并生成API文档。

![](https://upload-images.jianshu.io/upload_images/10024246-db7c1573ad44dfe2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`showdoc_api.sh`生成的文档会放进你填写`api_token`的这个项目里。

![](https://upload-images.jianshu.io/upload_images/10024246-648621284daf7fa0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 生成数据字典

如果我们想直接从数据库字典表生成数据字典文档，`showdoc`也是支持的，先下载官方提供的脚本

```wget https://www.showdoc.cc/script/showdoc_db.sh```

修改脚本里的配置，数据库、`api_key`、`api_token`等信息，直接执行后数据库表结构信息同步到`showdoc`。

![](https://upload-images.jianshu.io/upload_images/10024246-df2116dab82904e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如下配置的变量名和解释
![](https://upload-images.jianshu.io/upload_images/10024246-de113d8d1242ae2f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

效果就是如下图这样，生成了数据表字典文档，在一些特定场景下还是很方便的。

![](https://upload-images.jianshu.io/upload_images/10024246-c5915fd591e47638.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 开放API

`showdoc`开放了文档编辑的API，我们可以在代码中调用API创建、编辑文档。这样使用的场景就比较灵活了。

```https://www.showdoc.cc/server/api/item/updateByApi```

API参数如下，文档内容，可传递markdown格式的文本或者html源码都可以。

![](https://upload-images.jianshu.io/upload_images/10024246-eef2e2e73daf0dd3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

测试一下接口组装必要的参数，用简易在线API调试工具发送

```
{
  "api_key": "8e52cbad736aa9832b92acc4b34a830e961861279",
  "api_token": "9dcd8333afa7cde63bf84f8f0db5d2b2116079256",
  "page_title": "xiaofu",
  "page_content": "nihao"
}
```

![](https://upload-images.jianshu.io/upload_images/10024246-872fa08e0b23f082.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

看到在`showdoc`对应的项目里已经创建了名字为`xiaofu`的文档。
![](https://upload-images.jianshu.io/upload_images/10024246-1f4361bb97c2fce7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 说两句

前边说过`showdoc`现有的功能`postman`基本都支持，但`postman`功能过于繁杂不够简洁，加上网络条件等诸多限制，协同办公的效率并不高，而`Runapi`配合`showdoc`在某些场景下能够很大程度上提升我们开发交付的效率，所以能自动生成的绝对不手写！
