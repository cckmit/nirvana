---
title: Mac-OS-安装-Telnet
date: 2021-03-10 21:12:40
categories: mac
---
使用 homebrew 安装telnet：
```
brew install telnet
```
安装报错
```
$ brew install telnet
Error: /usr/local/Cellar is not writable. You should change the
ownership and permissions of /usr/local/Cellar back to your
user account:
  sudo chown -R $(whoami) /usr/local/Cellar
Warning: You are using macOS 10.15.
We do not provide support for this pre-release version.
You will encounter build failures with some formulae.
Please create pull requests instead of asking for help on Homebrew's GitHub,
Discourse, Twitter or IRC. You are responsible for resolving any issues you
experience, as you are running this pre-release version.

Error: The following directories are not writable by your user:
/usr/local/Cellar
/usr/local/Homebrew
/usr/local/etc
/usr/local/etc/bash_completion.d
/usr/local/opt
/usr/local/var/homebrew/linked
/usr/local/var/homebrew/locks

You should change the ownership of these directories to your user.
  sudo chown -R $(whoami) /usr/local/Cellar /usr/local/Homebrew /usr/local/etc /usr/local/etc/bash_completion.d /usr/local/opt /usr/local/var/homebrew/linked /usr/local/var/homebrew/locks
```
说明你当前用户权限不对，需要sudo权限执行命令，增加用户权限
```
sudo chown -R $(whoami) /usr/local/Cellar /usr/local/Homebrew /usr/local/etc 
```
重新安装
```
$ brew install telnet
Warning: You are using macOS 10.15.
We do not provide support for this pre-release version.
You will encounter build failures with some formulae.
Please create pull requests instead of asking for help on Homebrew's GitHub,
Discourse, Twitter or IRC. You are responsible for resolving any issues you
experience, as you are running this pre-release version.

==> Downloading https://opensource.apple.com/tarballs/remote_cmds/remote_cmds-60
######################################################################## 100.0%
==> Downloading https://opensource.apple.com/tarballs/libtelnet/libtelnet-13.tar
######################################################################## 100.0%
==> xcodebuild SYMROOT=build -arch x86_64
==> make -C telnet.tproj OBJROOT=build/Intermediates SYMROOT=build/Products DSTR
🍺  /usr/local/Cellar/telnet/60: 4 files, 142.3KB, built in 21 seconds
==> `brew cleanup` has not been run in 30 days, running now...
```
执行telnet ip port命令
```
$ telnet baidu.com 443
Trying 220.181.38.148...
Connected to baidu.com.
Escape character is '^]'.
^
HTTP/1.1 302 Moved Temporarily
Server: bfe/1.0.8.18
Date: Mon, 08 Feb 2021 02:31:59 GMT
Content-Type: text/html
Content-Length: 161
Connection: close
Location: http://www.baidu.com/error.html

<html>
<head><title>302 Found</title></head>
<body bgcolor="white">
<center><h1>302 Found</h1></center>
<hr><center>bfe/1.0.8.18</center>
</body>
</html>
Connection closed by foreign host.
```
