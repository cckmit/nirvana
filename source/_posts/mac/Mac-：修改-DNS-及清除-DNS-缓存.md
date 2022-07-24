---
title: Mac-：修改-DNS-及清除-DNS-缓存
date: 2021-03-10 21:12:40
categories: mac
---
*   1.  在苹果菜单选择 “系统偏好设置”。

        ![](https://upload-images.jianshu.io/upload_images/10024246-134910c4b8f7de46.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   1.  单击 “网络” 图标。

![](https://upload-images.jianshu.io/upload_images/10024246-edd411652e2d5024.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


*   1.  从列表中选择想要修改或当前正在使用的网络连接服务（例如 WI-FI 或以太网），然后点击 “高级”。

![](https://upload-images.jianshu.io/upload_images/10024246-33b002edbd8a3a66.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


*   1.  选择 DNS 标签，单击 “DNS 服务器” 左下角的 “+”，添加 119.29.29.29 和 114.114.114.114，然后单击右下角的 “好”。
![](https://upload-images.jianshu.io/upload_images/10024246-f1ecd48ae2e2e8c1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 还原 OS X 中的 DNS 缓存设置

```
了解如何还原（清空）DNS 缓存设置。

```

> 关于 DNS 缓存

OS X 会将已解析的 DNS 查询的本地缓存保留一段时间，保留时间由 DNS 服务器定义。

有时可能需要立即还原缓存设置并重新查询 DNS 服务器。

例如，如果您是网络或服务器管理员且您的 DNS 服务器上的条目最近有更改，您可能需要执行此操作。

如果您的 Mac 使用的不是您服务器上的最新 DNS 条目，您可重新启动 Mac 以更新其缓存的信息。

如果您需要更新使用 OS X 的服务器上的 DNS 条目但又不能重新启动服务器，请使用以下适用于您所使用的 OS X 版本的命令。

> ［应用程序］-［实用工具］-［终端］

## OS X Yosemite 及更新机型

> 在 OS X v10.10.4 或更高版本中，请使用以下 “终端” 命令来还原 DNS 缓存设置：

```
sudo killall -HUP mDNSResponder

```

> 在 OS X v10.10 至 v10.10.3 中，请使用以下 “终端” 命令来还原 DNS 缓存设置：

```
sudo discoveryutil mdnsflushcache

```

## OS X Mavericks、Mountain Lion 和 Lion

在 OS X v10.9.5 及更低版本中，请使用以下 “终端” 命令来还原 DNS 缓存设置：

```
sudo killall -HUP mDNSResponder

```

> Mac OS X Snow Leopard

在 OS X v10.6 至 v10.6.8 中，请使用以下 “终端” 命令来还原 DNS 缓存设置：

```
sudo dscacheutil -flushcache

```

*   DNS 清除缓存方法来自：[苹果官网](https://support.apple.com/zh-cn/HT202516)
*   DNS 修改图片来自：[DNSPOD](https://support.dnspod.cn/Kb/showarticle/tsid/240/#link1)
