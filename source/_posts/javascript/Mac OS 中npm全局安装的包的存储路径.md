---
title: Mac OS 中npm全局安装的包的存储路径
date: 2022-02-11 16:28:03
categories: 前端
---
Mac OS 中npm全局安装的包的存储路径是 `/usr/local/lib/node_modules/`
后来发现也可能是在这里 `/Users/[username]/.nvm/versions/node/[vx.x.x]/lib/node_modules`(方括号里是变量，根据自己的情况修改: `username`: 系统用户名；`vx.x.x`: node版本号)

## 示例

比如我全局安装了一个`vue-cli`的包，这个包提供一个`vue`命令，那么就这样查看`vue`命令所在的位置：
```
where vue
# /usr/local/bin/vue
```
现在知道了`vue`所在的位置是`/usr/local/bin/vue`,进入到该路径父目录：
```
cd /usr/local/bin
# 查看当前目录下vue文件信息
ll | grep vue
# lrwxr-xr-x  1 zhgs  admin    35B  9 21 09:26 vue -> ../lib/node_modules/vue-cli/bin/vue
```
可以看到vue的所在位置，这里看到了熟悉的`node_modules`，很明显，`npm`全局安装的包都存在了目录`/usr/local/lib/node_modules/`下
