---
title: mac终端切换用户
date: 2021-03-10 21:12:40
categories: mac
---
1、mac终端切换用户到root
sudo -i即可

2、mac终端切换成普通用户
su - test（test为用户名）即可

3、mac可以设置命令别名
如ll，正常情况下，mac终端不识别ll命令，但是alias 命令设置别名后，终端即可以设别ll命令：alias ll='ls -la'；但是没有修改系统文件，则该命令只能临时生效，下次再打开终端，还是不能识别ll命令，永久生效办法，请修改系统文件，方法有多种，请自行百度（如果不是ll命令，其他命令同理）
————————————————
版权声明：本文为CSDN博主「CoberOJ_」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/huodoubi/article/details/69948483
