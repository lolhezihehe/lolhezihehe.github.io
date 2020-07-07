---
title: git配置多个SSH-Key
top: false
cover: false
date: 2020-06-18 19:23:20
categories: git
tags:
- git 
- github
- gitlab
- gitee
- ssh-key
---

### 背景
公司总部统一了gitlab库，所以部门的仓库要从gitee转移到公司的gitlab仓库，加上个人开发总共有3个远程git库：
- gitee（部门内部开发旧库，迁移至公司gitlab）
- gitlab（公司内部开发新库）
- github（个人开发）

### 方案

1、生成对应平台的ssh-key
```bash
# gitee
$ ssh-keygen -t rsa -C 'xxxxx@qq.com' -f ~/.ssh/gitee_id_rsa

# gitlab
$ ssh-keygen -t rsa -C 'xxxxx@company.com' -f ~/.ssh/company_id_rsa

# github
$ ssh-keygen -t rsa -C 'xxxxx@163.com' -f ~/.ssh/github_id_rsa
```

2、mac下终端输入open ~/.ssh打开ssh目录，在目录下新建一个config文件，添加以下内容
```
# gitee
Host gitee.com
HostName gitee.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/gitee_id_rsa
# gitlab
Host git.company.com
HostName git.company.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/gitlab_id_rsa
# github
Host github.com
HostName github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/github_id_rsa
```

3、命令测试，成功会返回successfully
```bash
$ ssh -T git@gitee.com
$ ssh -T git@git.company.com
$ ssh -T git@github.com
```