---
title: Mac-配置-adb
date: 2021-03-09 21:12:40
categories: android
---
第一步：打开终端，进入根目录。

```
$HOME (进入根目录)
```

第二步：创建 .bash_profile 文件（已存在则无需创建）。
```
touch .bash_profile （创建 .bash_profile 文件）
```
第三步：打开并编辑 .bash_profile 文件。
```
open -e .bash_profile （打开编辑 .bash_profile）
```
在该文件中配置 adb 的路径信息，比如我的（注意替换为你的路径）：
```
export ANDROID_HOME=/Users/mac/Library/Android/sdk
export PATH=$PATH:$ANDROID_HOME/tools
export PATH=$PATH:$ANDROID_HOME/platform-tools
```
第四步：使配置生效。
```
source .bash_profile
```
到这里， 如果输入 adb 后并没有提示：
```
-bash: adb: command not found
```
就表示已经配置成功了。

但是如果你此时退出终端，然后重新打开终端输入 adb，又提示你：
```
-bash: adb: command not found
```
那么你就需要做如下操作了，首先检查你的 shell：
```
echo $SHELL
```
如果是 base，就把：
```
~/.bash_profile
```
追加到 ~/.bashrc 末尾。

如果是 zsh，就把
```
 ~/.bash_profile
```
追加到 ~/.zshrc 末尾。

如果没有对于的 .bashrc 、.zshrc 文件，那么按照上面对应的命令新建一个即可。
这里以 shell 为 base 以及没有 .bashrc 文件为例，贴一下终端代码：
```
$HOME
touch .bashrc
open -e .bashrc
```
将 ~/.bash_profile 保存到刚打开的文件中，重新打开终端
