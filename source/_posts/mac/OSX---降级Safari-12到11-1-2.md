---
title: OSX---降级Safari-12到11-1-2
date: 2021-03-10 21:12:40
categories: mac
---
### 操作

#### 工具

```
Pacifist : https://www.charlessoft.com/cgi-bin/pacifist_download.cgi?type=zip
```

#### Safari 11

```
https://www.newasp.net/soft/109641.html
```

#### 步骤

```
	1 . 重启系统, 按住⌘-R不松手
	2 . 在实用工具(Utilities)下打开终端,输入csrutil disable, 然后回车; 你就看到提示系统完整性保护(SIP: System Integrity Protection)已禁用
	3 . 输入reboot回车重启电脑
	4 . 打开Pacifist, 点击 打开Safari 11.1.软件包, 选择Safari 11.1.2包的内容,点击安装按钮;
	5 . 给予管理员权限, 并一路替换,就可以了
	6 . 如果你想启用SIP, 那么重启启动系统,按住⌘-R不松手; 输入csrutil enable回车.开启SIP
```

#### 使用pacifist安装safari

![](http://upload-images.jianshu.io/upload_images/10024246-137076307d06f5c0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 后记

```
1 . 一旦升级到 mojave 版本,是不能降级到 safari11.1.2版本的.
2 . 如果这么操作怎么都不成功, 完全卸载 safari 再直接安装吧.
```
