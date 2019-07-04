---
title: Git 子仓库管理 
date: 2018-05-17 10:30:50
categories: Git
tag: [git subtree, git submodule]
---

在使用NexT作为Hexo博客的主题时，不能 **友好** 的支持其主题的更新，以及 **多设备** 之间的主题同步。  
按照官方提供的导入主题操作指引   
```bash
$ cd hexo
$ git clone https://github.com/theme-next/hexo-theme-next themes/next
```
发现commit并push到GitHub的远程服务器上，发现`themes/next`路径下并不能打开和查看该路径下的文件，原因是NexT是当前项目的一个子仓库(项目)，在Github上对于之仓库项目的引用，推荐使用`git subtree`命令来进行对子仓库的管理，不推荐直接拷贝需要子仓库的代码到自己的项目中

原因是我是使用Travis CI来部署自己的项目，具体的[构建脚本和介绍](https://incoder.org/2018/05/02/hexo-iterative)请看，下面分别使用 `git submodule`、`git subtree`的方式进行NexT主题的管理

## git submodule 与 git subtree
* `git submodule`、`git subtree`都可以实现一个仓库作为其他仓库的子仓库的管理
* `git submodule`：是Git官方以前的推荐方案
* `git subtree`：Git [1.5.2](https://lwn.net/Articles/235109) 开始，Git 新增并推荐使用这个功能来管理子项目
* `git subtree`与`git submodule`不同，它不增加任何像`.gitmodule`这样的新的元数据文件
* `git subtree`对于项目中的其他成员透明，意味着可以不知道`git subtree`的存在

## git submodule 常用操作
[Git Submodule](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A8%A1%E5%9D%97)功能官方操作指引

### add 一个submodule

1. Fork Repository
  [hexo-theme-next](https://github.com/theme-next/hexo-theme-next)项目右上角`Fork`按钮即可
2. Clone Repository
    ```bash
    git clone git@github.com:RootCluster/hexo-theme-test.git
    ```
3. Add Submodule
    ```bash
    # 进入项目
    cd hexo-theme-test 
    # 注册next项目是一个submodule，并把数据拷贝到`themes/next`路径
    git submodule add git@github.com:RootCluster/hexo-theme-next.git themes/next
    ```
4. status
    ```bash
    # 当前submodule已被注册并指向了某个commit
    git submodule status
    1f5643061ec5257269673bd6159403c24015c53d themes/next (v6.3.0)
    # 查看在父仓库中有哪些变化被注册
    git status
    On branch submodule
    Changes to be committed:
    (use "git reset HEAD <file>..." to unstage)
          new file:   .gitmodules
          new file:   themes/next
    ```

    >有2个文件被修改过：`.gitmodules`,`themes/next`，当在父仓库时，Git不会跟踪submodule中的文件，Git只把它当成一个单一的文件

    * `.gitmodules`:存有submodule的信息
    * `themes/next`:submodule它自己

5. commint
    ```bash
    # 推送到远程submodule分支
    git commit -am "add next submodule"
    [submodule a5a612b] add next submodule
    2 files changed, 4 insertions(+)
    create mode 100644 .gitmodules
    create mode 160000 themes/next
    ```
6. push
    ```bash
    git push origin submodule
    Counting objects: 4, done.
    Delta compression using up to 4 threads.
    Compressing objects: 100% (4/4), done.
    Writing objects: 100% (4/4), 451 bytes | 451.00 KiB/s, done.
    Total 4 (delta 1), reused 0 (delta 0)
    remote: Resolving deltas: 100% (1/1), completed with 1 local object.
    To github.com:RootCluster/hexo-themes-test.git
      71879a8..a5a612b  submodule -> submodule
    ```
查看Github上的仓库，发现父仓库里有一个指向submodule的链接，表示你已经成功添加了一个submodule

### clone 带 submodule的项目
新路径下，clone项目，submodule分支
```bash
# clone项目
git clone -b submodule git@github.com:RootCluster/hexo-themes-test.git
# 进入项目路径
cd hexo-themes-test/
# 项目注册submodule
git submodule init
# clone submodule代码
git submodule update

```

### update 带 submodule的项目
只要在submodule路径下，所有的常规Git操作，如`push`,`pull`,`reset`,`status`等，都可以正常工作，如果要保证submodule和远程仓库保存同步，在submodule路径下运行`git pull`

* 如果你得到一个错误信息, 说你不在任何分支之上, 只要运行`git checkout master`就可修复
* 如果你在`pull`后 `submodule` 有一些更新, 父仓库会告诉你有一些变动需要 `commit` 了. `submodule`自身指向一个指定的 `commit`, 并且如果这个 `commit` 改变了, 父仓库会得知这个改变. 如果你的 `submodule` 需要在一个指定 `commit` 上工作, 可用`git reset`来设置

例如：我需要把NexT的版本改变到上一个Tag 6.2.0 (目前是6.3.0)
> git reset --hard (commit hash)

```bash
# 进入项目路径
cd hexo-themes-test/
# 重新指向submodule关联的commit记录
git reset --hard 206d463
# 回到父目录
cd ..
# commit本次的修改
git commit -am "set next version to 6.2.0"
```

{% note info %} 推送到远程仓库后，`submodule` 会和指定的`commit` 关联起来。如果你和别人一起工作在同一个项目，别人也可以在`submodule`下`pull`并且`commit`，因此改变了`submodule`的`commit`指向，这个问题，可以通过`git reset` 来解决{% endnote %}

### remove 项目中的 submodule
* 项目的根目录下(不是 submodule 的目录)，编辑 .gitmodules 文件，删除submodule配置
  ```bash
  [submodule "themes/next"]
      path = themes/next
      url = https://github.com/RootCluster/hexo-theme-next.git
  ```
* 项目根目录下，编辑`.git`文件夹下`config`文件，删除submodule配置
  ```bash
  [submodule "themes/next"]
      url = https://github.com/RootCluster/hexo-theme-next.git
  ```
* 清除submodule缓存
  ```bash
  git rm --cached themes/next
  ```

## git subtree 常用操作(重点)

### add一个subtree
* 在父仓库中新增子仓库
  ```bash
  # 添加子仓库
  git subtree add --prefix=themes/next https://github.com/RootCluster/hexo-theme-next.git master --squash
  git fetch https://github.com/RootCluster/hexo-theme-next.git master
  warning: no common commits
  remote: Counting objects: 3407, done.
  remote: Total 3407 (delta 0), reused 0 (delta 0), pack-reused 3406
  Receiving objects: 100% (3407/3407), 1.21 MiB | 36.00 KiB/s, done.
  Resolving deltas: 100% (2192/2192), done.
  From https://github.com/RootCluster/hexo-theme-next
  * branch            master     -> FETCH_HEAD
  Added dir 'themes/next'
  ```
  >`--squash`参数表示不拉取历史信息，而只生成一条commit信息

* 查看项目状态
  ```bash
  # 查看项目状态
  git status
  On branch subtree
  Your branch is ahead of 'origin/subtree' by 2 commits.
    (use "git push" to publish your local commits)

  nothing to commit, working tree clean
  ```

* 推送更改到远程仓库
  ```bash
  git push origin subtree
  Counting objects: 381, done.
  Delta compression using up to 4 threads.
  Compressing objects: 100% (334/334), done.
  Writing objects: 100% (381/381), 650.26 KiB | 34.22 MiB/s, done.
  Total 381 (delta 23), reused 225 (delta 19)
  remote: Resolving deltas: 100% (23/23), completed with 1 local object.
  To https://github.com/RootCluster/hexo-themes-test.git
    8ed2e2e..405af42  subtree -> subtree
  ```

### pull 子仓库更新
```bash
# 更新子仓库
git subtree pull --prefix=themes/next https://github.com/RootCluster/hexo-theme-next.git master --squash
From https://github.com/RootCluster/hexo-theme-next
 * branch            master     -> FETCH_HEAD
Subtree is already at commit 1f5643061ec5257269673bd6159403c24015c53d.
```

### push 子仓库修改
在引用子仓库的项目中修改了子仓库的相关代码，推送修改到源仓库
* commit 修改记录
* push 到源仓库
  ```bash
  # 推送子仓库修改到源仓库master分支
  git subtree push --prefix=themes/next https://github.com/RootCluster/hexo-theme-next.git master
  ```

### subtree 常用命令
```bash
git subtree add   --prefix=<prefix> <commit>
git subtree add   --prefix=<prefix> <repository> <ref>
git subtree pull  --prefix=<prefix> <repository> <ref>
git subtree push  --prefix=<prefix> <repository> <ref>
git subtree merge --prefix=<prefix> <commit>
git subtree split --prefix=<prefix> [OPTIONS] [<commit>]
```
## 附录
* [如何使用 Git Submodule](http://linlexus.com/git-submodule-usage)
* [git subtree教程](https://www.jianshu.com/p/d42d330bfead)