---
title: nginx:[emerg] the "ssl" parameter requires ngx_http_ssl_module
date: 2021-11-26 16:28:03
category: linux
---
Nginx加ssl_certificate配置后报这个错。
```
$ sudo nginx -s reload
nginx: [emerg] the "ssl" parameter requires ngx_http_ssl_module in /usr/local/nginx/conf/nginx.conf:38
```
证明nginx在编译安装时候没有连同http_ssl_module模块一同编译；现在的情况是nginx已经安装过了，需要重新编译，编译安装的时候带上--with-http_ssl_module配置。

1.修改前
```
$ sudo nginx -V
nginx version: nginx/1.16.1
built by gcc 4.8.5 20150623 (Red Hat 4.8.5-36) (GCC)
configure arguments:
```
2.切换到源码包
3.进行编译：
```
sudo ./configure --with-http_stub_status_module --with-http_ssl_module
```
4.配置完成后，运行命令：
```
sudo make
```

5.make命令执行后，不要进行make install，否则会覆盖安装。

6.备份原有已安装好的nginx：
```
sudo cp /usr/local/nginx/sbin/nginx /usr/local/nginx/sbin/nginx.bak
sudo ls -l /usr/local/nginx/sbin/
```

7.停止nginx状态：
```
ps -aux|grep nginx
sudo kill -9 <PID>
```
8.将编译好的nginx覆盖掉原有的nginx：
```
sudo cp ./objs/nginx /usr/local/nginx/sbin/
```
9.提示是否覆盖，输入yes即可。

10.然后启动nginx：
```
/usr/local/nginx/sbin/nginx
```
11.有以下提示，证明已经编译成功：
```
$ sudo nginx -V
nginx version: nginx/1.16.1
built by gcc 4.8.5 20150623 (Red Hat 4.8.5-36) (GCC)
built with OpenSSL 1.0.2k-fips  26 Jan 2017
TLS SNI support enabled
configure arguments: --with-http_stub_status_module --with-http_ssl_module
```