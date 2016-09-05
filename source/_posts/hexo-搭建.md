---
title: hexo 搭建
date: 2016-09-05 20:41:34
tags:
---
* 安装node.js(https://nodejs.org/en/)

```
https://nodejs.org/dist/v4.5.0/node-v4.5.0-x64.msi
```
* github建立github pages仓库(https://pages.github.com/)

```
git@github.com:dzyong/dzyong.github.io.git
```
* 本地clone上面建立的仓库

```
git clone git@github.com:dzyong/dzyong.github.io.git
```
* 初始化hexo

```
hexo init dzyong.github.io
```
* 安装hexo的git插件

```
npm install hexo-deployer-git --save
```
* 配置_config.yml中的deploy

```
deploy:
  type: git
  repo: git@github.com:dzyong/dzyong.github.io.git
  branch: master
```
* 新建blog

```
hexo new <blog name>
```
* 生成静态文件

```
hexo generate(g)
```
* 查看预览

```
hexo server(S)
```
* 上传生成的静态文件

```
hexo deploy
```
* 新建github分支

```
git init
git remote add -f origin git@github.com:dzyong/dzyong.github.io.git
git checkout -b hexo
```
* 生成.gitignore

```
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
```
* 提交源码

```
git add .
git commit
git push origin hexo
```