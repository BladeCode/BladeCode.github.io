---
title: Git 常用命令 
date: 2018-10-07 12:43:50
categories: Git
tag: [git bash]
---

记录 Git 日常操作常用命令

## git config
Git级别：system(系统所有用户) < global(当前用户) < local(当前仓库)
* 查看配置信息
    ```
    # 查看对应 Git 级别（--local；--global；--system）的配置信息
    git config --list --local
    ```
* 新增或修改
    ```sh
    git config --global user.name xxxxx
    git config --global user.email xxx@xxxx.com
    ```
* 删除用户配置信息
    ```sh
    # 如果当前只有一个用户，就不用加入xxxx
    git config --global --unset user.name xxxx
    ```

## git init
1. 把已有项目代码纳入 Git 管理
    ```sh
    # 进入项目根路径
    cd project_dir
    # 进行项目 Git 初始化
    git init
    ```
2. 新建项目直接使用 Git 管理
    ```sh
    # 在当前路径下创建项目并使用 Git 初始化项目
    git init project_name
    # 进入项目根路径
    cd project_name
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

## git mv
* 重命名文件
    ```sh
    git mv old_file_name new_file_name
    ```

## git log
* 查看仓库 commit 历史日志
    ```sh
    # 下面参数可任意组合
    git log --oneline(简洁查看) --all(所有分支) -n4(最近 4 次记录) --graph(图形化展示)
    ```

## git help
更多命令
    ```sh
    git --help
    ```

## git other
* 查看当前项目远程仓库地址
    ```sh
    git remote -v
    ```

## 附录
* [Git Docs](https://git-scm.com/docs)
