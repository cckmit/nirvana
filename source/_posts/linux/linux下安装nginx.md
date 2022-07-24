---
title: linux版本：CentOS7 64位
date: 2021-03-09 21:12:40
categories: linux
---

【yum 安装最新版nginx：[https://www.jianshu.com/p/a2de35ee5283](https://www.jianshu.com/p/a2de35ee5283)】

在安装nginx前首先要确认系统中安装了`**gcc、pcre-devel、zlib-devel、openssl-devel**`。

Linux下检查是否安装过某软件包：[https://www.jianshu.com/p/356c2d9deff1](https://www.jianshu.com/p/356c2d9deff1)

安装命令：

```
yum -y install gcc pcre-devel zlib-devel openssl openssl-devel
```

nginx下载地址：[https://nginx.org/download/](https://nginx.org/download/)

下载“nginx-1.18.0.tar.gz”，移动到/usr/local/下。

```
## 解压
tar -zxvf nginx-1.18.0.tar.gz

##进入nginx目录
cd nginx-1.18.0
## 配置
./configure --prefix=/usr/local/nginx
# make
make
make install
```

OK，现在可以执行make 了。 

   ![](https://upload-images.jianshu.io/upload_images/10024246-e43e02b3dca9408f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

执行make、make install命令

测试是否安装成功

```
# cd到刚才配置的安装目录
/usr/loca/nginx/
./sbin/nginx -t
```

`**错误信息**`：
```
nginx: [alert] could not open error log file: open() "/usr/local/nginx/logs/error.log" failed (2: No such file or directory)
2016/09/13 19:08:56 [emerg] 6996#0: open() "/usr/local/nginx/logs/access.log" failed (2: No such file or directory)
```
原因分析：nginx/目录下没有logs文件夹

`**解决方法**`：

```
mkdir logs
chmod 700 logs
```

正常情况的信息输出：

nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful

启动nginx 

```
cd /usr/local/nginx/sbin
./nginx //启动nginx
```

在浏览器中输入服务器的ip地址，如：http://localhost

##问题排查：
服务器的80端口是打不开的。

因为我使用的linux系统版本是CentOS7，所以可以在服务器中执行如下命令来验证

```
firewall-cmd --query-port=80/tcp
```
![](https://upload-images.jianshu.io/upload_images/10024246-fd24ff6c7af0da76.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


显然80端口没有开启。

下面我们开启80端口：

```
firewall-cmd --add-port=80/tcp --permanent
#重启防火墙
systemctl restart firewalld</pre>

```
 --permanent   #永久生效，没有此参数重启后失效
   ![](https://upload-images.jianshu.io/upload_images/10024246-ac48aec75fe6b5c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

刷新浏览器

![](https://upload-images.jianshu.io/upload_images/10024246-3209134f65b0a8f5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**====================== 分割线 ====================**

配置完毕！

2、配置nginx开机自启动

```
vim /etc/rc.d/rc.local</pre>
```
![image](https://upload-images.jianshu.io/upload_images/10024246-475fc49e832b0290.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
