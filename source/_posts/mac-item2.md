---
title: iTerm2 日常
date: 2020-08-05 00:32:10
categories: macOS
tag: iTerm2
---

![](https://res.cloudinary.com/incoder/image/upload/v1596547673/blog/logo2x.jpg)

iTerm2是一款优秀强大的第三方终端，相信用 Mac 的开发者，一定听过或者用过 iTerm2 这款终端应用，如果你还没使用过，没关系那么本篇文章就带你了解学习 iTerm2 中的一些常用操作，来提高你的工作效率

<!-- more -->

## 安装配置

### 下载

[iTerm2官网](https://www.iterm2.com)

> 注意：
>系统自带的终端默认使用`bash`；iTerm2默认使用`zsh`，因此两者切换如下命令
```bash
# 安装完iTerm 可使用如下命令来切换
chsh -s /bin/[zsh | bash]
```

### 配置

### 基础设置

1. 默认应用
MenuBar -> iTerm2 -> Make iTerm2 Default Term
![iterm2-default](https://res.cloudinary.com/incoder/image/upload/v1541968115/blog/mac-iterm2-default.png)

2. 全局热键
MenuBar -> iTerm2 -> preference -> Keys -> Show/hide iTerm2 with a system-wide hotkey
输入设置的快捷键，这里使用`⌘,`
![iterm2-hotkey](https://res.cloudinary.com/incoder/image/upload/v1541968115/blog/mac-iterm2-hotkey.png)

### 安装Oh my zsh

* 方式一：crul
    ```bash
    sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
    ```
* 方式二：wget
    ```bash
    sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
    ```
    ![zsh-install](https://res.cloudinary.com/incoder/image/upload/v1541968115/blog/mac-zsh-install.png)

#### PowerLine

```bash
pip install powerline-status --user
```
> 如果提示：
command not found: pip
先执行，再执行上面的字体安装命令
```bash
sudo easy_install pip
```

#### 安装PowerFonts

为避免后续的使用中，可能会遇到字符乱码的问题，因此安装字体

字体库需要首先将项目`clone`到本地，然后执行源码中的`install.sh`，根据自己的喜好存放在指定的位置
```bash
# 进入Documents目录
cd Documents
# 创建文件夹PowerFonts
mkdir PowerFonts
# 进入PowerFonts目录
cd PowerFonts
# clone源码
git clone https://github.com/powerline/fonts.git --depth=1
# 进入fonts目录
cd fonts
# 执行安装脚本
./install.sh
```

#### 设置字体及背景

* 设置字体
MenuBar -> iTerm2 -> Preferences -> Profiles -> Text -> Change Font，选择`Meslo LG`字体，L，M，S风格，看个人喜好，这里选择`Meslo LG S Powerline`
![iterm2-font](https://res.cloudinary.com/incoder/image/upload/v1541968115/blog/mac-iterm2-font.png)

* 背景设置
![iterm2-presets](https://res.cloudinary.com/incoder/image/upload/v1541968117/blog/mac-iterm2-presets.png)

#### 修改主题

```bash
# 打开.zshrc隐藏文件
vim ~/.zshrc
# 修改ZSH_THEME为agnoster
ZSH_THEME="agnoster"
```
>默认：ZSH_THEME="robbyrussell"

#### 辅助

* 高亮插件
    ```bash
    cd ~/.oh-my-zsh/custom/plugins/
    git clone https://github.com/zsh-users/zsh-syntax-highlighting.git
    vim ~/.zshrc
    # 添加zsh-syntax-highlighting到plugins中，放在git后面
    plugins=(
        git
        zsh-syntax-highlighting
    )
    # 文件最后添加，然后保存并退出
    source ~/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
    ```
    最后，对配置文件进行生效处理
    ```bash
    source ~/.zshrc
    ```
* 命令补全
安装步骤和上面的高亮插件一致
    ```bash
    cd ~/.oh-my-zsh/custom/plugins/

    git clone https://github.com/zsh-users/zsh-autosuggestions

    vi ~/.zshrc
    ```
* 设置背景图
iTerm2 -> Preferences -> Profiles -> Window -> BackGround Image

## 常用工具

### Homebrew

* [Homebrew](https://brew.sh) 是一款自由及开放源代码的软件包管理系统，用以简化macOS系统上的软件安装过程，最初由马克斯·霍威尔（Max Howell）写成
* [Homebrew Cask](https://github.com/Homebrew/homebrew-cask)，它是一套建立在 Homebrew 基础之上软件安装命令行工具，是 Homebrew 的扩展
  
#### 官方

>如果你使用的官方安装教程，需要切换 brew 的镜像源，可参考 [专治各种网络不服](https://incoder.org/2020/02/27/fuck-gfw) 文章

##### 安装

在你的终端（Terminal/iTerm2）运行如下脚本
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```

##### 卸载

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"
```

#### 国内（推荐）

>按照命令行上面的提示，进行安装

##### 安装

```bash
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"
```

##### 卸载

```bash
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/HomebrewUninstall.sh)"
```

#### 常用命令

对于 brew 和 brew cask 的命令基本一致，比如
* 【brew】：brew install [包名]
* 【brew cask】：brew cask install [包名]

##### brew 基础命令

```bash
# 安装指定包应用，如：brew install wget
brew install [包名]
# 重新安装指定应用，如：brew reinstall wget
brew reinstall [包名]
# 卸载指定包，如：brew uninstall wget
brew uninstall [包名]
# 列出已安装的软件
brew list
# 全部更新过时软件，如：brew upgrade
brew upgrade
# 指定更新过时的软件，如：brew upgrade wget
brew upgrade [包名]
# 用浏览器打开brew的官方网站
brew home
# 显示当前软件信息，如：brew info wget
brew info [包名]
# 显示包依赖，如：brew deps wget
brew deps [包名]
# 清理所有包的旧版本
brew cleanup
# 清理指定包的旧版本
brew cleanup [包名]
# 查看可清理的旧版本包，不执行实际操作
brew cleanup -n
# 查找指定包
brew search [包名]
# 查看缓存路径
brew --cache
```

##### brew service

```bash
# 查看使用brew安装的服务列表
brew services list
# 启动服务（仅启动不注册）
brew services run formula|--all
# 启动服务，并注册
brew services start formula|--all
# 停止服务，并取消注册
brew services stop formula|--all
# 重启服务，并注册
brew services restart formula|--all
# 清除已卸载应用的无用的配置
brew services cleanup
```

### Nmap

端口扫描必备工具
```bash
# 安装 nmap 工具
brew install nmap
```

![nmap](https://res.cloudinary.com/incoder/image/upload/v1605693920/blog/nmap.png)

## 常用快捷键

### 标签

1. 新建标签：⌘(command) + t
2. 关闭标签：⌘(command) + w
3. 切换标签：⌘(command) + 数字 ⌘(command) + 左右方向键
4. 切换全屏：⌘(command) + enter
5. 查找：⌘(command) + f

### 分屏

1. 垂直分屏：⌘(command) + d
2. 水平分屏：⌘(command) + ⇧(shift) + d
3. 切换屏幕：⌘(command) + ⌥(option) + 方向键 ⌘(command) + [ 或 ⌘(command) + tab 所在的数字]
4. 查看历史命令：⌘(command) + ;
5. 查看剪贴板历史：⌘(command) + ⇧(shift) + h

### 其他

1. 清除当前行：⌘(command) + u
2. 清屏：⌘(command) + r
3. 列出剪切板历史：⌘(command) + ⇧(shift) + h
4. 命令快照（很实用的一个功能）：⌘(command) + ⌥(option) + h

## 常见问题

### 文本乱码

在一开始使用macOS就已经安装iTerm2来代替了系统自带的Terminal应用，毕竟颜值是决定要不用长期使用的重要因素

iTerm2对应的配置文件：`.zshrc`，Terminal对于的配置文件：`.bash_profile` 或 `.bashrc`

* 问题：iTerm2查看本地文件，能正常显示，无乱码，但查看服务器上文件，出现乱码
* 原因：本地iTerm2终端和服务器字符集不一致，造成乱码，macOS默认Terminal应用是`utf-8`，而iTerm2默认没有设置`utf-8`编码
* 解决办法：给本地的`.zshrc`设置字符集编码
    ```bash
    # 使用vim打开.zshrc文件
    vim ~/.zshrc
    # 在文本内容末尾添加以下两行内容进行字符编码设置
    export LC_ALL=en_US.UTF-8  
    export LANG=en_US.UTF-8
    # 保存文件内容，退出vim模式，并使刚刚设置的内容生效
    source ~/.zshrc
    ```
    >帮助：可以在本地和服务器上分别使用`locale`命令来查看，本地和服务器的字符编码是否一致

### 结束指定进程

```bash
# 查看指定端口号 lsof -i:端口号
lsof -i:8088
COMMAND   PID  USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
java    72612 blade   18u  IPv6 0x21ccddb0352361e5      0t0  TCP *:radan-http (LISTEN)
# kill指定进程
kill -9 72612
```

### 免密登录服务器

一图胜千言，请看图

![ssh-login](https://res.cloudinary.com/incoder/image/upload/v1559382897/blog/ssh-login.png)

### 代理处理

在 Mac 系统上，使用 iTerm2 是一件很享受的过程，很多事情都可以通过命令行直接完成，但是一个致命的问题是，很多连接在国内环境下，异常忙，比如通过命令 clone 或处理 GitHub 上的项目，速度慢的让人抓狂，虽然电脑开启了代理（非全局），但视乎没有什么作用，针对此问题，需要让我们的终端也通过代理

1. install privoxy
    ```bash
    brew install privoxy
    ```
2. setting privoxy
    ```bash
    vim /usr/local/etc/privoxy/config
    ```
3. config privoxy
    ```bash
    listen-address 0.0.0.0:xxxx
    forward-socks5 / localhost:1080 .
    ```
    >0.0.0.0 可以让其他设备访问到，若不需要，请修改成用 127.0.0.1；xxxx是HTTP代理的默认端口；
    >localhost:1080 是 SOCKS5（shadowsocks） 默认的地址，可根据需要自行修改，且注意不要忘了最后有一个空格和点号。
4. start privoxy
    ```bash
    # 因没有安装在系统目录内，所以启动的时候需要打全路径
    sudo /usr/local/sbin/privoxy /usr/local/etc/privoxy/config
    # 查看是否启动成功（1087 端口号换成自己的）
    netstat -na | grep 1087
    # 看到有类似如下信息就表示启动成功了
    tcp4	0	0  *.1087		*.*		LISTEN
    ```
    代理端口查看  
    ![shadowsocks-proxy](https://res.cloudinary.com/incoder/image/upload/v1562848027/blog/shadowsocks-proxy.png)
5. use proxy
    * temp proxy
        如果关闭终端标签页或窗口，功能就会失效
        - star proxy
            ```bash
            # 这里的端口号1087，换成你自己的
            export http_proxy='http://localhost:1087'
            export https_proxy='http://localhost:1087'
            ```
        - cancel proxy
            ```bash
            unset http_proxy
            unset https_proxy

            ```
    * auto proxy
        - setting ~/.bash_profile 
            ```bash
            # 打开.bash_profile 文件
            vim ~/.bash_profile
            # .bash_profile文件最后添加（1087 端口替换成你自己的）
            export http_proxy='http://localhost:1087'
            export https_proxy='http://localhost:1087'
            # 保存文件  :wq 后，使配置生效
            source  ~/.bash_profile
            ```
        - 上面的方式也可以在文件(`.bash_profile`)中加入如下方法，使用时只需要在终端中输入`proxy_on`命令，关闭输入`proxy_off`
            ```bash
            function proxy_off(){
                unset http_proxy
                unset https_proxy
                echo -e "已关闭代理"
            }

            function proxy_on() {
                export no_proxy="localhost,127.0.0.1,localaddress,.localdomain.com"
                export http_proxy="http://127.0.0.1:1087"
                export https_proxy=$http_proxy
                echo -e "已开启代理"
            }
            ```
6. test
    ```bash
    # 已废弃，只能查看到当前的 IP 地址，其他信息请使用 curl cip.cc 命令
    curl ip.gs
    ```
    ![proxy-config](https://res.cloudinary.com/incoder/image/upload/v1562849019/blog/proxy-config.png)
    ```bash
    curl cip.cc
    ```
    ![proxy-config-info](https://res.cloudinary.com/incoder/image/upload/v1605687042/blog/proxy-config-info.png)

## 参考

* [Mac终端-iTerm2使用](https://zhangmiao.cc/posts/e6bc65de.html)
* [Homebrew国内如何自动安装（国内地址）](https://zhuanlan.zhihu.com/p/111014448)
* [iTerm2 用法与技巧](https://lhajh.github.io/mac/2018/04/25/Iterm2-usage-and-skills.html)

