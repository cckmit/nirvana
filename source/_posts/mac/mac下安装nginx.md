---
title: mac下安装nginx
date: 2021-03-10 21:12:40
categories: mac
---
安装工具：

[homebrew](https://brew.sh/index_zh-cn.html)（还没用过的小伙伴可以点链接进行了解或者自行百度）

步骤：

　　1、打开终端，习惯性命令：

```
brew update
```
//结果：Already up-to-date.</pre>

　　2、终端继续执行命令：

```
brew search nginx   //查询要安装的软件是否存在
```

　　3、这里我们多执行一步“废”命令，不过有利于我们后面的配置：

```
brew info nginx
```

　　运行结果：

　　![](https://upload-images.jianshu.io/upload_images/10024246-4c9ce6a852151bf0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

　　我们可以看到，nginx在本地还未安装（Not installed），nginx的来源（From），Docroot默认为/usr/local/var/www，在/usr/local/etc/nginx/nginx.conf配置文件中默认端口被配置为8080从而使nginx运行时不需要加sudo，nginx将在/usr/local/etc/nginx/servers/目录中加载所有文件，以及我们可以通过最简单的命令 ‘nginx’ 来启动nginx。

　　4、正式开始安装：

```
brew install nginx
```

![](https://upload-images.jianshu.io/upload_images/10024246-de5daa8180b3f4c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

　　5、查看nginx安装目录（是否如info所说）：　

```
open /usr/local/etc/nginx/
```

　　　　![](https://upload-images.jianshu.io/upload_images/10024246-90142c4098d678f3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

　　成功打开nginx目录，也可以看到如info所说servers目录以及nginx.conf的配置文件（后面会用到这个配置文件）。但我们并没有找到nginx被安装到了哪里。

　　终端继续执行：

```
open /usr/local/Cellar/nginx  //其实这个才是nginx被安装到的目录
```

　　![](https://upload-images.jianshu.io/upload_images/10024246-4a716a1bef024b54.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

　　会看到一个以当前安装的nginx的版本号为名称的文件夹，这个就是我们安装的nginx根目录啦。进入1.12.2_1/bin 目录，会看到nginx的可执行启动文件。

　　同样的，我们在1.12.2_1/目录下还可以看到一个名字为html的快捷方式文件夹（暂且就这么叫吧），进入该目录我们会发现其实它指向的就是/usr/local/var/www目录，这个在上面我们查看的info信息中有提到（Dcroot）

 　　6、启动nginx，终端输入如下命令：

```
nginx
```

　　没有报错即为启动成功。
　　7、访问验证：

　　　　打开浏览器访问localhost:8080,这里跟网上的一些教程会有些不一样，正常情况下到这一步就会能看到nginx的欢迎界面啦，然而博主却遇到了坑爹的情况（如果你能正常看到nginx欢迎界面，可以直接跳过这一步了）

![](https://upload-images.jianshu.io/upload_images/10024246-c85425af5395d326.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 　　想必这个错误大家都很熟悉了，我就不再多做解释，接下来直接说原因（该原因仅为猜测，因为无从验证）：安装的nginx会默认在html（也就是/usr/local/var/www）目录下生成一个欢迎页面文件，而出现上面这种情况就是欢迎页面文件没有生成（至于为什么没有生成就不得而知了，一切都是猜测）。

　　那在解决这个问题之前，我们先来了解一下nginx的配置文件吧（nginx.conf）:

```
cat /usr/local/etc/nginx/nginx.conf
```

　　显示配置文件的代码为：

```
#user  nobody;
worker_processes 1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;

events {
    worker_connections 1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main '$remote_addr - $remote_user [$time_local] "$request" ' # '$status $body_bytes_sent "$http_referer" ' # '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout 0;
    keepalive_timeout 65;

    #gzip  on;

    server {
　　　　 #侦听8080端口
        listen 8080;
　　　　 #定义使用 localhost访问
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
　　　　　　　#定义服务器的默认网站根目录位置
            root   html;
　　　　　　  #定义首页索引文件的名称
            index  index.html index.htm;
        }
　　　　　...
　　　　　...
　　　　　... (注释代码太多，就不全部贴出来了)  include servers/*;
}
```
　　通过配置文件我们可以看到其默认的网站根目录为html（即/usr/local/var/www），而默认的索引文件为index.html 和 index.htm，这下就找到原因了，原来我们的根目录少了首页索引文件，那就来手动创建一个吧：
```
cd /usr/local/var/www/                      //进入到www目录下
touch index.html                            //创建一个新的index.html文件
vim index.html                              //编辑该文件 </pre>
````
　　将如下代码写入index.html文件中：
```

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>title</title>
</head>
<body>
    <div>
        <h1>我的nginx欢迎页面</h1>
    </div>
</body>
</html>
```


　　回到浏览器（localhost:8080）刷新：

![](https://upload-images.jianshu.io/upload_images/10024246-669dfb5bb1851e49.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

　　OK，大功告成啦！
