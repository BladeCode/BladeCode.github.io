---
title: Api 文档管理系统 RAP2环境搭建
date: 2018-03-27 10:20:10
update: 2018-08-01 22:10:11
categories: Api
tag: RAP
---

RAP2是采用前后端分离的形式，因此搭建完整的RAP2需要 **服务端：**[rap2-delos](https://github.com/thx/rap2-delos)，**客户端：**[rap2-dolores](https://github.com/thx/rap2-dolores) 同时部署

部署RAP2需要亲具有Node+Linux+MySQL的运维知识，如果亲对此不是很了解，建议用[http://rap2.taobao.org](http://rap2.taobao.org) 线上版本就可以

由于 **客户端：**[rap2-dolores](https://github.com/thx/rap2-dolores) 是建立在 **服务端：**[rap2-delos](https://github.com/thx/rap2-delos) 基础上，因此先搭建服务端应用

<!-- more -->

> * 截至到2018-08-01 delos 并没有发布Tag版本，应该还处于功能开发前期阶段吧。本教程是在CentOS机器上实战部署
> * 然而安装部署并不是顺利，因此记录踩过的坑（别问我为啥不用Docker，因为我司分配的机器无法满足Docker的最低内核版本），安装环境介绍：Redis，delos，dolores均在一台服务器，MySQL使用已存在的服务

## 安装基本工具
* [Git](https://git-scm.com/downloads)
* [Node 8.9.4+](https://nodejs.org/zh-cn/download)
* [Redis 4.0+](https://redis.io/download)
* [MySQL 5.7+](https://www.mysql.com/cn/downloads)

以上基本工具请根据自身需要，下载对应系统安装包，请自行解决安装配置等问题，这里不做过多说明

> Redis 安装可参考[Linux 常用应用安装](https://incoder.org/2018/05/15/linux-build)；
Redis 最好用**非安全**模式启动

## 服务端delos环境搭建

### 构建项目

> 构建项目前，请确认Node，Redis，MySQL服务均能正常使用

``` sh
git clone https://github.com/thx/rap2-delos.git
```

### 环境配置

#### 创建数据库

* Mac or Linux
    ```sql
    mysql -e 'CREATE DATABASE IF NOT EXISTS RAP2_DELOS_APP DEFAULT CHARSET utf8 COLLATE utf8_general_ci';
    ```
* Windows 环境

    进入mysql命令后执行
    ```sql
    CREATE DATABASE IF NOT EXISTS RAP2_DELOS_APP DEFAULT CHARSET utf8 COLLATE utf8_general_ci;
    ```
#### 配置文件

目录：rap2-delos/src/config  
文件：`config.dev.ts`;其中dev，表示开发环境，其他同理  
修改：`config.dev.ts`文件中`db`对象中`username`，`password`参数与**本地**或者**开发环境**的数据库信息匹配

### 启动项目

#### 安装项目依赖包

项目根目录下执行

```sh
# 安装项目所需依赖
npm install
# 全局安装PM2
npm install -g pm2
```

#### 安装TypeScript编译包

```sh
npm install typescript -g
```

> 如果下载缓慢，请使用[淘宝npm镜像](https://npm.taobao.org)

#### 初始化数据库

项目根目录下执行(该过程比较慢，耐心等待初始化完成)

```npm
npm run create-db
```

#### 编译启动项目
执行mocha测试用例和js代码规范检查
```sh
npm run check
```

* 开发模式  
启动开发模式的服务器 监视并在发生代码变更时自动重启(第一次运行比较慢，请耐心等待)
    ```sh
    npm run dev
    ```
* 生产模式  
    启动生产模式服务器
    ```sh
    npm start
    ```

看到浏览器中如下提示，表示**服务端delos**已经部署成功
>RAP2后端服务已启动，请从前端服务(rap2-dolores)访问。 RAP2 back-end server is started, please visit via front-end service (rap2-dolores).

或者在程序控制台出现如下Log，表示**服务端delos**已经部署成功  
![delos](https://res.cloudinary.com/incoder/image/upload/v1525517437/blog/gitpages-rap2-delos-success.png)

### 常见问题

#### 部署问题
1. Windows下执行`npm run build`，提示`'rm' 不是内部或外部命令，也不是可运行的程序或批处理文件`

    原因：`rm` 是Linux下命令，  
    解决方法：Windows系统可使用 `git bash` 打开该项目，执行该命令

2. 执行`npm run create-db`命令，提示`Unable to connect to the database:{ SequelizeAccessDeniedError: Access denied for user 'root'@'localhost' (using password:NO)}`
    
    原因：未修改`rap2-delos/src/config`目录下数据库配置文件，或者是与文件中的数据库信息与之连接的数据库信息不匹配
    解决方法：修改`config.dev.ts`文件数据库配置信息
    >如果修改正确无误后，执行`npm run create-db`依旧出错，那么查看该项目中是否已经存在`dist`目录，如果有，请按照如上修改对应的数据库配置信息
3. 执行`npm run dev`命令，提示`Error: listen EADDRINUSE :::8080`
    原因：8080端口被占用 
    解决方法：杀掉占用8080端口的应用
4. 执行`npm install` 命令，提示`hiredis` 编译无法通过
    原因：无权限操作`rap2-delos/node_modules/hiredis`路径
    解决方法：`sudo npm install`
    > 如果提示`sudo: npm: command not found`，请参考[stackoverflow-npm](https://stackoverflow.com/questions/31472755/sudo-npm-command-not-found),[stackoverflow-node](https://stackoverflow.com/questions/4976658/on-ec2-sudo-node-command-not-found-but-node-without-sudo-is-ok)
5. 执行`npm run dev`可以正常启动，`npm start`命令无法正常启动服务
    原因：请使用`pm2 logs`查看日志具体定位
    示例：由于Redis的安全模式，不能正常使用
    ```bash
    ReplyError: Ready check failed: DENIED Redis is running in protected mode because protected mode is enabled, no bind address was specified, no authentication password is requested to clients. In this mode connections are only accepted from the loopback interface. If you want to connect from external computers to Redis you may adopt one of the following solutions: 
    
    1) Just disable protected mode sending the command 'CONFIG SET protected-mode no' from the loopback interface by connecting to Redis from the same host the server is running, however MAKE SURE Redis is not publicly accessible from internet if you do so. Use CONFIG REWRITE to make this change permanent. 
    2) Alternatively you can just disable the protected mode by editing the Redis configuration file, and setting the protected mode option to 'no', and then restarting the server. 
    3) If you started the server manually just for testing, restart it with the '--protected-mode no' option. 
    4) Setup a bind address or an authentication password. 
    NOTE: You only need to do one of the above things in order for the server to start accepting connections from the outside.
    ```
    解决方法： 使用`--protected-mode no`方式启动

## 客户端dolores环境搭建

### 构建项目
#### 获取源代码
``` sh
git clone https://github.com/thx/rap2-dolores.git
```

### 环境配置

#### 配置文件

目录：rap2-dolores/src/config  
文件：`config.dev.ts`;其中dev，表示开发环境，其他同理  
修改：`config.dev.ts`文件，`serve`地址是 **服务端** `rap2-delos` 部署成功后的地址，默认：`'http://localhost:8080'`

### 启动项目

#### 安装项目依赖包

项目根目录下执行

```sh
npm install
```

> 如果下载缓慢，请使用[淘宝npm镜像](https://npm.taobao.org)

#### 编译启动项目

* 开发模式  
自动监视改变后重新编译
    ```sh
    npm run dev
    ```
    备注：测试用例
    ```sh
    npm run test
    ```
* 生产模式  
编译React生产包
    ```sh
    npm run build
    ```
    用serve命令或nginx服务器路由到编译产出的build文件夹作为静态服务器即可
    ```sh
    serve -s ./build -p 80
    ```

看到浏览器中出现登录页面，表示部署成功  
![dolores](https://res.cloudinary.com/incoder/image/upload/v1525517454/blog/gitpages-rap2-dolores-success.png)

### 常见问题

#### 部署问题
1. 执行`npm run dev`，提示
    ```sh
    return process.dlopen(module,path._makeLong(filename))
    ...
    ...node_modules\node-sass\vendor\win32-x64-57\binding.node is not a valid Win32 application...
    ```

    原因：项目依赖包`node-sass`没有安装完全  
    解决方法：`npm install node-sass`

2. 项目运行起来，但一直停留在加载动画那里
    
    浏览器控制台输出：  
    `GET http://127.0.0.1:8080/account/info  ==>>
    Failed to load http://127.0.0.1:8080/account/info`

    原因：未修改`rap2-delos/src/config`目录下服务端连接地址,或者修改结果与[rap2-dolores](https://github.com/thx/rap2-dolores)实际提供服务地址不匹配
    解决方法：修改`config.dev.ts`文件serve配置信息
    >如果Windows系统修改正确无误后，依旧出错，那么查看hosts(路径：C:\Windows\System32\drivers\etc)中127.0.0.1的IP前是否有`#`，如果有请取消注释

## 其他
### MySQL 运行问题
* 错误一  
![mysql](https://res.cloudinary.com/incoder/image/upload/v1525517475/blog/gitpages-rap2-mysql.png)  
原因：MySQL 集成命令没有加入系统的环境变量  
解决方法：将安装的MySQL Service路径加入系统变量
![path](https://res.cloudinary.com/incoder/image/upload/v1525517495/blog/gitpages-rap2-mysql-path.png)
* 错误二  
![create](https://res.cloudinary.com/incoder/image/upload/v1525517523/blog/gitpages-rap2-mysql-create.png)  
原因：没有数据库链接权限  
解决方法：先登录用root数据库，密码具体看自己数据库当时设置的密码

### 如何获取更新
目前请选择`master`分支源码，后续其他分支请看相应分支说明文档。在开发环境中git pull来获取最新的源码更新，每一期更新都会有对应的update.md请关注并按照上面的指示进行升级工作。

## 附录
* [Redis如何后台启动](https://blog.csdn.net/ksdb0468473/article/details/52126009)
* [Redis配置文件介绍](http://www.cnblogs.com/ysocean/p/9074787.html)
* [PM2实用入门指南](https://www.cnblogs.com/chyingp/p/pm2-documentation.html)