---
title: Linux 命令：exec
date: 2021-09-09 21:12:40
categories: linux
---
命令简介
exec 命令用于调用并执行指定的命令。

exec 命令通常用在 Shell 脚本程序中，可以调用其他的命令。如果在当前终端中使用命令，则当指定的命令执行完毕后会立即退出终端。

##### 语法格式
```
exec [-cl] [-a name] [command [arguments]]
```
#####  选项说明
-c  #在空环境中执行指定的命令
-l  #在传递给command的第零个arg的开头放置一个破折号
-a  #Shell将name作为第零个参数传递给command

##### 应用举例
输出指定信息
```
[ ~]# echo "mingongge"
mingongge
[ ~]# exec -c echo mingongge
mingongge
```
其它总结
```
exec ls      #在 shell 中执行 ls，ls 结束后不返回原来的 shell 中了
exec        #file 中的内容作为标准输入（替代 STDIN）
exec >file  #将标准输出写入file（替代STDOUT）
exec 3      #将 file 读入到文件描述符 3 中（此时，创建了文件描述符 3）
sort <&3    #将文件描述符3作为临时输入，用于 sort 排序
exec 4>file  #将写入文件描述符 4 中的内容写入 file 中（此时，创建了文件描述符 4）
ls >&4    #ls将不会有显示，直接写入文件描述符 4 中了，即上面的 file 中
exec 5<&4  #创建文件描述符 4 的拷贝文件描述符 5
exec 3<&-  #关闭文件描述符 3
```
