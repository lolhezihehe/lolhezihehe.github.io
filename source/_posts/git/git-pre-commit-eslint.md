---
title: 提升团队代码质量
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
使用`prettier`配合`eslint`

先保证安装了`prettier`和`eslint`
```bash
npm install --save-dev eslint prettier
```

再安装eslint-config-prettier和eslint-plugin-prettier
```bash
npm install --save-dev eslint-config-prettier eslint-plugin-prettier
```
`eslint-config-prettier`关闭了可能与Prettier冲突的ESLint规则，`eslint-plugin-prettier`则将Prettier规则集成到ESLint规则中。

最后配置一下.eslintrc文件
```json
/* .eslintrc.json or .eslintrc.js */
{
  "extends": ["prettier"],
  "plugins": ["prettier"],
  "rules": {
    "prettier/prettier": ["error"]
  },
}
```
---

### 引入typescript（2021-5-5更新）

在原有的vue项目引入typescript


#### vue-cli3

新项目直接使用vue create选择typescript就可以，原有的javascript版本可以直接参考cli3生成的文件目录，查看package.json需要哪些typescript包


#### 安装

根据ts版的package.json先安装 **typescript** 和 **@vue/cli-plugin-typescript**
```bash
npm install --save-dev typescript @vue/cli-plugin-typescript
```
如果需要eslint可以直接把typescipt相关的eslint也一起安装了
```bash
npm install --save-dev @typescript-eslint/eslint-plugin @typescript-eslint/parser @vue/eslint-config-typescript
```

#### 配置

根目录下新建一份tsconfig.json，复制ts版的tsconfig.json，详细配置可以参考[https://www.tslang.cn/docs/handbook/tsconfig-json.html](https://www.tslang.cn/docs/handbook/tsconfig-json.html)

在src目录下新建 **shims-vue.d.ts** 和 **shims-tsx.d.ts**
```ts
/* shims-vue.d.ts */
declare module '*.vue' {
  import Vue from 'vue'
  export default Vue
}
```

```ts
/* shims-tsx.d.ts */
declare global {
  /* 这里不能把import放在最外层，否则会报错 */
  import Vue, { VNode } from 'vue'
  namespace JSX {
    // tslint:disable no-empty-interface
    interface Element extends VNode {}
    // tslint:disable no-empty-interface
    interface ElementClass extends Vue {}
    interface IntrinsicElements {
      [elem: string]: any
    }
  }
}
```

修改.eslintrc.js配置文件
```js
module.exports = {
  root: true,
  env: {
    node: true
  },
  'extends': [
    'plugin:vue/essential',
    'eslint:recommended',
    '@vue/typescript/recommended'
  ],
  parserOptions: {
    ecmaVersion: 2020
  },
  rules: {
    'no-console': process.env.NODE_ENV === 'production' ? 'warn' : 'off',
    'no-debugger': process.env.NODE_ENV === 'production' ? 'warn' : 'off'
  }
}
```

最后修改 main.js为main.ts

#### 实践
目前在一个小版本项目引入了typescript，小版本是基于[vue-admin-template](https://github.com/PanJiaChen/vue-admin-template)开发，所以参考了typescript版[vue-typescript-admin-template](https://github.com/Armour/vue-typescript-admin-template/tree/minimal)

旧代码并没有全部改造为ts，未改造的js文件添加`@ts-nocheck`，如果只忽略其中的一个方法可以使用`@ts-ignore`，具体参考[ban-ts-comment](https://github.com/typescript-eslint/typescript-eslint/blob/master/packages/eslint-plugin/docs/rules/ban-ts-comment.md)
```json
/* .eslintrc.js */
rules: {
  "@ts-nocheck": "on",
  "@ts-ignore": "on"
}
```
```js
// @ts-nocheck
export function yourFn(){
  // ...
}
```
目前的计划还是先让团队熟悉ts的写法，后续会逐步把.js文件转换为ts文件，.vue文件依旧保持原有的写法，vue2.7会对ts类型进行改进，预计在2.7的时候开始对项目的vue文件做改造。



### 参考
- [eslint+husky+prettier+lint-staged提升前端应用质量](https://juejin.cn/post/6844903778227847181)
- [eslint-config-alloy](https://github.com/AlloyTeam/eslint-config-alloy/blob/master/README.zh-CN.md)
- [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)
- [由 shims-vue.d.ts 引发的思考](https://juejin.cn/post/6844903882309500942)
