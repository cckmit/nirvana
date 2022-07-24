---
title: GIT使用之设置SSH KEY
date: 2021-04-21 14:48:46
categories: git
---
## 检查是否设置过SSH Key**

```
cd ~/.ssh 
```
## 本地生成key
```
ssh-****** -t rsa -C 'xxxxx.com'
```
- 执行命令后需要进行3次或4次确认：
- 确认秘钥的保存路径（如果不需要改路径则直接回车）；
- 如果上一步置顶的保存路径下已经有秘钥文件，则需要确认是否覆盖（如果之前的秘钥不再需要则直接回车覆盖，如需要则手动拷贝到其他目录后再覆盖）；
- 创建密码（如果不需要密码则直接回车）；
- 确认密码；

## 进入ssh目录下查看公钥**

```
cat id_rsa.pub
```

**4\. 将公钥添加至账号，github或者gitlab**
![](https://upload-images.jianshu.io/upload_images/10024246-3704a5eb0c6d6b6a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/10024246-dda69dc9797750b7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 配置用户名和邮箱**

```
git config --global user.name "xxx"  #commit时显示的用户名 
git config --global user.email "xxx@qq.com"
```

## 查看配置信息**

```
git config --list
```
