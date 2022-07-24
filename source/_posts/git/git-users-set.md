---
title: Git多账号配置
date: 2021-03-09 21:22:47
categories: git
---
### 一、如何生成ssh密钥

#### 1.1 设置Git的user.name和user.email

为了生成多账户，这里展示的局部的git配置，并不是全局的账户配置(全局配置：在config后面加上–list)。

```
//github账号
$ git config user.name "zhangsan"
$ git config user.email "zhangsan@gmail.com"

//gitlab账号
$ git config user.name "zhangsan"
$ git config user.email "zhangsan@qq.com"
```

#### 1.2 生成SSH密钥

```
//github账号
$ ssh-keygen -t rsa -C “zhangsan@gmail.com”

//gitlab账号
$ ssh-keygen -t rsa -C “zhangsan@qq.com”
```

文件命名后，按2次回车，密码为空。

```
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /Users/zhangsan/.ssh/github_id_rsa.
Your public key has been saved in /Users/zhangsan/.ssh/github_id_rsa.pub.
The key fingerprint is:
SHA256:mRAluu98izlMRIQhaezphUvRHnkEH5HFm+aAX6wtXBU zhangsan@gmail.com
The key's randomart image is:
+---[RSA 2048]----+
| ..ooBB*o E.     |
|  =.=o++. .      |
| o =.=o. +       |
|  = +.o.Bo       |
| o o.+ OS        |
|  o  .* o        |
|     o..         |
|     ooo.        |
|      =o..       |
+----[SHA256]-----+

```

将上面ssh密钥生成步骤重复一次，最后得到四个文件：github_id_rsa、github_id_rsa.pub和gitlab_id_rsa、gitlab_id_rsa.pub

### 二、多账号配置

在~/.ssh目录下，增加config配置文件(注：无后缀名)，配置规范如下(可配置多个git账号):

```
  #Host host（Host简称，使用命令ssh host可连接远程服务器，如：ssh github）
    #User/Email 登录用户名(如：zhangsan/zhangsan@gmail.com)
    #HostName 主机名用ip或域名，建议使用域名(如:github.com)
    #Port 服务器open-ssh端口（默认：22,默认时一般不写此行
    #IdentityFile 证书文件路径（如~/.ssh/id_rsa_*)
```

接下来，举个具体的栗子：

```
#github zhangsan@gmail.com
host github
    Hostname github.com
    User zhangsan
    IdentityFile ~/.ssh/github_id_rsa

#gitlab zhangsan@qq.com
host gitlab
    Hostname gitlab.*.com
    User zhangsan
    IdentityFile ~/.ssh/gitlab_id_rsa
```

注意事项：
- 在配置文件中的，IdentityFile文件位置是rsa私钥，不是.pub公钥
- push代码的时候注意下，得check下本的user.name和user.email,若没有进行生成操作。建议最好设置一个全局的user.name和user.email,然后需要特定的配置的git仓库，就单独配置(当前配置查询命令:$ git config –list)

### 三、ssh-add增加新的私钥

```
//github账号
$ ssh-add ~/.ssh/github_id_rsa

//gitlab账号
$ ssh-add ~/.ssh/gitlab_id_rsa
```

> 该命令如果报错：Could not open a connection to your authentication agent.无法连接到ssh agent，可执行ssh-agent bash命令后再执行ssh-add命令。

上面的配置完成完成，使用命令ssh-add -l可以看到所有的密钥列表,ssh-add的作用主要将密钥添加到 ssh-agent 的高速缓存中，这样在当前会话中就不需要再次输入密码了
具体的可以参考[SSH Keys](https://wiki.archlinux.org/index.php/SSH_keys_%28%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87%29)

