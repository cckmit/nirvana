---
title: Nginx 编译报错：SSL modules require the OpenSSL library
date: 2021-11-27 16:28:03
categories: linux
---
ginx 编译报错：SSL modules require the OpenSSL library

```
./configure: error: SSL modules require the OpenSSL library.
You can either do not enable the modules, or install the OpenSSL library
into the system, or build the OpenSSL library statically from the source
with nginx by using --with-openssl=<path> option.
```
 在编译 Nginx 时遇到这样的错误，明显是缺少 openssl 环境，需要手动安装

CentOS
```
$ yum -y install openssl openssl-devel
```
Ubuntu
```
$ apt -y install openssl openssl-devel
```
