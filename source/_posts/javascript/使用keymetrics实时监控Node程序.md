---
title: 使用keymetrics实时监控Node.js程序
date: 2021-04-21 14:34:28
categories: 前端
---
通过pm2能守护node.js程序永远在线，在实际应用中是非常有必要的。另外，pm2配合keymetrics能实时监控node.js程序的运行，达到监控node.js程序的目的。

> 安装pm2

pm2可以使我们的node.js或io.js程序永远在线。这是[pm2](https://www.npmjs.com/package/pm2)的官方介绍：

`PM2 is a production process manager for Node.js applications with a built-in load balancer. It allows you to keep applications alive forever, to reload them without downtime and to facilitate common system admin tasks.`


安装pm2


```
npm install -g pm2
```
启动一个node.js程序

```
//进入到app的目录去启动`

pm2 start index.js --name 'ghost'

 ```

其他常用命令：

```
//查看pm2守护的app
pm2 list
//或者
pm2 status
//重启，restart后面跟--name后面指定的名字
pm2 restart ghost
//查看进程的使用资源情况
pm2 monit
//查看log
pm2 logs ghost
//查看app的更多详细信息，后面跟id
pm2 describe 1
//升级pm2，升级完毕后自动加载之前运行中的所有app
npm install pm2@latest -g ; pm2 updatePM2
```

使用Keymetrics可以配合pm2来监控node.js程序（也支持io.js程序的监控）。

> 安装Keymetrics
首先需要注册Keymetrics：
https://app.keymetrics.io/#/register

登录后，通过new bucket新建，然后进入控制面板，可以看到分配的public key 和secret key。然后，在安装有pm2的服务器端输入以下命令
```
pm2 interact your-secret-key your-public-key
```

监控成功后，会有类似下面的提示：
```
[Keymetrics.io] [Agent created] Agent ACTIVE - Web Access: [https://app.keymetrics.io/](https://app.keymetrics.io/)
 ```
此时，pm2会把收集到的统计信息实时地推送到Keymetrics，我们可以在Keymetrics的后台中实时地查看到node.js程序的运行信息，其中还有一些快捷操作，如重启node.js程序等。

![](https://upload-images.jianshu.io/upload_images/10024246-cb89932826f59578.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
