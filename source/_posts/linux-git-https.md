---
title: linux git https
date: 2024-04-10 13:33:36
tags:
---

## linux Git使用https url

当在使用github的https链接在远程服务器上开发，push代码时会发现输入github账号密码的方式已经被禁用了，官方推荐使用access token的方式来登录Github。Access token本身很难记住，Github为此推荐使用Git-credential-manager （**GCM**）来对密码进行管理，这样在每次使用push时不需要输入access token，而是使用一些其他的密码来登录，本文记录一下配置和使用过程。

### 安装

前往GCM的下载界面，下载最新的GCM [release ](https://github.com/git-ecosystem/git-credential-manager/releases/latest)，运行以下命令安装

```
sudo dpkg -i <path-to-package>
git-credential-manager configure
```

接下来需要配置credential.credentialStore，这是存储密码的方式方式。具体的区别可以参考[文档](https://github.com/git-ecosystem/git-credential-manager/blob/main/docs/credstores.md) ，这里我们采用gpg的方式

```
git config --global credential.credentialStore gpg
```

首先需要使用gpg 密钥

```shell
gpg --gen-key
```

生成后会有一个`gpg-id` 接下来init pass

```
pass init <gpg-id>
```

可以使用`gpg --list-secret-keys` 来查看当前gpg管理的密钥，在sec中可以看到一个类似 `C7E1A00F9C8F05D0F371CA5C512C8D464D6C5393` 形式的id，此即为`gpg-id`

如果没有pass 可以 使用 `apt install pass` 等命令安装

下面在push时可能会出现需要

```
export GPG_TTY=$(tty)
```

可以写入在`~/.bashrc`中

接下来就可以开心的push代码啦！
