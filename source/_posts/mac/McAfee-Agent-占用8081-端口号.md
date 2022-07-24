---
title: McAfee-Agent-占用8081-端口号
date: 2021-03-10 21:12:40
categories: mac
---
公司电脑被标装，McAfee Agent关不掉

```
sudo lsof -n -i4TCP:8081
sudo launchctl list | grep 5693
sudo launchctl remove com.mcafee.agent.macmn
```

如果需要暂时禁用mac上装的mcafee，可以在命令行里先su到之前有管理员权限的账户，然后跑命令：
```
sudo /usr/local/McAfee/AntiMalware/VSControl stopoas
```
