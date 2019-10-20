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
    curl ip.gs
    ```
    ![proxy-config](https://res.cloudinary.com/incoder/image/upload/v1562849019/blog/proxy-config.png)