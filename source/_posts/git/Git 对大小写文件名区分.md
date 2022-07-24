---
title: Git 对大小写文件名区分
date: 2021-10-09 21:19:18
categories: git
---
git 默认不区分文件名大小写

当你创建一个文件后,叫 readme.md 写入内容后 提交到线上代码仓库.

然后你在本地修改文件名为 Readme.md 接着你去提交,发现代码没有变化.

控制台输入git status 也不显示任何信息
```
git status

nothing to commit, working tree clean
```
那么就配置git 使其对文件名大小写敏感

`git config core.ignorecase false`

或者使用`git mv`对文件重命名，这个时候git是能检测到文件变化的

`git mv readme.md Readme.md`
