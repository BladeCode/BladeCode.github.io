---
title: Git 常用命令 
date: 2018-10-07 12:43:50
categories: Git
tag: [git bash]
---

记录 Git 日常操作常用命令

## git config
Git级别：system() < global(当前用户) < local(当前仓库)
* 新增或修改
```sh
git config --global user.name xxxxx
git config --global user.email xxx@xxxx.com
```
* 删除用品配置信息
```sh
# 如果当前只有一个用户，就不用加入xxxx
git config --global --unset user.name xxxx
```

## git clone
* clone 
```sh
git clone url
```
* clone 指定分支
```sh
git clone -b branch_name url
```
* clone 指定tag
```sh
# clone 
git clone url
# checkout tag
git checkout tag_name
```
* clone 指定commit
```sh
# 查看git commit 历史的
git log
# 指定 commit SHA
git clone commit_sha_value 
```

## git commit
```sh
git commit -m "注释"
```

## git branch
* 创建分支
```sh
# 创建分支
git branch branch_name
# 创建并切换到新分支
git checkout -b branch_name
```
* 切换分支
```sh
git checkout branch_name
```
* 删除分支
```sh
# 删除本地分支
git branch -d branch_name
# 删除远程指定分支
git push origin --delete branch_name
```
* 重命名分支
```sh
git branch -m old_branch_name new_branch_name
```
* 查看分支
```sh
# 查看本地所有分支
git branch
# 查看远程所有分支
git branch -r
# 查看本地和远程所有分支
git branch -a
```

## git tag
* 新增tag
```sh
git tag -a tag_name -m "注释"
```
* 查看tag
```sh
git tag -l
```
* 删除tag
```sh
# 删除本地tag
git tag -d tag_name
# 删除远程指定tag
git push origin --delete tag tag_name
```
## git help
更多命令
```sh
git --help
```
## 附录
* [Git Docs](https://git-scm.com/docs)