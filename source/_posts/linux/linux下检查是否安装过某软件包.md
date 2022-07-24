---
title: linux下检查是否安装过某软件包
date: 2021-03-09 21:12:40
categories: linux
---
1、rpm包安装的，可以用 rpm -qa 看到，如果要查找某软件包是否安装，用** rpm -qa | grep "软件或者包的名字"**

2、以deb包安装的，可以用 dpkg -l 看到。如果是查找指定软件包，用 **dpkg -l | grep "软件或者包的名字"**

3、yum方法安装的，可以用 yum list installed 查找，如果是查找指定包，用 **yum list installed | grep "软件名或者包名"**

**举例：查看是否安装了gcc**

```
yum list installed | grep "gcc"
```
安装gcc
```
yum -y install gcc
```
