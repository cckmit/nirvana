---
title: remote:Support for password authentication was removed on August 13, 2021
date: 2021-09-05 20:35:36
category: git
---
一、 问题描述
如果你在七夕（没错就是2021年8月14日）的这一天刚好加班，想把这个仓库送给（push）github，你就刚好会遇到这个问题：remote: Support for password authentication was removed on August 13, 2021. Please use a personal access token instead.

```
Username for 'https://github.com': XXXX
Password for 'https://XXXX@github.com': 
remote: Support for password authentication was removed on August 13, 2021. Please use a personal access token instead.
remote: Please see https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/ for more information.
```
二、 github为什么要把密码换成token
github官方解释

1、修改为token的动机

我们描述了我们的动机，因为我们宣布了对 API 身份验证的类似更改，如下所示：

近年来，GitHub 客户受益于 GitHub.com 的许多安全增强功能，例如双因素身份验证、登录警报、经过验证的设备、防止使用泄露密码和 WebAuthn 支持。 这些功能使攻击者更难获取在多个网站上重复使用的密码并使用它来尝试访问您的 GitHub 帐户。 尽管有这些改进，但由于历史原因，未启用双因素身份验证的客户仍能够仅使用其GitHub 用户名和密码继续对 Git 和 API 操作进行身份验证。

从 2021 年 8 月 13 日开始，我们将在对 Git 操作进行身份验证时不再接受帐户密码，并将要求使用基于令牌（token）的身份验证，例如个人访问令牌（针对开发人员）或 OAuth 或 GitHub 应用程序安装令牌（针对集成商） GitHub.com 上所有经过身份验证的 Git 操作。 您也可以继续在您喜欢的地方使用 SSH 密钥（如果你要使用ssh密钥可以参考）。

2、修改为token的好处

令牌（token）与基于密码的身份验证相比，令牌提供了许多安全优势：

唯一： 令牌特定于 GitHub，可以按使用或按设备生成

可撤销：可以随时单独撤销令牌，而无需更新未受影响的凭据

有限 ： 令牌可以缩小范围以仅允许用例所需的访问

随机：令牌不需要记住或定期输入的更简单密码可能会受到的字典类型或蛮力尝试的影响

三、 如何生成自己的token
1、在个人设置页面，找到Setting（参考）


2、选择开发者设置Developer setting



3、选择个人访问令牌Personal access tokens，然后选中生成令牌Generate new token



4、设置token的有效期，访问权限等

选择要授予此令牌token的范围或权限。

要使用token从命令行访问仓库，请选择repo。
要使用token从命令行删除仓库，请选择delete_repo
其他根据需要进行勾选


5、生成令牌Generate token



如下是生成的token

注意：

记得把你的token保存下来，因为你再次刷新网页的时候，你已经没有办法看到它了，所以我还没有彻底搞清楚这个token的使用，后续还会继续探索！


6、之后用自己生成的token登录，把上面生成的token粘贴到输入密码的位置，然后成功push代码！



也可以 把token直接添加远程仓库链接中，这样就可以避免同一个仓库每次提交代码都要输入token了：

git remote set-url origin https://<your_token>@github.com/<USERNAME>/<REPO>.git

<your_token>：换成你自己得到的token
<USERNAME>：是你自己github的用户名
<REPO>：是你的仓库名称
例如：
```
git remote set-url origin https://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@github.com/shliang0603/Yolov4_DeepSocial.git/
```