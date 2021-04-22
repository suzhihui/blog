---
title: git 高级
date: 2021-04-12 15:41:20
tags: git
category: 前端
---

### 安装
<!-- ![输入图片说明](/img/election/1.png) -->
```bash
brew install git
```
### 配置
```bash
git config --global user.name "laosu"
git config --global user.email "xxx.qq.com"
```
### 初始化
```bash
git init
```
### 新建文件
```bash
$ echo laosu is here > index.txt
```

### 查看
```bash
ls -a
```

### 命令
``` bash
git status

git log
git log --pretty=oneline
git log --pretty=oneline --author='laosu'

git diff: 默认比较的是工作区和暂存区
// 先从缓存区撤销，缓存区无内容，从历史区域撤销
git checkout "文件名"
// 有的时候我们希望提交时合并到上一次的提交
git commit --amend
// 若本地文件存在则不能删除，需要通过-f参数删除
git rm 文件名
// 删除缓存区
git rm --cached "文件名"

// 10. 恢复某个版本文件
git checkout commit_id filename "文件名"
```

### git 3个区
- 1. 工作区
- 2. 暂存区
- 3. 版本库

> [mermaid 手册](http://mermaid-js.github.io/mermaid)
---
```mermaid
graph LR
    工作区 --> |git add | 暂存区
    暂存区 --> |git add | 版本库
```

