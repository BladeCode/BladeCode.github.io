---
title: 非对称加密——RSA
date: 2018-07-03 20:28:51
categories: Security
tag: RSA
---

这是常用加密技术的系列文章，主要包含`非对称`，`对称`，`JWT`三类常用技术的应用

## RSA
[RSA](https://zh.wikipedia.org/wiki/RSA%E5%8A%A0%E5%AF%86%E6%BC%94%E7%AE%97%E6%B3%95)：RSA加密算法是一种 **非对称** 加密算法。在公开密钥加密和电子商业中RSA被广泛使用。RSA是1977年由罗纳德·李维斯特（Ron Rivest）、阿迪·萨莫尔（Adi Shamir）和伦纳德·阿德曼（Leonard Adleman）一起提出的。RSA就是他们三人姓氏开头字母拼在一起组成的。

### RSA加密解密
公钥 **加密** 私钥 **解密**，持有公钥（多人持有，**客户端**）可以对数据加密，但是只有持有私钥（一人持有，**服务端**）才可以解密并查看数据

### RSA加签验签
私钥 **加签** 公钥 **验签**，持有私钥（一人持有，**服务端**）可以加签，持有公钥（多人持有，**客户端**）可以验签

### RSA过程示意图

![security-rsa](https://res.cloudinary.com/incoder/image/upload/v1530793864/blog/security-rsa.png)

如上图，具体表述两个场景过程

#### 结果不需加密
场景：返回的数据不需要加密(例如：绑定银行卡的时候)
* 客户端`Client A`发送使用服务端`Serve publicKey` **加密** 的密文`cipher A(包含用户的银行卡号，手机号等重要信息)`到服务器
* 服务器`Serve` 通过 `Serve privateKey`**解密**
* 服务端业务处理完成，直接返回数据(一些普通信息，比如状态码code，提示信息msg，提示操作是成功还是失败)给客户端`Client A`

#### 结果需加密
场景：返回的数据需要加密(例如：用户登录)  
* 客户端`Client B`发送使用服务端`Serve publicKey` **加密** 的密文`cipher B(包含用户名和密码等重要信息)`以及客户端`Client B`的`Client B publicKey`到服务器
* 服务器`Serve` 通过 `Serve privateKey`**解密**
* 服务端业务处理完成，直接返回数据(一般为token，token使用客户端`Client B`的`Client B publicKey`加密)给客户端`Client B`
* 客户端`Client B`使用`Client B privateKey`进行 **解密** 获取相应的用户信息等

## 密钥对
在使用RSA加密解密之前，首先要生成密钥对。所谓的密钥对，指的是公钥和私钥。RSA算法的密钥可以通过两个途径生成，一是借助`openssl`命令终端，二是使用`JDK`生成。  
本篇采用`JDK`方式生成密钥对，`openssl`方式可自行尝试

### JDK

#### Serve端密钥对

#### Client端密钥对

##### Android密钥对

##### Web密钥对

##### iOS密钥对

### OpenSSL

略...

## RSA加密

## RSA解密

## RSA缺点
虽然RSA是一种较高级别加密机制，但也存在一些缺点
1. 产生密钥很麻烦，受到素数产生技术的限制，因而难以做到一次一密。
2. 安全性，RSA的安全性依赖于大数的因子分解，但并没有从理论上证明破译RSA的难度与大数分解难度等价，而且密码学界多数人士倾向于因子分解不是NP问题。
3. 速度太慢，由于`RSA`的分组长度太大，为保证安全性，n 至少也要 `600 bit` 以上，使运算代价很高，尤其是速度较慢，较对称密码算法慢几个数量级；且随着大数分解技术的发展，这个长度还在增加，不利于数据格式的标准化。

## 附录
参考学习文章
* [一张图了解RSA加解密与加验签](https://blog.csdn.net/zhshulin/article/details/71573542)
* [RSA加密解密及RSA加签验签](https://www.cnblogs.com/loveyou/p/7299524.html)
* [RSA加解密和加签验签](https://www.jianshu.com/p/ff9bd897e96a)
* [RSA加密解密样例](https://www.jianshu.com/p/283fff43a948)
* [RSA加密解密实现](https://blog.csdn.net/hustpzb/article/details/72734578)