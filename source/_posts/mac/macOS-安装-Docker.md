---
title: macOS-安装-Docker
date: 2021-03-10 21:12:40
categories: mac
---
# macOS

# 

系统要求[](https://yeasy.gitbook.io/docker_practice/install/mac#xi-tong-yao-qiu)

[Docker Desktop for Mac](https://docs.docker.com/docker-for-mac/) 要求系统最低为 macOS Mojave 10.14。

# 

安装[](https://yeasy.gitbook.io/docker_practice/install/mac#an-zhuang)

## 

使用 Homebrew 安装[](https://yeasy.gitbook.io/docker_practice/install/mac#shi-yong-homebrew-an-zhuang)

[Homebrew](https://brew.sh/) 的 [Cask](https://github.com/Homebrew/homebrew-cask) 已经支持 Docker Desktop for Mac，因此可以很方便的使用 Homebrew Cask 来进行安装：

```
$ brew cask install docker
```
## 手动下载安装

如果需要手动下载，请点击以下链接下载 [Stable](https://download.docker.com/mac/stable/Docker.dmg) 或 [Edge](https://download.docker.com/mac/edge/Docker.dmg) 版本的 Docker Desktop for Mac。

如同 macOS 其它软件一样，安装也非常简单，双击下载的 `.dmg` 文件，然后将那只叫 [Moby](https://www.docker.com/blog/call-me-moby-dock/) 的鲸鱼图标拖拽到 `Application` 文件夹即可（其间需要输入用户密码）。
![](https://upload-images.jianshu.io/upload_images/10024246-ed67d3f5d80f1b0a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


# 运行

从应用中找到 Docker 图标并点击运行。

![](https://upload-images.jianshu.io/upload_images/10024246-53121fff4bb2948c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

运行之后，会在右上角菜单栏看到多了一个鲸鱼图标，这个图标表明了 Docker 的运行状态。

![](https://upload-images.jianshu.io/upload_images/10024246-23ece114e822f25d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


每次点击鲸鱼图标会弹出操作菜单。

![](https://upload-images.jianshu.io/upload_images/10024246-b9ea98917ad76fe3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

之后，你可以在终端通过命令检查安装后的 Docker 版本。

```
$ docker --version

Docker version 19.03.8, build afacb8b

$ docker-compose --version

docker-compose version 1.25.5, build 8a1c60f6
```

如果 `docker version`、`docker info` 都正常的话，可以尝试运行一个 [Nginx 服务器](https://hub.docker.com/_/nginx/)：
```
$ docker run -d -p 80:80 --name webserver nginx
```

服务运行后，可以访问 [http://localhost](http://localhost/)，如果看到了 "Welcome to nginx!"，就说明 Docker Desktop for Mac 安装成功了。
![image.png](https://upload-images.jianshu.io/upload_images/10024246-0e37b4c4c52d946a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


要停止 Nginx 服务器并删除执行下面的命令：

```
$ docker stop webserver

$ docker rm webserver

```

# 镜像加速
如果在使用过程中发现拉取 Docker 镜像十分缓慢，可以配置 Docker [国内镜像加速](https://yeasy.gitbook.io/docker_practice/install/mirror)。

# 

参考链接[](https://yeasy.gitbook.io/docker_practice/install/mac#can-kao-lian-jie)

*   [官方文档](https://docs.docker.com/docker-for-mac/install/)
