---
title: Aria2 之 macOS
date: 2018-12-12 02:14:59
categories: macOS
tag: [Download]
---

## Aria2 是什么
Aria2 是一款支持多种协议的 **轻量级命令行** 下载工具。有以下特性：
* 多线程连线：Aria2 会自动从多个线程下载文件，并充分利用你的带宽；
* 轻量：运行时不会占用过多资源，根据官方介绍，内存占用通常在 4MB~9MB，使用 BitTorrent 协议，下行速度 2.8MB/s 时 CPU 占用率约 6%；
* 全功能 BitTorrent 客户端；
* 支持 RPC 界面远程控制

<!-- more -->

## Aria2 安装
```bash
brew install aria2
```

> [Homebrew](https://brew.sh)是一款自由及开放源代码的软件包管理系统，用以简化Mac OS X系统上的软件安装过程，以Ruby语言写成，默认安装在`/usr/local`

## Aria2 配置

```bash
# 进入~路径
cd ~
# 创建.aria2文件夹
mkdir .aria2
# 创建aria2.conf配置文件
touch aria2.conf
```

复制以下内容保存在`aria2.conf`文件中，**修改** `dir=/Users/blade/Downloads`路径即可

```
#用户名
#rpc-user=user
#密码
#rpc-passwd=passwd
#上面的认证方式不建议使用,建议使用下面的token方式
#设置加密的密钥
#rpc-secret=token
#允许rpc
enable-rpc=true
#允许所有来源, web界面跨域权限需要
rpc-allow-origin-all=true
#允许外部访问，false的话只监听本地端口
rpc-listen-all=true
#RPC端口, 仅当默认端口被占用时修改
rpc-listen-port=6800
#最大同时下载数(任务数), 路由建议值: 3
max-concurrent-downloads=5
#断点续传
continue=true
#同服务器连接数
max-connection-per-server=5
#最小文件分片大小, 下载线程数上限取决于能分出多少片, 对于小文件重要
min-split-size=10M
#单文件最大线程数, 路由建议值: 5
split=10
#下载速度限制
max-overall-download-limit=0
#单文件速度限制
max-download-limit=0
#上传速度限制
max-overall-upload-limit=0
#单文件速度限制
max-upload-limit=0
#断开速度过慢的连接
#lowest-speed-limit=0
#验证用，需要1.16.1之后的release版本
#referer=*
#文件保存路径, 默认为当前启动位置
dir=/Users/blade/Downloads
#文件缓存, 使用内置的文件缓存, 如果你不相信Linux内核文件缓存和磁盘内置缓存时使用, 需要1.16及以上版本
#disk-cache=0
#另一种Linux文件缓存方式, 使用前确保您使用的内核支持此选项, 需要1.15及以上版本(?)
#enable-mmap=true
#文件预分配, 能有效降低文件碎片, 提高磁盘性能. 缺点是预分配时间较长
#所需时间 none < falloc ? trunc << prealloc, falloc和trunc需要文件系统和内核支持
file-allocation=prealloc
```

### 开启 Aria2
终端中输入，其中xxx是你的电脑用户名
```bash
aria2c --conf-path="/Users/xxx/.aria2/aria2.conf" -D
```

### Aria2 开机自启
1. 创建`aria2.plist`文件
    ```bash
    cd ~/Library/LaunchAgents
    touch aria2.plist
    ```
2. 修改`aria2.plist`文件内容，其中`<array></array>`中的值改为自己电脑上 aria2c 命令的路径，可以在终端输入which aria2c查看，将WorkingDirectory后面的`<string></string>`中的值改为自己的下载路径
    ```bash
    <?xml version="1.0"encoding="utf-8"?>
    <!DOCTYPE plist PUBLIC"-//Apple//DTD PLIST 1.0//EN"
    "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
        <dict>
            <key>KeepAlive</key>
            <true />
            <key>RunAtLoad</key>
            <true />
            <key>Label</key>
            <string>aria2</string>
            <key>ProgramArguments</key>
            <array>
                <string>/usr/local/bin/aria2c</string>
            </array>
            <key>WorkingDirectory</key>
            <string>/Users/blade/Downloads</string>
        </dict>
    </plist>
    ```

### 启用Web
其实，如果你喜欢使用命令来操作，那么此步可跳过
```bash
# 获取项目代码
git clone https://github.com/ziahamza/webui-aria2
# 打开 index.html 文件
cd webui-aria2/docs
open index.html
```

## 其他

### 进行brew更新警告
警告内容：`Unbrewed header files were found in /usr/local/include ...`
原因：系统中已存在下面列表中包含的包内容不是通过`brew`进行安装
解决方法：删除那些文件就可以了
```bash
# 或者获取sudo权限删除
sudo rm  -rf ‘/usr/local/bin/node’
# 重新安装node
brew install node
```

## 附录
* [Mac安装使用aria2,AriaNg下载百度网盘资源](https://blog.tearth.me/mac_aria2_ariang/)
* [如何配置 Aria2 来进行文件下载](https://mofiter.com/2018/08/19/%E5%A6%82%E4%BD%95%E9%85%8D%E7%BD%AE-Aria2-%E6%9D%A5%E8%BF%9B%E8%A1%8C%E6%96%87%E4%BB%B6%E4%B8%8B%E8%BD%BD/)
* [Aria2 - 下载神器](https://mac-setup.wildflame.org/aria_2/readme.html)
* [Aria2 命令使用参考文档](https://github.com/erasin/notes/blob/master/linux/soft/aria2.md)