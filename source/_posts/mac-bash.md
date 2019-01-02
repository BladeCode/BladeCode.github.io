---
title: 应该知道的系统环境配置文件
date: 2018-11-24 15:27:20
categories: Operating Systems
tag: [OS, Shell]
---

在计算机操作系统中`Shell`是用户与操作系统交互的媒介，而`bash`作为目前`Linux\macOS`系统中最常用的`Shell`，它支持的`startup`文件也并不单一，甚至让人感到费解，以下就是对Shell的学习

Shell：在计算科学中，指“为用户提供用户界面”的软件，通常指的是 **命令行界面** 的解析器。一般来说，`Shell`指操作系统中提供访问内核所提供的服务程序。

通常将`Shell`分为两类
* 命令行：提供一个命令行界面（CLI）
* 图形界面：提供一个图形用户界面（GUI）

![Linux_system](https://upload.wikimedia.org/wikipedia/commons/thumb/b/be/The_general_structure_of_a_Linux_system.jpeg/250px-The_general_structure_of_a_Linux_system.jpeg)

在PC桌面领域，不同的操作系统都有自己的`Shell`，截止2018.10主流的操作系统市场占有率，Windows（78.04%），OS X（13.73%），Unknown（5.44%），Linux（1.64%），Chrome OS（1.15%），数据来源于[statcounter](http://gs.statcounter.com/os-market-share/desktop/worldwide/#monthly-201710-201810)；

这些操作系统中都有自己独特的`Shell`命令，在不同的系统版本中，命令工具也是不完全相同，例如：
* Windows：Windows CE、Windows NT常用[`cmd.exe`](https://en.wikipedia.org/wiki/Cmd.exe)；Windows 10中常用 [`PowerShell`](https://zh.wikipedia.org/wiki/Windows_PowerShell)
* OS X：默认[`bash`](https://zh.wikipedia.org/wiki/Bash)，除此之外还提供了[`tcsh`](https://zh.wikipedia.org/wiki/Tcsh)、[`zsh`](https://zh.wikipedia.org/wiki/Z_shell)和[`ksh`](https://zh.wikipedia.org/wiki/Korn_shell)
* Linux：`/etc/shells`路径下，`/bin/sh`，`/bin/bash`，`/bin/csh`等应用
> 更详细的请查阅[维基百科](https://zh.wikipedia.org/wiki/%E6%AE%BC%E5%B1%A4#%E5%91%BD%E4%BB%A4%E8%A1%8C%E7%95%8C%E9%9D%A2%EF%BC%88CLI%EF%BC%89%E6%AE%BC%E5%B1%A4)

## Configuration Files
|    文件    | sh | ksh | csh | tcsh | bash | zsh |
| --------- | --- | --- | --- | --- | --- | --- |
| /etc/.login               |  --- | --- | login | login | --- | --- |
| /etc/csh.cshrc            |  --- | --- | yes | yes | --- | --- |
| /etc/csh.login            |  --- | --- | login | login | --- | --- |
| ~/.tcshrc                 |  --- | --- | --- | yes | --- | --- |
| ~/.cshrc                  |  --- | --- | yes | yes | --- | --- |
| ~/etc/ksh.kshrc           |  --- | int. | --- | --- | --- | --- |
| /etc/sh.shrc              |  int. | --- | --- | --- | --- | --- |
| $ENV (typically ~/.kshrc) |  int. | int. | --- | --- | int. | --- |
| ~/.login                  |  --- | --- | login | login | --- | --- |
| ~/.logout                 |  --- | --- | login | login | --- | --- |
| /etc/profile              |  login | login | --- | --- | login | login |
| ~/.profile                |  login | login | --- | --- | login | login |
| ~/.bash_profile           |  --- | --- | --- | --- | login | --- |
| ~/.bash_login             |  --- | --- | --- | --- | login | --- |
| ~/.bash_logout            |  --- | --- | --- | --- | login | --- |
| ~/.bashrc                 |  --- | --- | --- | --- | int.+n/login | --- |
| /etc/zshenv               |  --- | --- | --- | --- | --- | yes |
| /etc/zprofile             |  --- | --- | --- | --- | --- | login |
| /etc/zshrc                |  --- | --- | --- | --- | --- | int. |
| /etc/zlogin               |  --- | --- | --- | --- | --- | login |
| /etc/zlogout              |  --- | --- | --- | --- | --- | login |
| ~/.zshenv                 |  --- | --- | --- | --- | --- | yes |
| ~/.zprofile               |  --- | --- | --- | --- | --- | login |
| ~/.zshrc                  |  --- | --- | --- | --- | --- | int. |
| ~/.zlogin                 |  --- | --- | --- | --- | --- | login |

* yes：表示shell在启动时始终读取文件
* login：表示如果shell是登录shell，则读取文件
* n/login：表示如果shell不是登录shell，则读取文件
* int.：表示如果shell是交互式的，则读取文件

>更详细的介绍请查阅[维基百科](https://en.wikipedia.org/wiki/Unix_shell#Configuration_files)

关于常用`Shell`，执行流程如下图：

![flow](https://res.cloudinary.com/incoder/image/upload/v1543141362/blog/flow.png)

## startup文件
`bash`作为目前`Linux`，`macOS（默认bash命令）`系统中最常用的`shell`，通过上面的表格，我们可以知道macOS系统中，`bash`主要由以下文件
* /etc/profile：The systemwide initialization file, executed for login shells
* ~/.profile：
* ~/.bash_profile：The personal initialization file, executed for login shells
* ~/.bash_login：
* ~/.bash_logout：The individual login shell cleanup file, executed when a login shell exits
* ~/.bashrc：The individual per-interactive-shell startup file

我们看看在macOS系统中，bash的startup文件是如何进行加载

>注意：
>* `/etc/profile`和`/etc/paths`是系统级别，系统启动后就会加载，后面的配置文件是当前用户级的环境变量
>* 如果`~/.bash_profile`存在，后面几个文件就会忽略不读，不在时，才会以此类推读取后面的文件
>* `~/.bashrc`没有上述规则，他始终加载，它是在`bash shell`打开的时候载入的

## 特点
`bash`的两种属性，即 **“交互”** 与 **“登录”**，按照`bash`是否与用户进行交互，可将其分为 **“交互式”** 与 **“非交互式”**；按照`bash`是否被用户登录，又可将其分为 **“登录shell”** 与 **“非登录shell”**

### 交互式与非交互式
* 交互式：shell的一种运行模式，交互式shell等待用户输入命令，并且立即执行，然后将结果反馈给用户。整个流程：登录——>执行命令——>退出。当你退出后，这个shell就终止
* 非交互式：shell的另一种运行模式，它专门用来执行预先设定的命令。这种模式下，shell不予用户进行交互，而是读取存储在脚本文件中的命令并执行它们。当它读取到文件结尾，这个shell就终止

### 登录shell与非登录shell
* 登录shell：
    * 用户通过输入用户名/密码（或者证书认证）后启动的shell；
    * 通过带有`-l|--login`参数的`bash`命令启动的shell
例如：系统启动，远程启动，使用`su -`切换用户，通过`bash --login`命令启动的bash等
* 非登录shell：以上情况除外基本就是 **“非登录shell”**
例如：从图形化界面启动终端，使用`su -`切换用户，通过`bash`命令启动bash等

### 主要区别
* 使用`logout`退出`登录shell`，使用`exit`退出`非登录shell`。
* 其实`exit`命令会判断当前shell的登录属性，并且分别调用`logout`或`exit`指令
* **登录shell**和**非登录shell**的主要区别在于启动shell时所执行的startup文件不同；登录shell执行的startup文件为`~/.bash_profile`，而**非登录shell**执行的startup文件为`~/.bashrc`

## 总结

### Path语法
```bash
# 中间使用冒号分隔
export PATH=$PATH:<PATH 1>:<PATH 2>:<PATH 3>:------:<PATH N>
```

### 环境变量设置

#### 全局设置
* `/etc/paths`：全局环境变量设置，建议修改此文件
* `/etc/profile`：不建议修改此文件，全局配置，不管是哪个用户，登录时都会读取此文件
* `/etc/bashrc`：一般在这个文件中添加系统级别环境变量，全局配置，bash shell执行时，不管是何种方式，都会读取此文件

#### 单用户设置
* `~/.bash_profile`：添加用户级环境变量  
例如：设置`ANDROID_HOME`到PATH
```bash
export ANDROID_HOME=/Users/shaoc/Library/Android/sdk
export PATH=$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools:$PATH
```
* `~/.bashrc` 同上  
一般重启shell设置就会生效，如果想立刻生效，则可执行下面的语句：
```bash
source 相应的文件
```

#### zsh中配置环境变量
在安装 `oh my zsh`后，`.bash_profile`文件中的环境变量就无法起到作用，因为终端默认启动的是`zsh`，而不是`shell`，所以无法加载

* 解决方法
在`~/.zshrc`配置文件中，增加对`.bash_profile`的引用：
```bash
source ~/.bash_profile
```

`.bash_profile`文件示例：  
```bash
export ANDROID_HOME=/Users/blade/Library/Android/sdk
export GRADLE_HOME=/Users/blade/Documents/DevTools/Gradle/gradle-4.6
export FLUTTER_HOME=/Users/blade/Documents/DevTools/flutter
export PATH=$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools:$GRADLE_HOME/bin:$FLUTTER_HOME/bin:$PATH
```

## 附录
* [原关于“.bash_profile”和“.bashrc”区别的总结](https://blog.csdn.net/sch0120/article/details/70256318)
* [Mac环境变量配置](https://hao5743.github.io/2017/06/28/2017-06-28/)

