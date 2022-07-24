---
title: mac 使用nvm安装node
date: 2021-03-11 21:12:40
categories: 前端
---

1.curl https://raw.github.com/creationix/nvm/master/install.sh | sh

2.vi ~/.bash_profile
　　添加：source /Users/dujie/.nvm/nvm.sh

nvm install 0.10.24
nvm use 0.10.24
# 默認使用 0.10.24 版本，否則每次關掉 Terminal 就得重新 nvm use 一次
$ nvm alias default 0.10.24

# 列出所有安裝的版本
$ nvm ls


# 列出總共有哪些版本可以安裝
$ nvm ls-remote


安装常用的工具
npm install -g express 
npm install -g bower
npm install -g fis
