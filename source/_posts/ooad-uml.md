---
title: OOAD 与 UML
date: 2019-05-27 22:00:10
categories: DevTool
tag: [OOAD, UML]
---

OOAD（Object Oriented Analysis and Desigin） 是根据 OO 的方法学，对软件系统进行分析和设计的过程
* OOA（Object Oriented Analysis）：分析阶段
* OOD（Object Oriented Desigin）：设计阶段

## What to do
分析阶段主要解决以下问题
* 建立针对业务问题域的清晰视图
* 列出系统必须要完成的核心任务
* 针对问题域建立公共词汇表
* 列出针对此问题域的最佳解决方案

<!-- more -->

## How to do
设计阶段主要解决以下问题（How to do?）
* 如何解决具体的业务问题
* 引入系统工作所需的支持元素
* 定义系统的实现策略

## OOP的主要特征

### 抽象（abstract）
* 忽略到一个对象或实体的细节而只关注其本质特征的过程
* 简化功能与格式
* 帮助用户与对象交互

### 封装（encapsulation）
* 隐藏数据和实现
* 提供公共方法供用户调用功能
* 对象的两种视图
    * 外部视图：对象能做的工作
    * 内部视图：对象如何完成工作

### 继承（inheritance）
* 通过存在的类型定义新类型的机制
* 通常在两个类型之间存在“is a” 或 “kind of” 这样的关系
* 通过继承可实现代码重用，另外继承也是多态的基础
* 如苹果 “is a” 水果

### 多态（polymorphism）
* 一个名称，多种形式
* 基于继承的多态
* 调用方法时根据所给对象的不同选择不同的处理方式
* 如 Football——play()
* 给出一个具体的足球或篮球，用户自动知道该使用谁的方式去执行

### 关联（association）
* 对象之间交互时的一种引用方式
* 当一个对象通过对另一个对象的引用去使用另一个对象的服务或操作时，两个对象之间便产生了关联
* 如 person 使用 computer，person 与 computer 之间就存在了关联关系

### 聚合（aggregation）
* 关联关系的一种，一个对象成为另一个对象的组成部分
* 使用关系强的关联
* 在两个对象之间存在 “has a”这样的关系，一个对象作为另一个对象的属性存在，在外部对象被产生时，可由客户端指定与其关联的内部对象

> 如汽车与轮胎，轮胎作为汽车的一个组成部分，它和汽车可由分别产生以后转配起来使用，但汽车可由换新轮胎，轮胎也可以卸下来给其他汽车使用

### 组合（composition）
* 当一个对象包含另一个对象时，外部对象负责管理内部对象的生命周期的情况
* 关联关系中最为强烈的一种
* 内部对象的创建由外部对象自己控制
* 外部对象不存在时，内部对象也不能存在

> 如电视机与显示器

### 内聚与耦合（cohesion & coupling）
* 域模型是面向对象的。在面向对象术语中域模型也可称为设计模型。
* 域模型由以下内容组成
    * 关联（Association）:一对多，多对一，一对一
    * 依赖（Dependency）
    * 聚集（Aggregation）：整体和部分之间的关系
    * 一般化（泛化）（Generalization）：类与类之间的**继承**
* 内聚：度量一个类独立完成某项工作的能力
* 耦合：度量系统内或系统之间依赖关系的复杂度
* 设计原则：增加内聚，减少耦合

## 开发过程概述

### 传统开发过程
* 瀑布模型(真实环境，不可能满足这些)

### 统一软件开发过程（USDP）
特点： 项目是迭代，递增
* 迭代指生命周期中的一个步骤
* 迭代导致“递增”或者是整个项目的增长
* 大项目分解为子项目
* 在每一个迭代的阶段，应该做以下工作
    * 选择并分析相关用例
    * 更加所选架构进行设计
    * 在组件层次实现设计
    * 验证组件满足用例的需要
* 当一次迭代满足目标后，开发进入下一个迭代周期
* 每一个周期包含一次或多次迭代
* 一个阶段的结束称之为“里程碑”

### 阶段

#### 初始化阶段
该阶段的增量集中于：
* 项目启动
* 建立业务模型
* 定义业务问题域
* 找出主要的风险因素
* 定义项目需求的外延
* 创建业务问题域的相关说明文档

#### 细化阶段
本阶段的增量集中于
* 高层的分析与设计
* 建立项目的基础框架
* 监督主要的风险因素
* 制订达成项目目标的创建计划

#### 构建阶段
 本阶段的增量集中于
 * 代码及功能的实现

#### 移交阶段
本阶段的增量集中于：
* 向用户发布产品
* beta 测试
* 执行性能调优，用户培训和接收测试

#### 阶段特点
每一个阶段所包含的工作流，每一次递增都由 5 个部分工作流组成
* 需求与初始化分析
* 分析
* 设计
* 实现
* 测试
* 每一次迭代执行工作流的深度不同
* 早期的迭代在深度上覆盖初始工作流，后期迭代在深度上覆盖后期工作流
* 80/20原则

## UML
UML(Unified Modeling Language)统一建模语言，图形化语言表示，它可以帮助我们在 OOAD 过程中标识元素，构建模块，分析过程并可通过文档说明系统中的重要细节

### 静态模型（static model）
* 创建并记录一个系统的静态特征
* 反映一个软件系统基础，固定的框架结构
* 创建相关问题域主要元素的视图
* 静态建模包括：
    * **<font color=#FF0000>用例图（use case diagrams）</font>**
    * **<font color=#FF0000>类图（class diagrams）</font>**
    * 对象图（object diagrams）
    * 组件图（component diagrams）
    * 部署图（deployment diagrams）

### 动态模型（dynamic model）
* 用以展示系统的行为
* 动态建模包括：
    * **<font color=#FF0000>时序图（sequence diagrams）</font>**
    * 协作图（collaboration diagrams）
    * 状态图（state chart diagrams）
    * 活动图（activity diagrams）

### UML 其他重要元素
* 包（package）
* UML 的扩展机制
    * 注释（comments）
    * 构造型（stereotypes）
    * 标记值（tagged values）
    * 限制（constraints）

### 示例

#### 用例图
* 展示系统的核心功能及逾期交互的用户
* 用户被称为“活动者”（Actor）
* 用例使用椭圆表示
* 为简化建模过程，用例图可以标注优先级
![uml-usecase-diagram](https://res.cloudinary.com/incoder/image/upload/v1559486362/blog/uml-usecase-diagram.png)

#### 类图
* 表现类的特征
* 类图描述了多个类，接口的特征，以及对象之间的协作与交互
* 由一个或者多个矩形区域构成，内容包括
    * 类型（类名）
    * 属性（可选）
    * 操作（可选）
![uml-class-diagram](https://res.cloudinary.com/incoder/image/upload/v1559486361/blog/uml-class-diagram.png)

#### 对象图
* 表现对象的特征
* 对象图展现了多个对象的特征及对象之间的交互
![uml-object-diagram](https://res.cloudinary.com/incoder/image/upload/v1559486360/blog/uml-object-diagram.png)

#### 组件图
* 表示软件组件之间的关系
![uml-component-diagram](https://res.cloudinary.com/incoder/image/upload/v1559486358/blog/uml-component-diagram.png)

#### 部署图
* 表现用于部署软件应用的物理设备信息
![uml-deployment-diagram](https://res.cloudinary.com/incoder/image/upload/v1559486362/blog/uml-deployment-diagram.png)

#### 时序图
* 捕捉一段时间范围内多个对象之间的交互信息
* 强调消息交互的时间顺序
