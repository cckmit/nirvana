---
title: linux 查看磁盘空间占用情况
date: 2021-03-09 21:12:40
categories: linux
---
服务器运行一点时间后各种的项目文件，日志文件，数据库备份登，会越来越多，在linux下不像是win下一样直接右键属性可以查看，那么怎么看呢？  用到的命令 du 和 df 命令。

先上代码：
1.     df -h 命令查看磁盘空间
![](https://upload-images.jianshu.io/upload_images/10024246-7ce57bd365e173f7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


2.   du -ah --max-depth=1  /    查看根目录下各个文件占用情况
![](https://upload-images.jianshu.io/upload_images/10024246-c3a3064e29607710.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

max-depth表示目录的深度。

2. 查看某个目录   du -bsh 命令看一下常用的usr目录大小

    du -bsh 可以看到uer 目录占用了2.8G
![](https://upload-images.jianshu.io/upload_images/10024246-165d469d572f959c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3.  进入usr目录用find 命令找到大于100M文件   find . -size +100M 
    可以看到 小写的m不识别

![](https://upload-images.jianshu.io/upload_images/10024246-704e938a03244f0d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

二、du常用的选项：
　　-h：以人类可读的方式显示
　　-a：显示目录占用的磁盘空间大小，还要显示其下目录和文件占用磁盘空间的大小
　　-s：显示目录占用的磁盘空间大小，不要显示其下子目录和文件占用的磁盘空间大小
　　-c：显示几个目录或文件占用的磁盘空间大小，还要统计它们的总和
　　--apparent-size：显示目录或文件自身的大小
　　-l ：统计硬链接占用磁盘空间的大小
　　-L：统计符号链接所指向的文件占用的磁盘空间大小


du -sh : 查看当前目录总共占的容量。而不单独列出各子项占用的容量 

du -lh --max-depth=1 : 查看当前目录下一级子文件和子目录占用的磁盘容量。
