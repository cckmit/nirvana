---
title: nginx:[warn] the "user" directive makes sense only if the master process runs with super-user
date: 2021-11-19 16:28:03
category: javascript
---

普通用户在restart和reload nginx时，会报错：

`nginx: [warn] the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /usr/local/nginx/conf/nginx.conf:2`

我又不能给开发人员root权限，没办法，只好这么做。

原因是:默认情况下Linux的1024以下端口是只有root用户才有权限占用

## 方法一：

所有用户都可以运行（因为是755权限，文件所有者：root，组所有者：root）
```
chown root.root nginx
chmod 755 nginx
chmod u+s nginx
```
## 方法二：

仅 root 用户和 wyq 用户可以运行（因为是750权限，文件所有者：root，组所有者：www）
```
chown root.www nginx
chmod 750 nginx
chmod u+s nginx
 ```

## 方法三：还有就是在sbin目录下使用管理员权限启动  sudo ./nginx
![](https://upload-images.jianshu.io/upload_images/10024246-704a97d27d1b2201.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
