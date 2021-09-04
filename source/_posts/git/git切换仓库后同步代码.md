---
title: git切换仓库后同步代码
date: 2021-09-09 21:12:40
category: git
---
git仓库需要迁移到新的服务器，现在需要把所有的代码同步到新的仓库。

1、获取源仓库所有分支 
```
git clone <源仓库>
cd <项目名称>
git branch -r | awk -F'origin/' '!/HEAD|master/{print $2 " " $1"origin/"$2}' | xargs -L 1 git branch -f --track 
git fetch --all --prune --tags
git pull --all
```
2、同步到自己的仓库 
```
git remote set-url origin <新仓库地址>
git pull
git push --all
git push --tags
```
