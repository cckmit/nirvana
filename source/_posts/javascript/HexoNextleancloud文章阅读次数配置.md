---
title: Hexo Next leancloud文章阅读次数配置
date: 2021-04-21 14:28:48
categories: 前端
---
# 前言

由于`Next`主题修复了`leancloud`的安全问题, 导致以前`leancloud`失效, 所以重新介绍如何配置`leancloud`

> 所有使用该插件而未经修复的NexT站点或使用类似方法集成Leancloud访客统计功能的站点都被认为是不安全的，请尽快修复

效果如图
![](https://upload-images.jianshu.io/upload_images/10024246-4c34cbaf05fa0929.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 注册Leancloud并创建应用

1.  首先，前往Leancloud官网[leancloud.cn](https://leancloud.cn/dashboard/login.html#/signup)进行注册，并登陆。

2.  点击**创建应用**
    ![](https://upload-images.jianshu.io/upload_images/10024246-89aca6e79e05f569.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3.  输入应用的名称(随便起都行), 选择**开发版**, 点击**创建**
    ![](https://upload-images.jianshu.io/upload_images/10024246-097016813d541b77.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

4.  创建成功后点击右上角的设置**小齿轮**
    ![](https://upload-images.jianshu.io/upload_images/10024246-3db198f1151cdcbf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

5.  点击**存储**-**创建Class**-**Class名称**设置为`Counter`(必须为`Counter`)-选择**无限制**-点击**创建**
    ![](https://upload-images.jianshu.io/upload_images/10024246-432bb8b837df5c25.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

6.  创建后点击**设置**-点击**应用Key**-获取**App ID**和**App Key**
    ![](https://upload-images.jianshu.io/upload_images/10024246-9535fa3e0ed7959c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

7.  将获取到的**App ID**和**App Key**设置到`Next`主题配置文件`_config.yml`
    *配置文件已存在这个配置, 只要把id和key加上去就好了*
```
leancloud_visitors:
 enable: true
 app_id: <<your app id>>
 app_key: <<your app key>>
```

8.  点击**安全中心**, 填写自己博客对应的域名（注意协议、域名和端口号需严格一致）
    ![](https://upload-images.jianshu.io/upload_images/10024246-0a2fe8df57f0f3c6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

9.  点击**云引擎**-点击**部署**-点击**在线编辑**
    ![](https://upload-images.jianshu.io/upload_images/10024246-e403d7af8cb3c367.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

10.  点击**创建函数**-选择**Hook**-AV.Cloud.选择**beforeSave**-类选择**Counter**-**添加代码**-点击**保存**

![](https://upload-images.jianshu.io/upload_images/10024246-fd6294350d1575b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-e68e0a9d45123d5a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
var query = new AV.Query("Counter");
if (request.object.updatedKeys.indexOf('time') !== -1) {
     return query.get(request.object.id).then(function (obj) {
     if (obj.get("time") + 1 !== request.object.get("time")) {
     throw new AV.Cloud.Error('Invalid update!');
   }
 })
}
```
11.  点击**部署**
    ![](https://upload-images.jianshu.io/upload_images/10024246-6f2cb6e065606ee4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

    ![](https://upload-images.jianshu.io/upload_images/10024246-7f0f70d529036253.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

12.  待出现红框处的成功部署信息后，点击**关闭**
    ![](https://upload-images.jianshu.io/upload_images/10024246-74bb624ee196b414.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 设置权限

1.  打开`Next`主题配置文件`_config.yml`, 将leancloud_visitors下的security设置为true（如没有则新增）

```
leancloud_visitors:
 enable: true
 app_id: <<your app id>> # 同前文的id
 app_key: <<your app key>> # 同前文的key
 # Dependencies: https://github.com/theme-next/hexo-leancloud-counter-security
 security: true
 # 由于Leancloud免费版的云引擎存在请求线程数和运行时间限制以及休眠机制，
 # 很多时候访客数量加载会很慢。
 # 如果设置betterPerformance为true，
 # 则网页则会在提交请求之前直接显示访客人数为查询到的人数+1，以增加用户体验。
 betterPerformance: false
```

2.  打开站点配置文件`_config.yml`
```
leancloud_counter_security:
 enable_sync: true
 app_id: <<your app id>>   # 同前文的id
 app_key: <<your app key>> # 同前文的key
 username: 
 password:
```

3.  打开命令行`cmd`并进入站点文件夹，键入以下命令以安装`hexo-leancloud-counter-security`插件

复制 

```
npm install hexo-leancloud-counter-security --save
```

4.  接着键入命令
```
hexo lc-counter register <<username>> <<password>>
```

**PS:** 将`<<username>>`和`<<password>>`替换为你自己的用户名和密码（不必与leancloud的账号）相同。**此用户名和密码将在hexo部署时使用。**

5.  打开站点配置文件`_config.yml`, 将`<<username>>`和`<<password>>`替换为你刚刚设置的用户名和密码

```
leancloud_counter_security:
 enable_sync: true
 app_id: <<your app id>>   # 同前文的id
 app_key: <<your app key>> # 同前文的key
 username: <<username>>    #如留空则将在部署时询问
 password: <<password>>    #建议留空以保证安全性，如留空则将在部署时询问
```

6.  返回Leancloud控制台的应用内, 检查_User表中是否出现一条记录（图示以用户名为admin为例）
    ![](https://upload-images.jianshu.io/upload_images/10024246-999c5edc89f7b563.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

7.  进入**Counter**表, 打开**权限设置**
    ![](https://upload-images.jianshu.io/upload_images/10024246-787b5c61ae91e7f2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

    ![](https://upload-images.jianshu.io/upload_images/10024246-37f02ef456350fe9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

    ![](https://upload-images.jianshu.io/upload_images/10024246-65c41acaf06d5c02.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

    ![](https://upload-images.jianshu.io/upload_images/10024246-7f8593a5f8c45141.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**至此配置完成**

