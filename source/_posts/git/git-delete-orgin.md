---
title: Git 清除远端已删除的分支
date: 2021-03-09 21:05:00
categories: git
---
使用 git branch -a 命令可以查看所有本地分支和远程分支（git branch -r 可以只查看远程分支） 
发现很多在远程仓库已经删除的分支在本地依然可以看到。
```
# git branch -a
  2-android-tableview-resize
  20180131hotfix
  master
  release0308_v52
  release0322
* release20180412_from0326
  remotes/origin/2-android-tableview-resize
  remotes/origin/20180131hotfix
  remotes/origin/HEAD -> origin/master
  remotes/origin/dev
  remotes/origin/dev_cache_renxj
```

使用命令 git remote show origin，可以查看remote地址，远程分支，还有本地分支与之相对应关系等信息。
```
# git remote show origin
* remote origin
  Fetch URL: git@*********
  Push  URL: git@**********
  HEAD branch: master
  Remote branches:
    2-android-tableview-resize                        tracked
    20180131hotfix                                    tracked
    master                                            tracked
    mergeFrom0808AndDev                               tracked
    refs/remotes/origin/3IN1                          stale (use 'git remote prune' to remove)
    refs/remotes/origin/3IN1_v052                     stale (use 'git remote prune' to remove)
    refs/remotes/origin/dev20171214                   stale (use 'git remote prune' to remove)
......
    release1228                                       tracked
    release20180130                                   tracked
  Local branches configured for 'git pull':
    2-android-tableview-resize                  merges with remote 2-android-tableview-resize
    20180131hotfix                              merges with remote 20180131hotfix
    3IN1                                        merges with remote 3IN1
    dev                                         merges with remote dev
    dev20171214                                 merges with remote dev20171214
......
  Local refs configured for 'git push':
    2-android-tableview-resize                  pushes to 2-android-tableview-resize                  (up to date)
    20180131hotfix                              pushes to 20180131hotfix                              (up to date)
........
```
此时我们可以看到那些远程仓库已经不存在的分支，根据提示，使用 git remote prune origin 命令：

```
git remote prune origin
Pruning origin
URL: git@**********
 * [pruned] origin/3IN1
 * [pruned] origin/3IN1_v052
 * [pruned] origin/dev20171214
........
```
这样就删除了那些远程仓库不存在的分支。
