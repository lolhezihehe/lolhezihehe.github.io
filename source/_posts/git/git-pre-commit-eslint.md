---
title: git代码提交检查
top: false
cover: false
date: 2020-10-22 17:02:50
categories: git
tags:
- git
- eslint
---

### 简介
团队成员的代码风格迥异，在vue推荐的基础上增加了一些eslint规则，团队成员提交都需要经过eslint检测通过才能commit

### git hooks
git hooks就是在你`git add``git commit`等操作的时候触发的自定义脚本，具体可以看[git-hooks](https://git-scm.com/book/zh/v2/%E8%87%AA%E5%AE%9A%E4%B9%89-Git-Git-%E9%92%A9%E5%AD%90)


### husky
一般我们需要修改`.git/hooks下的文件，通过husky可以简单的实现git hooks

```bash
# 安装
npm install husky --save-dev
```

```json
# package.json
{
  "husky": {
    "hooks": {
      "pre-commit": "eslint --ext .js,.vue src"
    }
  }
}
```

### lint-staged
husky会对整个项目代码进行检查，但是我们需要的只是对每个成员提交的代码进行检查，这里就需要用到lint-staged，我们只对git add的文件进行检查

```bash
# 安装
npm install lint-staged --save-dev
```

```json
# package.json
{
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged"
    }
  },
  "lint-staged": {
    "src/**/*.{js,vue}": [
      "eslint --ext .js,.vue src",
      "git add"
    ]
  }
}
```

### vue-cli
vue-cli3官方提供了[git-hook](https://cli.vuejs.org/zh/guide/cli-service.html#git-hook)方案
`@vue/cli-service`集成了`yorkie`(fork自husky)，我们只需要安装lint-staged就可以

```bash
# 安装
npm install lint-staged --save-dev
```

```json
# package.json
{
  "gitHooks": {
    "pre-commit": "lint-staged"
  },
   "lint-staged": {
    "*.{js,vue}": [
      "vue-cli-service lint",
      "git add"
    ]
  }
}
```

### 效果
通过git commit命令提交代码会先通过eslint检测，不通过的检测提交失败，命令终端会显示eslint报错的文件；
通过vscode可视化提交检测失败会弹出git提示
{% asset_img git-pre-commit-hook.png This is an pre-commit image %}


### prettier
> 使用prettier配合eslint，prettier只做代码格式化，代码风格检查交给eslint；

看了很多文章发现都会加类似的一句话，但是都会推荐安装`eslint-plugin-prettier`和`eslint-config-prettier`，然后添加如下配置
```json
/* .eslintrc */
{
  "extends": [
    "some-other-config-you-use",
    "prettier"
  ],
  "plugins": ["prettier"],
  "rules": {
    "prettier/prettier": "error"
  }
}
```
其实这个配置遵循了`prettier`的eslint规则，而不是`"some-other-config-you-use"`，*相当于你和你老婆意见一致时听你的，意见不一致时听你老婆的*，并没有很好的遵循`prettier`只做代码格式化。

### vscode
我们团队使用vscode只配置eslint fix
个人推荐先手动根据`prettier` format，再通过`eslint` fix

- 安装`prettier`的vscode扩展
- 添加需要`prettier`格式化的配置
```json
/* settings.json */
{
  "[javascript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[typescript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[json]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[scss]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[vue]": {
    "editor.defaultFormatter": "octref.vetur"
  },
  "vetur.format.defaultFormatter.js": "prettier",
  "vetur.format.defaultFormatter.ts": "prettier",
  "vetur.format.defaultFormatter.html": "prettier",
}
```
- 添加fix的配置
```json
/* settings.json */
{
  "[javascript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[typescript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[json]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[scss]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[vue]": {
    "editor.defaultFormatter": "octref.vetur"
  },
  "vetur.format.defaultFormatter.js": "prettier",
  "vetur.format.defaultFormatter.ts": "prettier",
  "vetur.format.defaultFormatter.html": "prettier",
  
  /* 保存自动修复 */
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  }
}
```
团队风格统一可以把工作区的`settings.json`传到git；
