---
title: nginx自动添加内网端口
date: 2021-03-09 21:12:40
categories: linux
---
## 现象：

1.内网nginx服务器提供web的端口为8080；
2.通过路由的转换，将8080端口映射为外网的80端口（`www.test.com`）；
3.访问二级目录`www.test.com/test`,此时nginx会出现访问出错，并自动更改端口为内网的8080，即`www.test.com:8080/test/`；
4.排除了nginx自动添加斜杠的问题，因为nginx从0.8.48这个版本后就已经设置为自动添加了，即
```
server_name_in_redirect off;//默认是off了
```
## 原因：
通过端口映射时，nginx会自动添加内网端口，修改路径导致路径访问出错；
## 解决：
在server里面加入
```
port_in_redirect off;//关闭自动添加端口
```
