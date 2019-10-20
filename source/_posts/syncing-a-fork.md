---
title: Git 同步 Fork 项目
date: 2018-08-01 16:09:50
categories: Git
tag: Syncing
---

[Github](https://www.github.com) 全球最大的同性交友网站，这里拥有最前沿的IT技术创新，拥有最流行的开源项目，等等...，总之这里是我的知识仓库，每天都会在上面寻找，学习知识

扯远了，本篇解决对于fork的项目，如何进行源项目的更新和同步问题

## 远程仓库
1. 查看fork项目的远程仓库信息
    ```bash
    git remote -v
    origin  https://github.com/YOUR_USERNAME/YOUR_FORK.git (fetch)
    origin  https://github.com/YOUR_USERNAME/YOUR_FORK.git (push)
    ```
2. 设置源项目仓库地址
    ```bash
    git remote add upstream https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git
    ```
3. 检查远程地址信息
    ```bash
    git remote -v
    origin    https://github.com/YOUR_USERNAME/YOUR_FORK.git (fetch)
    origin    https://github.com/YOUR_USERNAME/YOUR_FORK.git (push)
    upstream  https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git (fetch)
    upstream  https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git (push)
    ```

## 同步源仓库信息

1. 获取源仓库更新
    ```bash
    git fetch upstream
    remote: Counting objects: 75, done.
    remote: Compressing objects: 100% (53/53), done.
    remote: Total 62 (delta 27), reused 44 (delta 9)
    Unpacking objects: 100% (62/62), done.
    From https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY
    * [new branch]      master     -> upstream/master
    ```
2. 查看本地master分支
    ```bash
    git checkout master
    Switched to branch 'master'
    ```
3. 合并源仓库更新到本地master分支
    ```bash
    git merge upstream/master
    Updating a422352..5fdff0f
    Fast-forward
    README                    |    9 -------
    README.md                 |    7 ++++++
    2 files changed, 7 insertions(+), 9 deletions(-)
    delete mode 100644 README
    create mode 100644 README.md
    ```

## 同步源仓库branch
在git中master实质是一个特殊的branch，其它的branch的同步和master同步操作并不一样

```bash
# 查看项目的所有分支
git branch -v
# 当前项目在master分支，origin/HEAD类似指针，表示项目默认分支是origin/master
# origin/dev，origin/i18n，origin/ivan/feat-custom-lang，origin/master这四个分支是fork项目目前拥有分支
# upstream/dev，upstream/i18n，upstream/master表示源仓库项目所拥有的分支
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/dev
  remotes/origin/i18n
  remotes/origin/ivan/feat-custom-lang
  remotes/origin/master
  remotes/upstream/dev
  remotes/upstream/i18n
  remotes/upstream/master

# 切换到dev分支，同步源仓库dev分支到fork项目的dev分支
git checkout -b dev upstream/dev
# 推送修改到fork项目dev分支
git push origin dev
```

> 如果源仓库分支已被删除，那么可以在fork项目中删除源仓库已被删除的分支
```bash
# 删除指定分支，并推送到远程仓库
git push origin --delete branch_name
```

## 同步源仓库tag
```bash
# 获取源仓库的tag
git fetch upstream --tags
# 将新的的tag推送到fork项目
git push --tags
```

## 附录
* [同步你的 Fork 仓库](http://wiki.jikexueyuan.com/project/github-basics/fork-synced.html)
* [Configuring a remote for a fork](https://help.github.com/articles/configuring-a-remote-for-a-fork/)
* [Syncing a fork](https://help.github.com/articles/syncing-a-fork)