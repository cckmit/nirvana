---
title: linux下yum安装最新稳定版nginx
date: 2021-03-09 21:12:40
categories: linux
---
## 摘抄nginx官网文档

URL：[http://nginx.org/en/linux_packages.html#stable](http://nginx.org/en/linux_packages.html#stable "http://nginx.org/en/linux_packages.html#stable")

To set up the yum repository for RHEL/CentOS, create the file named `/etc/yum.repos.d/nginx.repo` with the following contents:

```
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/7/$basearch/
gpgcheck=0 
enabled=1
```

Replace “`OS`” with “`rhel`” or “`centos`”, depending on the distribution used, and “`OSRELEASE`” with “`6`” or “`7`”, for 6.x or 7.x versions, respectively.

执行如下命令进行yum安装nginx

```
yum install nginx
```

查看nginx版本
```
# 查看nginx版本
nginx -v

# 查看编译参数
nginx -V</pre>
```
 查看安装目录

```
rpm -ql nginx
```

查看配置文件

```
# 用于日志切割
 /etc/logrotate.d/nginx
```
