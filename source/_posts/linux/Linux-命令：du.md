---
title: Linux 命令：du
date: 2021-09-09 21:12:40
categories: linux
---
## 命令简介
du 命令用于显示每个文件和目录使用磁盘空间大小，du 会显示指定的目录或文件所占用的磁盘空间大小。还可以利用 du 命令，可以快速的查找 Linux 下的大目录。

## 命令语法
```
du [OPTION]... [FILE]...
```
## 选项说明
```
-a  #显示目录中所有文件的大小
-b  #显示目录或文件大小时，以byte为单位。
-c  #除了显示个别目录或文件的大小外，同时也显示所有目录或文件的总和
-D  #显示指定符号连接的源文件大小
-h  #以K，M，G为单位，提高可读性
-H  #与-h参数相同，但以1000为换算单位
-k  #以1024 bytes为单位
-l  #重复计算硬件连接的文件
-L<符号连接>  #显示选项中所指定符号连接的源文件大小
-m  #以1MB为单位
-s  #只显示总和
-S  #显示指定目录的大小，但是不显示其子目录
--exclude=<目录或文件> #忽略指定的目录或文件
--max-depth=<目录层数> #超过指定层数的目录后，忽略
--help  #打印帮助信息
--version  #打印版本信息
```
## 应用举例
- 显示目录或者文件所占用的磁盘空间
```
[root:test]# du
0 ./dir
28 .
[root:test]# du -h *.gz
4.0K cest.txt.gz
4.0K cuttest.txt.gz
4.0K mingongge1.txt.gz
4.0K mingongge2.txt.gz
4.0K mingongge.txt.md5.gz
4.0K sort.cut.txt.gz
[root:test]# du -a
4 ./cest.txt.gz
4 ./cuttest.txt.gz
4 ./mingongge1.txt.gz
4 ./mingongge2.txt.gz
4 ./mingongge.txt.md5.gz
4 ./sort.cut.txt.gz
0 ./dir
0 ./file
4 ./mingongge.zip
28 .
```
- 显示指定文件所占的磁盘空间
```
[root:test]# du test.txt
4 test.txt
[root:test]# du -h test.txt
4.0K test.txt
```
- 查看大目录
```
[root:test]# du -h --max-depth=1
0 ./.pki
38M ./goinception
46M ./httpd-2.4.46
28K ./testdir
114M .
[root:test]# du -h --max-depth=1 |sort -nr
114M .
46M ./httpd-2.4.46
38M ./goinception
28K ./testdir
0 ./.pki
```
