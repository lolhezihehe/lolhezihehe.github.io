---
title: hello-hexo
date: 2020-04-30 14:13:58
categories: hexo
tags: hexo
---
#### 安装 Hexo
使用 npm 安装 Hexo。
```bash
$ npm install -g hexo-cli
```
安装 Hexo 完成后，请执行下列命令，Hexo 将会在指定文件夹中新建所需要的文件。
```bash
$ hexo init <folder>
$ cd <folder>
$ npm install
```
新建完成后，指定文件夹的目录如下：
#### 首页
默认的主题提供了一个首页（Homepage）的布局 (用于 这个网站的主页)。想要使用它，需要在你的docs根级 README.md 的 YAML front matter 指定 home: true。以下是一个如何使用的例子：
```bash
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```

#### _config.yml
在 _config.yml 中修改大部分的配置。

参数 | 描述
-----|---
title |	网站标题|
subtitle |	网站副标题|
description |	网站描述|
keywords |	网站的关键词。使用半角逗号 , 分隔多个关键词。|
author |	您的名字|
language |	网站使用的语言 `zh-CN`|
timezone|	网站时区。Hexo 默认使用您电脑的时区。|

#### 主题配置
从github克隆
```bash
git clone https://github.com/theme-next/hexo-theme-next themes/next
```
- 修改_config.yml中的`theme:next`
- 把next/_config.yml中的`scheme:Muse`换成`scheme:Muse`（替换注释行即可）启用mist主题


#### 运行
```bash
hexo server
```

#### 一键部署github pages
创建一个lolhezihehe.github.io项目并关联
安装 hexo-deployer-git
```bash
$ npm install hexo-deployer-git --save
```
```yml
deploy:
  type: git
  repository: https://github.com/lolhezihehe/lolhezihehe.github.io
  # User pages must be built from the master branchs
  # github pages只能是master分支，建议新增一个写作分支区别与部署分支
  branch: master
  message: Site updated: {{ now('YYYY-MM-DD HH:mm:ss') }})
```
修改pagckage.json
```json
"deploy": "hexo deploy -g"
```
部署
```bash
$ npm run deploy
```
