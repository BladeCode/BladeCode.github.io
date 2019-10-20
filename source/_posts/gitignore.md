---
title: .gitignore 基础知识
date: 2018-04-13 00:30:50
categories: Git
tag: ignore
---

.gitignore顾名思义是Git中用来管理所需要忽略或者说不用纳入版本控制文件  

## 基本配置语法
1. “#“：表示注释
2. “/“：表示目录
3. “*“：表示通配符，用来通配多个字符
4. “?“：表示通配单个字符
5. “[]“：表示包含单个字符的匹配列表
6. “!“：表示不忽略匹配到的文件或者目录

>注意：Git对.gitignore配置文件是从上往下进行规则匹配，这也意味如果：前（limit）>后（limit），则后面的规则不会被执行

## 全局与局部
.gitignore分为： **全局** ignore，**局部** ignore

### 全局ignore设置
* 在用户账户文件夹（C:\Users\<'YourName'>）路径下新建一个命名为`.gitignore_global`的文件
* 使用Git Bash(需要切换路径到C:\Users\<'YourName'>)或者Git CMD命令行工具输入：
    ``` bash
    git config --global core.excludesfile ~/.gitignore_global
    ```
* 此时全局ignore已经设置完成，你只需要修改`.gitignore_global`文件内需要忽略的文件类型就可以全局控制忽略不需要纳入版本控制的文件或文件夹
* 不难发现，其实是往 `.gitconfig`中加入如下内容来指名Git忽略不纳入版本控制的文件，当然如果你不想用命令行完成全局设置，你也可以直接在`.gitconfig`文件中加入`[core] excludesfile= ~/.gitignore_global`内容即可

### 局部ignore设置
* 只需要在Git控制版本控制项目的根目录中加入.gitignore文件，在.gitignore文件中写明忽略不纳入版本控制的文件即可

## 参考示例

>你可以查看参考[Github](https://github.com/github/gitignore)官方所写好的示例

## 插件.ignore
支持Android Studio，JetBrains系列     
安装方法  

* `Settings` > `Plugs` > `Browse repositories` > `.ignore` > `Install plugin`
* 里面有已经写好的模板，只需适当修改