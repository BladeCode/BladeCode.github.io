---
title: MacBook Pro 疑难杂症
date: 2019-01-10 02:04:46
categories: macOS
tag: [Exp]
---

这是一篇记录使用macOS系统时遇到的一些疑难杂症

## iTerm2相关

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