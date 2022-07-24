---
title: Mac 删除node
date: 2022-02-09 16:28:03
categories: 前端
---
## Uninstall

### 官方安装包

- 1.  删除 `/usr/local/lib` 下的任意 `node` 和 `node_modules` 的文件或目录
- 2.  删除 `/usr/local/include` 下的任意 `node` 和 `node_modules` 的文件或目录
- 3.  删除 `Home` 目录下的任意 `node` 和 `node_modules` 的文件或目录
- 4.  删除 `/usr/local/bin` 下的任意 `node` 的可执行文件

可用以下命令代替以上操作：
```
# 这里是卸载npm的
sudo npm uninstall npm -g

# 这里是用来删除node创建的各种文件夹
sudo rm -rf /usr/local/lib/node
sudo rm -rf /usr/local/lib/node_modules
sudo rm -rf /var/db/receipts/org.nodejs.*
sudo rm -rf /usr/local/include/node /Users/$USER/.npm*

# 删除node命令
sudo rm /usr/local/bin/node

# 删除node的所有man手册
sudo rm /usr/local/share/man/man1/node.1
sudo rm /usr/local/share/man/man1/npm-*
sudo rm /usr/local/share/man/man1/npm.1
sudo rm /usr/local/share/man/man1/npx.1
sudo rm /usr/local/share/man/man5/npm*
sudo rm /usr/local/share/man/man5/package.json.5
sudo rm /usr/local/share/man/man7/npm*

# 这个命令也是删除一个node文件，但不知道这文件有什么用
sudo rm /usr/local/lib/dtrace/node.d
```
node创建的各种文件夹确实是太多了，而且分布也十分乱，如果担心自己电脑中没有删除干净的话，可以运行下面两个命令在找一下
```
# 在/usr/local文件夹下查找以npm开头的文件
find /usr/local -name 'npm*'
# 在/usr/local文件夹下查找以node开头的文件
find /usr/local -name 'node*'
```
**PS: 删除的时候小心一些，不要把其他应用中以npm或node开头的文件删掉**
删除结束后重启一下电脑，Mac会把与npm相关的命令重新设置一下
执行命令
```
npm -v  
// 结果应该是 -bash: npm: command not found
node -v
// 结果应该是 -bash: node: command not found
```