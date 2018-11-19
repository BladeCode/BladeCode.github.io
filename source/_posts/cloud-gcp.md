---
title: Google Cloud Platform for VPN
date: 2018-11-07 14:43:46
categories: Google
tag: [vpn]
---

![cloud-gcp](https://res.cloudinary.com/incoder/image/upload/v1541559310/blog/cloud-gcp.png)

随着云产品的普及推广，各路国际大场也是纷纷推出了相关云产品的试用，其中具有代表性的[Google Cloud](https://cloud.google.com)，[Amazon](https://aws.amazon.com)，本篇主要讲解Googel Cloud产品的试用，并搭建SSR服务

<!-- more -->

Google Cloud 特点

* 可使用所有Cloud Platform产品
* 免费获得$300赠金
* 免费使用结束后不会自动收费

## 准备
* Google Email
* visa 信用卡（需要$1进行认证，认传完成后返还$1）
> 因为Google本身在大陆是无法正常访问的，因此需要先**自备梯子**，可以先使用[Lantern](https://github.com/getlantern/lantern)

## GCP

### 申请Google Cloud Platform
[官网申请](https://cloud.google.com/free)：https://cloud.google.com/free

![gcp-register1](https://res.cloudinary.com/incoder/image/upload/v1542540776/blog/gcp-register1.png)
* 国家地区：`中国`
* 服务条款：`同意`
* 动态邮件：可选，根据自身需要勾选

![gcp-register2](https://res.cloudinary.com/incoder/image/upload/v1542540935/blog/gcp-register2.png)

根据需要填写一些信息，由于我的Google账号已是开发者账号，一些信息都是完善的，所以Google直接关联了信息，因此也不会再扣除$1，如果你是新账号，详细步骤可参考附录

## VM创建
在创建VM之前，我们先进行[网络防火墙修改](https://console.cloud.google.com/networking/firewalls/list)，避免后续的麻烦

![gcp-firewall-settings](https://res.cloudinary.com/incoder/image/upload/v1542546675/blog/gcp-firewall-settings.png)
![gcp-create-firewall](https://res.cloudinary.com/incoder/image/upload/v1542545777/blog/gcp-create-firewall.png)
规则设置如下：
![gcp-firewall-rule](https://res.cloudinary.com/incoder/image/upload/v1542546500/blog/gcp-firewall-rule.png)

* 名称：自己命名一个用于区分其它得规则
* 来源IP地址范围：`0.0.0.0/0`，这个不要写错
其它按照图上设置即可

### 创建VM实例
![gcp-create-vm](https://res.cloudinary.com/incoder/image/upload/v1542546431/blog/gcp-create-vm.png)
![gcp-create-vm-init](https://res.cloudinary.com/incoder/image/upload/v1542546640/blog/gcp-create-vm-init.png)
![gcp-create-vm-course](https://res.cloudinary.com/incoder/image/upload/v1542546774/blog/gcp-create-vm-course.png)
* 名称：自己写一个即可
* 地区：建议选亚洲，别人推荐`asia-east1-c`，**台湾彰化县**实测延迟低，我这里选择了香港
* 机器类型：选微型（1个共享vCPU）
* 启动磁盘：推荐CentOS 7，当然也可以其它，选择自己熟悉的系统即可
其中关于网络的设置如下：
![gcp-create-vm-network](https://res.cloudinary.com/incoder/image/upload/v1542547144/blog/gcp-create-vm-network.png)
* 名称：任意输入即可（小写字母开头，不能为大写字母）

设置完成后，创建VM实例

## 连接VM

当然，你可以使用浏览器打开连接VM
![gcp-link-vm-chrome](https://res.cloudinary.com/incoder/image/upload/v1542547502/blog/gcp-link-vm-chrome.png)

### 使用Xshell 

经过实际操作，你会发现，在浏览器中操作延迟很高，因此我们就采用其它客户端去连接刚刚创建的这台服务器，这里以[Xshell](https://www.netsarang.com/products/xsh_overview.html)为例，其它同理

#### 密钥生成
1. 新建用户密钥生成向导  
![gcp-link-vm-xshell1](https://res.cloudinary.com/incoder/image/upload/v1542547944/blog/gcp-link-vm-xshell1.png)
2. 密钥类型长度设置  
![gcp-link-vm-xshell2](https://res.cloudinary.com/incoder/image/upload/v1542548236/blog/gcp-link-vm-xshell2.png)
3. 生成密钥  
![gcp-link-vm-xshell3](https://res.cloudinary.com/incoder/image/upload/v1542548733/blog/gcp-link-vm-xshell3.png)
4. 设置密钥名称及密码  
![gcp-link-vm-xshell4](https://res.cloudinary.com/incoder/image/upload/v1542548796/blog/gcp-link-vm-xshell4.png)
5. 保存密钥  
![gcp-link-vm-xshell5](https://res.cloudinary.com/incoder/image/upload/v1542548832/blog/gcp-link-vm-xshell5.png)

#### GCP添加密钥

* 元数据  
![gcp-link-vm-settings](https://res.cloudinary.com/incoder/image/upload/v1542549326/blog/gcp-link-vm-settings.png)
* SSH  
![gcp-link-vm-ssh](https://res.cloudinary.com/incoder/image/upload/v1542549369/blog/gcp-link-vm-ssh.png)
* SSH密钥添加  
![gcp-link-vm-create-ssh](https://res.cloudinary.com/incoder/image/upload/v1542549960/blog/gcp-link-vm-create-ssh.png)

#### Xshell 连接服务

* 配置连接的服务器地址
![gcp-link-vm-ssh-ip](https://res.cloudinary.com/incoder/image/upload/v1542554061/blog/gcp-link-vm-ssh-ip.png)
* 配置连接服务器的密钥
![gcp-link-vm-ssh-login](https://res.cloudinary.com/incoder/image/upload/v1542554117/blog/gcp-link-vm-ssh-login.png)

## SSR
通过以上的配置，我们可以使用Xshell进行SSR工具的安装，安装SSR工具前，需要先升级系统内核，按照如下执行命令

### 内核升级
```bash
# 切换到root用户
sudo -i
# 安装wget
yum install -y wget
# 安装bbr
wget --no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh
# 给bbr.sh文件设置权限
chmod +x bbr.sh
# 启动bbr.sh脚本
./bbr.sh
```
执行完成后，会提示，输入`y`并`回车`后重启，这时需要等待几分钟

重启完成后，重新连接服务器
```bash
# 切换到root用户
sudo -i
# 查看内核（版本大于4.13或以上版本，就表示OK）
uname -r
```

### 安装SSR
```bash
# 切换到root用户
sudo -i
# wget设置
wget --no-check-certificate -O shadowsocks-all.sh
# 下载安装SSR
https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh
chmod +x shadowsocks-all.sh
# 执行SSR运行脚本
./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log
```
安装过程步骤如下：
* 选择版本：推荐ShadowsocksR，输入`2`
* 设置密码
* 设置端口
* 选择加密方式，这里选择chacha20，输入`12`
* 选择协议，这里选择auth_sha1_v4，输入`3`
* 选择混淆方式，这里选择http_simple，输入`2`
![gcp-link-vm-ssh-install](https://res.cloudinary.com/incoder/image/upload/v1542556264/blog/gcp-link-vm-ssh-install.png)

等待安装完成，提示如下：
![gcp-link-vm-ssh-finish](https://res.cloudinary.com/incoder/image/upload/v1542556345/blog/gcp-link-vm-ssh-finish.png)

根据安装完成后提示的信息配置你的SSR客户端即可

## 其它
* 查询余额  
进入结算概览页面: https://console.cloud.google.com/billing/
* 扣费计算  
主机：$5/月.  
流量：谷歌云服务器出口大陆流量1T以内价格约为0.23$/1G.  
每个月可用流量：$300-$5*12=$240/12/0.23 ≈ 86G
* [SSR客户端](https://www.mediafire.com/folder/btkdbx7j9lr98/Shadowsocks_%E7%9B%B8%E5%85%B3%E5%AE%A2%E6%88%B7%E7%AB%AF#myfiles)

## 附录
* [Google Cloud Platform免费申请&一键搭建SSR & BBR加速教程](https://www.wmsoho.com/google-cloud-platform-ssr-bbr-tutorial)
* [Google Cloud使用VM虚拟机详细操作指南](https://www.rultr.com/tutorials/vps/2303.html)
* [ShadowsocksR客户端 各种隐藏使用技巧说明](https://www.wmsoho.com/how-to-use-shadowsocksr)