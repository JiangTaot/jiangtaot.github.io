---
wiki: hexo-stellar
menu_id: post
title: 基于GitHub Page的个人博客搭建
date: 2024-05-09 20:19:34
author: JT
tags: [hexo,博客]
categories: [博客搭建]
cover: /images/jt/blog/leimu.png
banner: /images/jt/blog/leimu.png
poster: # 海报（可选，全图封面卡片）
  headline: 基于GitHub Page的个人博客搭建 # 必选
  caption: 使用 hexo + GitHub Page + stellar 主题 实现 # 可选
---
# 基于GitHub Page的个人博客搭建

## 1、GitHub Page

![image-20240509201934170](https://s2.loli.net/2024/05/09/cgtPzKwWRvopkUy.png)

> - 仓库命名以 【账号】.github.io  访问地址就是 【账号】.github.io
>
> - 静态页面构建及发布方式：1、分支  2、GitHub Action

### GitHub Action 方式发布

.github/workflows/hexo.yml

~~~yaml
name: Hexo

on:
  push:
    branches:
      - master  # default branch

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          # If your repository depends on submodule, please see: https://github.com/actions/checkout
          submodules: recursive
      - name: Use Node.js 18.19
        uses: actions/setup-node@v4
        with:
          # Examples: 20, 18.19, >=16.20.2, lts/Iron, lts/Hydrogen, *, latest, current, node
          # Ref: https://github.com/actions/setup-node#supported-version-syntax
          node-version: '18.19'
      - name: Cache NPM dependencies
        uses: actions/cache@v4
        with:
          path: node_modules
          key: ${{ runner.OS }}-npm-cache
          restore-keys: |
            ${{ runner.OS }}-npm-cache
      - name: Install Dependencies
        run: npm install

      - name: Build
        run: ./node_modules/.bin/hexo clean && ./node_modules/.bin/hexo generate
      - name: Upload Pages artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public
  deploy:
    needs: build
    permissions:
      pages: write
      id-token: write
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
~~~

## 2、Hexo

### 以 Hexo + github page 方式创建个人博客

项目结构

![image-20240509203951173](https://s2.loli.net/2024/05/09/cbW5n3er4f8Kxhv.png)

1、hexo init 创建 hexo 初始化文件

2、将 hexo 初始化文件拷贝到 jiangtaot.github.io 仓库下

3、.gitignore文件

~~~txt
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
_multiconfig.yml
/.idea/
~~~

## 3、使用模板

### Stellar

> https://xaoxuu.com/wiki/stellar/ 博主主页

#### 自定义模式

1、将 Stellar 主题仓库 fork 到自己的 GitHub 账号下

2、把您 fork 的 Stellar 主题仓库添加为博客仓库的子模块

~~~bash
git submodule add https://github.com/#yourname#/hexo-theme-stellar.git themes/stellar
~~~

3、在 `blog/_config.yml` 文件中找到并修改：

~~~yaml
theme: stellar
~~~

