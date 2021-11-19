---
title: React Native ios 填坑之 Error:`fsevents` unavailable (this watcher can only be used on Darwin)
date: 2021-11-13 21:12:40
category: reactnative
---

react-native run-ios 运行报错 Error: `fsevents` unavailable (this watcher can only be used on Darwin)

网上一查两个命令解决, ` brew install watchman` 安装失败一直卡在哪里
```
npm r -g watchman
brew install watchman
```
没办法重新安装一下 brew 

重装后node用不了,在终端输入node -v   提示 `env: node: No such file or directory`
再次寻求解决办法,按照以下命令执行一遍,虽然我第一个命令就报错了,但是好像问题不大,已解决
```
sudo brew uninstall node
brew update
brew upgrade
brew cleanup
brew install node
sudo chown -R $(whoami) /usr/local
brew link --overwrite node
brew postinstall node
```
最后回到第一步 控制台执行以下两个命令
```
npm r -g watchman 

brew install watchman 
```