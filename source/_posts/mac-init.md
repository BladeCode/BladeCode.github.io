---
title: MacBook Pro 初始化
date: 2018-11-10 09:44:46
categories: macOS
tag: [Build]
---

今天拿到了一辆跑车 MBP，虽然不是顶配，也能算上中等吧，废话不啰嗦，上来就是一顿操作猛如虎，最终效果就是唬

跑车的一些零配件来源地[Awesome Mac](https://github.com/jaywcjlove/awesome-mac/blob/master/README-zh.md)

软件的安装，这里不再赘述，这里主要对常用开发软件的配置进行记录

<!-- more -->

## JDK 
作为Android开发者，JDK的安装那是少不了

### 下载
在Oracle 官网下载所需JDK 版本，这里举例：[JDK1.8.0_191](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

### 安装
此处省略，简单的安装步骤

### 配置
以下**命令**相关操作，均在自带系统**终端**应用或者自己安装的其他终端命令工具
```bash
# 查看安装的Java版本
java -version
# 编辑profile文件
sudo vim /etc/profile
# 在打开的 profile 文件中，最下面加入以下文本，添加完成后，保存退出
JAVA_HOME="/Library/Java/JavaVirtualMachines/jdk1.8.0_191.jdk/Contents/Home/"

CLASS_PATH="$JAVA_HOME/lib"

PATH=".:$PATH:$JAVA_HOME/bin"

# 使配置生效
source /etc/profile
# 查看JAVA_HOME是否正确
echo $JAVA_HOME
```
![jdk-command](https://res.cloudinary.com/incoder/image/upload/v1541968115/blog/mac-jdk-comand.png)

![jdk-config](https://res.cloudinary.com/incoder/image/upload/v1541968115/blog/mac-jdk-config.png)

>注意：
>1. `JAVA_HOME`中`jdk1.8.0_191.jdk`是自己安装对应版本的文件夹，可以在Finder中，快捷键：Command + Shift + G，输入：`/Library/Java/JavaVirtualMachines/`，最终得到对应的文件夹名，如：`jdk1.8.0_191.jdk`
>2. vim模式下，输入“i”：表示，插入，“esc”：表示退出编辑模式，“:wq!”：表示保存并退出

## Git
直接在自带系统**终端**应用中，输入`git --version`，由于之前并没有安装，系统会提示，直接同意并安装即可

### GitHub配置
```bash
# 查看本地是否生成过秘钥，如果该文件夹不存在，则表示未生成过秘钥
cd ~/.ssh
# 生成一个github的秘钥，这里github可以根据喜好自己命名（默认.ssh路径，不添加密码等操作，直接三次回车，即可生成秘钥）
ssh-keygen -t rsa -C "github"
# 查看公钥
cat ~/.ssh/id_rsa.pub
# 复制公钥添加到GitHub的SSH设置中，这里省略操作截图步骤
```
![mac-github-config](https://res.cloudinary.com/incoder/image/upload/v1542034940/blog/mac-github-config.png)

## iTerm2
iTerm2是一款优秀强大的第三方终端，这里主要介绍如何进行对终端主题个性化的配置

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

## Gradle配置
* 下载地址：[官网](http://services.gradle.org/distributions)，下载-all版本
* 设置GRADLE_HOME路径
    ```bash
    # 打开.bash_profile文件
    open -e .bash_profile
    # Gradle_HOME环境设置，并保存
    GRADLE_HOME=/Users/blade/Documents/DevTools/Gradle/gradle-4.6
    export GRADLE_HOME
    export PATH=$PATH:$GRADLE_HOME/bin
    # 配置文件生效
    source ~/.bash_profile
    # 验证配置
    gradle -version
    ```

    >如果提示The file /Users/blade/.bash_profile does not exist.则在根路径下创建`.bash_profile`文件
    >执行命令`touch .bash_profilesss`

    ![gradle-config](https://res.cloudinary.com/incoder/image/upload/v1541968116/blog/mac-gradle-config.png)
