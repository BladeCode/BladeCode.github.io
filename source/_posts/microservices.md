---
title: 【译】• 微服务
date: 2019-06-01 10:00:00
categories: 
  - [Translation]
  - [SpringBoot]
tag: [SpringBoot]
---

这是第一篇翻译文章，用于学习近些年火热的微服务，这篇是`微服务`概念是由 `James Lewis` 所著，虽然官网已有[中文翻译](https://mp.weixin.qq.com/s/clbRQZ6-5YoX68MzwBfQ_Q)，但是在学习过程中，应该应该动手输出，这样有助于对知识的理解和记忆，废话不多说，开始翻译

## 微服务
> 近些年术语“微服务架构”就像雨后春笋般蓬勃的发展，微服务描述软件应用设计是独立可部署服务一个特殊方式。虽然这些都不够准确的去定义一个架构风格，但存在一些通用的特质（大家达成共识的特征），如何去组织围绕业务能力，如何自动化部署，端点的智能发现，以及语言和数据去中心化的控制

<!-- more -->

### James Lewis
James Lewis 是 ThoughtWorks 的首席顾问，也是技术顾问委员会的成员。James 利用小型协作服务构建应用程序的兴趣起源于大规模集成企业系统的背景。他构建数量级的系统都使用微服务，并且几年来，他一直积极参与不断地社区发展

### Martin Fowler
Martin Fowler 是一个作者，演讲家，和普通的软件开发，他一直对如何组件化软件系统的问题感到困惑，他希望微服务能够实现其倡导者所发现的早期承诺

----------

“微服务”在当时任然是一个新的名词。虽然我们的自然倾向是通过这些构建，这个技术分隔软件系统，这个术语描述了一种我们发现越来越有吸引力的软件系统风格。我们已经看到很多项目在过去的几年中使用这种风格，到目前为止的结果是积极的，以至于对于我们的许多同事而言，这已成为构建企业级应用的默认样式。然而，遗憾的是，没有太多信息可以描述微服务的风格以及微服务是如何实现

简而言之，**微服务架构风格**[^1]是一个开发单应用作为小型服务套件开发模式，<font color=#FF0000>每个应用运行在自己的进程中并且他们之间通过轻量级的机制进行通信，通常的如 HTTP 资源 API</font>。这些服务围绕业务能力并且这些都是<font color=#FF0000>可以独立的自动化部署</font>。这些服务我们进行**去中心化**的集中管理。<font color=#FF0000>这些服务可以使用不同的编程语言来编写，同样也可以使用不同的数据存储技术</font> 

开始解释微服务风格，将它与单体风格进行比较是有用的：作为单元构建的单片应用程序。企业应用程序通常由 3 个之上主要构成部分：
* 客户端（由用户机器上的浏览器中运行的 HTML 页面和 JavaScript 组成）
* 数据库（由插入到公共中的许多表组成，通常是关系型，数据库管理系统）
* 服务端应用程序。
这个服务端应用程序处理 HTTP 请求，执行域逻辑，从数据库中检索和更新数据，并选择装配发送到浏览器的 HTML 视图。这个服务端应用程序是一个单体的，一个逻辑可执行文件[^2] ，任何一次更改都生成一个新的版本去构建和部署

这种单体服务是构建这种系统的自然方式。所有的请求逻辑处理都运行在一个进程中，允许你使用语言的基本特性将应用划分为类，功能和命名空间。对于一些其他样例，你可以运行和测试应用在开发者的笔记本上，并使用部署管道确保已正确测试并部署到生成环境中。你可以通过负载均衡运行许多实例来进行水平扩展（常见单体应用模式 前面通过Nginx负载均衡，在 Nginx 后面运行多个应用实例）

单体应用程序可以成功，但是越来越多的人感到沮丧-特别是随着更多应用程序部署到云端。更改周期紧密相连-对应用程序的一小部分更改，需要重新构建和部署整个应用。随着时间的推移，通常很难保持良好的模块化结构，是的更难以保持改变只影响模块中的一个模块的更改。扩展需要扩展整个应用程序，而不是需要更多资源的部分扩展

![图 1：单体应用和微服务](https://martinfowler.com/articles/microservices/images/sketch.png)
从上图可知单体应用和微服务在部署的角度（可升缩角度）来讲：
* 单体应用：进行可升缩，是将单体应用整个进行升缩，每台机器上的应用都是相同的
* 微服务：每个服务都是独立的单元，可根据需要对服务单元进行任意组合进行升缩，每台机器上的应用是不相同的

这些挫折导致了微服务架构的风格：构建应用程序作为服务套件。事实上服务是独立部署和可扩展的，每个服务之间也提供坚实模块的边界，甚至允许不同的编程语言编写不同的服务。它们也可以由不同的团队来管理

我们并不认为微服务风格是新颖的或创新的，其根源可以归结为 Unix 的设计原则。但是我们认为这些没有足够的人考虑微服务架构风格，如果使用它们，许多软件的开发会更好，从中获益匪浅

## 微服务架构的特征

我们不能说微服务架构风格有正式的定义，但我们可以尝试描述我们认为合适标签的架构的共同特性。与概述共同特征的任何定义一样，并非所有的微服务架构都具备所有的特征，但我们确实希望大多数微服务架构都具有大多数的特性。虽然我们的作者一直是这个相当宽松社区的积极成员，我们的目的是尝试描述我们在自己的工作中所看到的以及我们所知道的团队的努力，特别是我们没有规定一些符合的定义

### 服务组件化

只要我们参与软件行业，人们一直希望通过将组件集成在一起来构建系统，我们在物质世界中看待事物的方式有很多类似，在过去的几十年中，我们已经看到了大多数语言平台的大型公共 libraries 的大量进展

在谈论组件时，我们遇到了组件构成的困难定义，我们的定义**组件**是一个可独立更换和升级的软件单元

微服务架构会使用到这些 libraries，但他们讲自己的软件组件化的主要方式是分解为服务。我们定义 **libraries** 作为组件链接到程序中，也可以使用内存函数中调用的组件，而**服务**是进程外的组件，它们与诸如 Web 服务请求或远程调用之类的机制进行通信。（这与许多 OOP[^3] 中的服务对象感念不同）
>所谓的库都是调用在同一个进程当中，而服务的调用是跨进程的，要通过 Web 请求的方式或者是 RPC 的方式进行通信

将服务用作组件（而不是 libraries）的一个主要原因是服务可以独立部署。如果你在单个进程中有多个 libraries组成的应用程序[^4]，则对任何单个组件的更改都会导致必须重新部署整个应用程序。但如果一个应用由多个服务组成，你可以期望任何单服务的改变仅需要更新自己。这不是绝对的，一些更改改变了部分服务接口，从而导致一定的协调，但良好的微服务架构的目标是通过服务合同中的内聚服务边界和演化机制来最小化这些架构

将服务用作组件的另一个结果是更明确的组件接口，多数语言没有很好的机制来定义已[发布的接口](https://martinfowler.com/bliki/PublishedInterface.html)。通常这并不仅仅只有文档和原则性问题，来防止客户破坏组件的封装原则，而且会导致组件间过度紧密耦合。通过使用显示远程调用机制，服务可以更容易地避免这种情况

使用这种服务也有一些缺点。远程调用通常要比进程内调用成本要高，因此远程调用需要更粗粒度的，这通常更难以使用。如果你需要去更改组件间的职责分配，那么当你跨越流程边界时，这种行为的变化就更难

在第一次中，我们可以观察到服务可以映射到运行时的进程，但这只是一个大致的描述。一个服务可能包含多个进程，这些进程始终一起开发和部署，这样的应用进程和数据库是这个服务所独有的

### 围绕业务能力进行组织

在寻找将大型应用程序拆分为多个部分时，通常管理侧重于技术层，导致 UI 团队，服务器逻辑团队和数据库团队。当团队按照这些方式分开时，即使是简单的更改也可能导致跨团队项目需要时间和预算批准。一个聪明的团队围绕这个进行优化，并未减少这两个情况的发生——会强制将逻辑放置到可以访问的应用中。换句话说，逻辑无处不在。这就是康威定律[^5] 的一个例子
>Any organization that designs a system (defined broadly) will produce a design whose structure is a copy of the organization's communication structure
>                                               ——Melvyn Conway, 1967

![Conway's Law in action](https://martinfowler.com/articles/microservices/images/conways-law.png)
微服务划分方法是不同的，分为围绕**业务能力**组织的服务。此类服务为该业务领域采用广泛的软件实现，包括用户页面，持久存储，以及任何额外协作。因此，团队是跨职能的，包括开发所需的全部技能：用户体验，数据库和项目管理

![Service boundaries reinforced by team boundaries](https://martinfowler.com/articles/microservices/images/PreferFunctionalStaffOrganization.png)

>微服务有多大？
虽然“微服务”已经成为这种架构风格的流行名称，但它的名字确实导致了对微服务的不关注以及关于什么构成“micro”的争论。在我们与微服务从业者的对话中，我们看到了一系列服务规模。报道的最大数量遵循亚马逊的 Two Pizza Team 的概念（比如：整个团队都可以讨厌两个披萨），意味着不超过十二人。对于规模较小的服务，我们已经看到一个6人的团队在支持6个服务。
>
> 这导致了这样的问题：在这个尺寸范围内是否存在足够大的差异，每个人的服务和每个服务的尺寸不应该集中在一个微服务的标签下。目前我们认为将它们组合在一起会更好，但当我们进一步探索这种风格时，我们肯定会改变主意

以这种方式组建的一家公司是 [www.comparethemarket.com](http://www.comparethemarket.com)。跨职能团队负责构建和运营每个产品，每个产品分为多个通过消息总线进行通信的单独服务。

大型单机应用程序也可以围绕业务功能进行模块化，尽管这不是常见的情况。当然，我们会敦促一个庞大的团队构建一个单体的应用，以便在业务线上划分自己。我们在这里看到的主要问题是，它们往往围绕太多的背景进行组织。如果整体跨许多这些模块化边界，那么团队中的个体成员很难将它们适应其短期组织中。此外，我们看到模块化生产线需要大量的规范来执行。服务组件所需要的更明确的分离是的更容易保持团队边界清晰

### 产品不是项目

我们看到的大多数应用程序开发工作都使用项目模型：其目的是提供一些软件然后被认为是完成的。完成后，软件将移交给维护组织，构建他的项目团队将被解散。

微服务支持者倾向于避免这种模式，而是倾向于认为团队应该在其整个生命周期内拥有产品。对此的一个共同启示是亚马逊的概念[“你构建，运行它”](https://queue.acm.org/detail.cfm?id=1142065)，开发团队对生产中的软件负全部责任。这使的开发人员能够日常接触他们的软件在生成中的行为，并增加与用户的联系，因此他们必须承担至少一些支持工作。

产品心态，与业务能力的联系紧密相连。不是将软件视为一组要完成的功能，而是存在一种持续的关系，其中的问题是软件如何帮助其用户增强业务能力

没有理由不采用单一应用程序采用相同的方法，但较小的服务粒度可以更容易地在服务开发人员和用户之间创建个人关系

### 智能端点和哑的 pips

在构建不同进程间通信结构时，我们已经看到许多产品和方法都强调将重要的`smarts`放入沟通机制本身。一个很好的例子是企业服务总线（ESB），其中 ESB 产品通常包括用于消息路由，编排，转换和应用业务规则的复杂工具。

>微服务和 SOA
>当我们谈到微服务时，一个常见的问题，这是否是我们十年前看到的面向服务的体系结构（SOA），这一点是有道理的，因为微服务风格非常类似于 SOA 的一些拥护者所支持的。然而，问题在于 SOA 意味着[太多不同的东西](https://martinfowler.com/bliki/ServiceOrientedAmbiguity.html)，并且大多数时候我们遇到称为“SOA”的东西，它与我们在这里描述的样式有很大不同，通常是由于专注于用于集成单片应用程序的 ESB
>
>特别是我们已经看到了许多拙劣的服务导向实现——从隐藏 ESB[^6] 中的复杂性的趋势，失败的多年计划，耗资数百万美元，没有任何价值，积极治理模式，积极抑制变化，有时很难看到过去的这些问题
>
>当然，微服务社区中使用的许多技术都是从开发人员在大型组织中集成服务的经验中发展而来的。[容忍读者](https://martinfowler.com/bliki/TolerantReader.html)模式就是一个例子。使用网络努力做出了贡献，使用简单的协议是从这些经验中得到的另一种方法——远离中心标准的反应，这种标准已达到复杂性，坦率地说，[令人叹为观止](http://wiki.apache.org/ws/WebServiceSpecifications)（只要你需要一个本体来管理你的本体，你就知道你遇到了很大的麻烦）
>
>SOA 的这种场景表现导致一些服务提倡者完全拒绝 SOA 标签，尽管其他人认为服务是 SOA 的一种形式，也许正确的服务向导，无论哪种方式，SOA[^7] 意味着这些不同的事物意味着有一个更清晰地定义这种建筑风格的术语是有价值的

微服务社区倾向于采用另一种方法：智能端点和愚蠢的 pips。从微服务构建的应用程序旨在尽可能地分离和聚集——他们拥有自己的域逻辑，在经典的 Unix 意义上更像是过滤器——接收请求，适当地应用逻辑并产生响应。这些是使用简单的 RESTish 协议而不是复杂的协议（如 WS-Choregoraphy 或 BPEL 中央工具的编排）编排的。

最常用的两个协议是 HTTP 请求——响应资源 API 和轻量级消息[^8] 传递。第一个最好的表达方式是
>Be of the web, not behind the wed  
>                  -- lan Robinson

微服务团队使用万维网（在很大程度上，Unix）构建的原则和协议。经常使用的资源可以通过开发人员或操作人员的非常小的努力来缓存。

常用的第二种方法是通过轻量级消息总线进行消息传递。选择的基础设施通常是哑的（哑仅作为消息路由器的行为）—— 向 RabbitMQ 或者 ZeroMQ 这样的简单实现不仅仅提供可靠的异步结构——智能功能存在于那些生产和消费诸多消息的各个端点中，即存在于各个服务中。

在一个单体应用中，组件在进程中执行，它们之间通信是通过方法调用或函数调用。将整体变为微服务的最大问题在于改变通信模式。从内存中方法调用到 RPC 的简单转换导致繁琐的通信，这种通信效果不佳。相反，您需要粗粒度的方法替换细粒度的通信。

### 去中心化的治理

集中治理的后果之一是在单个技术平台上实现标准化的趋势。经验表明，这种方法是有限的——不是每个平台是一样的，不是每个平台的解决方案是一致的。我们推荐使用正确的工具来完成工作，而单体应用程序在一定程度上利用不同的语言，但这并不常见

将单个应用组件拆分为多个服务，我们可以在构建每个组件时做出选择。你希望使用 Node.js 建立一个简单的报告页面？没问题。想通过 C++ 来实现出彩的实时组件？没毛病。想换不同风格的数据库，以更好地适应一个组件的读取行为？可以重建

当然，只是因为你可以做某件事，并不意味着你可以应该——但以这种方式划分你的系统意味着你可以选择

构建微服务的团队也更喜欢采用不同的标准方法。他们更倾向于其他开发人员可以使用的有用工具来解决与他们面临的类似问题，而不是使用在纸上某处写下的一组定义标准。这些工具通常从实现中收集并广泛的共享，有时，但不仅仅是使用内部开源模型。现在 Git 和 GitHub 已经成为事实上的版本控制系统，开源实践在内部变得越来越普遍。

Netflix 是遵循这一理念的组织的一个很好的例子。共享有用的，尤其是经过实战考验的代码，因为鼓励其他开发人员以类似的方式解决类似问题，但如果需要，可以选择不同的方法。共享库往往侧重于数据存储，进程间通信的常见问题，我们将在下面进一步讨论基础架构自动化

对于微服务社区来说，管理费用特别缺乏吸引力。这并不是说社区不重视服务契约。恰恰相反，因为往往会有更多。只是他们正在寻找管理这些契约的不同方式。像[容错读取](https://martinfowler.com/bliki/TolerantReader.html)和[消费者驱动的契约](https://martinfowler.com/articles/consumerDrivenContracts.html)这样的模式通常应用于微服务。这些援助服务契约独立发展。在构建过程中执行消费者驱动的契约可以增强信心，并提供有关您的服务是否正常运行的快速反馈。事实上，我们知道澳大利亚的一个团队通过消费者驱动的契约推动服务的建设。他们使用简单的工具来定义合同服务。在编写新服务的代码之前，这将成为自动构建的一部分。然后，该服务仅构建在满足合同的程度——在构建新软件时避免“YAGNI”[^9] 困境的优雅方法。这些技术和围绕他们成长的工具通过减少服务间的时间耦合来限制重要合同管理的需要

>多语言，多选择
JVM 作为平台的发展只是在一个通用平台中混合语言的最新例子。近十年依赖，通常的做法是采用更高级别的语言来更高级别的抽象。同样，在平台底层以更低层次的编程语言编写性能敏感的代码也很普遍。然而，许多单块系统并不需要这种级别的性能优化，另外 DSL 和更高层次的抽象也不常用（这令我们感到失望）。相反，许多单体应用通常就使用单一编程语言，并且对所用的技术数量进行限制的趋势[^10] 

也许去中心化治理的最高点就是建立它/运行它，由亚马逊推广的精神。团队负责他们构建的软件的所有方面，包括全天候运行软件。这种责任水平的下放绝对不是常态，但我们确实看到越来越多的公司将责任推向开发团队。Netflix 是另一个采用这种精神[^11] 的组织。每天晚上凌晨 3 点您被你的寻呼机唤醒，无疑是在编写代码时专注于质量的强大动力。这些想法与传统的集中治理模式相差甚远

### 去中心化数据管理

去中心化数据管理以多种不同的方式呈现。在最抽象的层面上，它意味着世界的概念模型在不同系统之间会有所不同。在整个大型企业时，这是一个常见问题，客户的销售视角将与支持视角不同。从销售视角中称为“客户”的某些内容，可能根本不会出现在支持视角中。那些在两个视角中具有相同属性的事物，或许在语义上有微妙的不同

>经过实战检验的标准和强制执行的标准
微服务团队倾向于避开企业架构小组制定的严格执行标准，但很乐意使用甚至宣传 HTTP，ATOM 和其他微格式等开放标准的使用，这有点很二分法
>
>关键的区别在于如何制定标准以及如何实施标准。有 IETF 等团体管理的标准只有在更广泛的世界中有多高实施时才能成为标准，并且通常来至于成功的开源项目
>
> 这些标准与企业的许多标准不同，后者通常由最近没有编程或受供应商过度影响的团体开发

此问题在应用程序间很常见，但也可能在应用程序中发生，特别是将应用程序划分为单独的组件时。一种有用的思考方式是“领域驱动设计”中的[“限定上下文”](https://martinfowler.com/bliki/BoundedContext.html)的概念。DDD 将复杂领域划分为多个限界上下文，并映射出他们之间的关系。此过程对单体和微服务架构两者都很有用，而且就像前面有关“业务功能”一节中所讨论的那样，在服务和各个限界上下文之间所存在的自然的联动关系，能有助于澄清和强化这种划分。

除了关于概念模型的分散决策之外，微服务还分散了数据存储决策。虽然单一应用程序更喜欢使用单个逻辑数据库来存储持久性数据，但企业通常更喜欢在一系列应用程序中使用单个数据库——其中许多决策是通过供应商的商业模式来实现。微服务更喜欢让每个服务管理自己的数据库，可以是同一数据库技术的不同实例，也可以是完全不同的数据库系统——这种方法称为["Polyglot Persistence"](https://martinfowler.com/bliki/PolyglotPersistence.html)。你可以在整体中使用多语言持久性，但它在微服务中更常出现。
![](https://martinfowler.com/articles/microservices/images/decentralised-data.png)

跨服务分散数据责任对管理更新有影响。处理更新的常用方法是在更新多个资源时使用事务来保证一致性。这种方法通常用于整体结构中。

使用这样的事务有助于保持一致性，但会产生显著的时间耦合，在多个服务中是有问题。众所周知，分布式事务很难实现，因此微服务架构[强调服务之间的无事务协调](http://www.eaipatterns.com/ramblings/18_starbucks.html)，明确承认一致性可能只有最终的一致性，而问题则通过补偿操作来处理。

选择以这种方式管理不一致是许多开发团队面临的新挑战，但它通常与业务实践相匹配。企业通常会处理一定程度的不一致，以便快速响应需求，同时采取某种逆转流程来应对错误。只要修复错误的成本低于在更大的一致性下丢失业务的成本，那么权衡是值得的。

### 基建设施自动化

基础设施自动糊技术在过去几年中发生了巨大变化——特别是云和 AWS 的发展降低了构建，部署和运行微服务的操作复杂性。

许多使用微服务构建的产品或系统都是由具有丰富的[持续交付(Continuous Delivery)](https://martinfowler.com/bliki/ContinuousDelivery.html)经验的团队构建的，并且是前身的[持续集成(Continuous Integration)](https://martinfowler.com/articles/continuousIntegration.html)。以这种方式构建软件的团队广泛使用基础设施自动化技术。这在下面显示的构建管道中说明

![basic build pipeline](https://martinfowler.com/articles/microservices/images/basic-pipeline.png)

由于这不是关于持续交付的文章，我们将在这里引起注意几个关键功能。我们希望尽可能地信心使我们的软件正常工作，因此我们进行了大量的**自动化测试**。推广工作软件“向上”管道意味着我们**自动化部署**到每个新环境。

>做正取的事情很容易
我们发现由于持续交付和部署而增加自动化的一个副作用是创建有用的工具来帮助开发人员和操作人员。用于创建人工制品，管理代码库，提供简单服务或添加标准监视器和日志记录的工具现在非常普遍。网上最好的例子可能是 [Netflix 的开源工具集](https://netflix.github.io/)，但还有其他一些，包括我们官方使用的 [Dropwizard](http://dropwizard.codahale.com/)

一个单一的应用程序将非常愉快地构建，测试和推动通过这些环境。事实证明，一旦你投资自动化整个生产的生产之路，那么部署更多地应用程序视乎不再那么可怕。请记住，CD的目标之一就是使用部署无聊，所以无论是一个还是多个应用，只要它任然无聊就无聊无所谓[^12] 

我们看到团队使用广泛的基础设施自动化的另一个领域是管理生产中的微服务。与我们上面的断言相反，只要部署很无聊，单块和微服务之间没有太大的区别，每个部署的运营环境可能会截然不同
![Module deployment often differs](https://martinfowler.com/articles/microservices/images/micro-deployment.png)

### 容错设计
使用服务作为组件的结果是，应用需要设计以便他们能够容忍服务的失败。由于提供者不可用（不可达）等，任何服务调用都可能失败，客户端必须尽可能优雅地对此作出响应。与单体设计相比，这是一个缺点，因为它引入了额外的复杂性来处理它。结果是微服务团队持续不断反思服务失败如何影响用户体验。Netflix 的 Simian Army 在工作日引发服务甚至数据中心的故障，以测试应用程序的弹性和监控。

>断路器和“可随时上线的代码”
断路器一词与其他一些模式一起出现发布，如 Bulkhead 和 Timeout。在构建彼此通信的应用系统时，将这些模式加以综合运用变得至关重要。[Netflix 博客](http://techblog.netflix.com/2012/02/fault-tolerance-in-high-volume.html)这篇文章很好的解释了这些模式如何应用。

这种在生产环境中所进行自动化测试，足以让大多数运维组织兴奋地浑身颤栗，就像在一周的长假即将到来前那样。这并不是说单体式架构风格不具备复杂的监控设置——在我们的经验中，这在单体系统中并不常见罢了。

由于服务可能随时发生故障，因此能够快速检测故障并在可能情况下自动恢复服务非常重要。微服务应用程序非常重视应用程序的实时监控，检查“架构元素指标”（数据库每秒获得多少请求）和“业务相关指标”（例如每分钟收到多少订单）。当系统某个地方出现问题，语义监控可以提供一个预警，从而触发开发团队跟进和调查工作。

这对于微服务架构尤为重要，因为微服务对编排和事件协作的偏好会导致紧急行为。虽然许多权威人士赞扬偶然出现的价值，但事实是，新兴行为有时可能是一件坏事。监控对于快速发现下不良紧急行为至关重要，因此可以修复。

>“同步调用”有害
每当您在服务之间进行多次同步调用时，您将遇到停机的乘法效应。简而言之，就是系统停机时间成为各个组件停机时间的产物。您面临一个选择，使您的呼叫异步或管理停机时间。在 `www.guardian.co.uk`网站上，他们在新平台上实施了一条简单的规则——每个用户请求一次同步调用，而在 Netflix，他们的平台 API 重新设计已经在 API 结构中建立了异步性。

monoliths 可以像微服务一样透明——事实上，他们应该是。不同之处在于您绝对需要知道在不同进程中运行的服务何时断开连接。对于同一过程中的库，这种透明性不太可能有用。

微服务团队希望看到针对每个服务的复杂监控和日志记录设置，例如显示上/下状态的仪表板以及各种运营和业务相关指标。有关断路器状态，当前吞吐量和延迟的详细信息是我们经常遇到的其他示例。

### “演进式”设计

微服务从业者通常拥有“演进式”设计背景，而且通常将服务分解视为额外的工具，使应用程序开发人员能够控制应用程序中的更改而不会减慢变更。变更控制并不一定意味着改变——通过正确的态度和工具，你可以对软件进行频繁，快速和良好控制的变更。

每当你尝试将软件系统分解为组件时，就面临着如何进行划分各个部分的决定——我们决定将应用程序切分的遵循的原则是什么？组件的关键属性是独立替换和可升级性[^13] 的特点——这意味着需要寻找这些点，即想象在不影响其合作者的情况下重写组件。事实上，许多微服务团队通过明确预期服务将来会废弃，而不是守着这些服务做长期的演进。

Guardian 网站是一个设计和构建为单体的应用程序的一个很好例子，然而它已经开始向在微服务方向进行演进了。原先的单体系统依然是网站的核心，但在添加新特性时他们愿意以构建一些微服务的方式来进行添加，而这些微服务会去调用原先那个单体系统的 API。这种方法对于本质上是临时的功能尤其方便，例如报道体育赛事的专用页面。当使用快速开发语言时，像这样的网站就能被快速整合在一起，并在时间结束后删除。我们在金融机构看到了类似的做法，针对一个市场机会，添加新服务进来，并在几个月甚至几周后丢弃。

这种对可替换性的强调是模块化设计一般性原则的一个特例，即通过“演进式”模式推动模块化实现。大家都愿意将那些在同时发生变化[^14] 的东西，放到同一个模块中。很少变化的部分，应该放在不同的服务中，以区别那些当前正在经历大量变动的部分。如果您发现需要同时反复变更的两个服务时，那就表明他们应该合并。

将组件放入服务中可以为更细粒度的发布计划添加机会。对于单体应用，任何更改都需要完整构建和部署整个应用程序。但是对使用微服务，您只需要重新部署您修改的服务。这可以简化并加快发布过程。缺点是：必须考虑当一个服务发生变化时，依赖它并对其进行消费的其他服务将无法工作。传统的集成方法是尝试使用版本控制来解决这个问题，但微服务领域中，大家更喜欢使用版本控制作为[最后不得已的手段](https://martinfowler.com/articles/enterpriseREST.html#versioning)。我们可以通过将服务设计为对提供者变更，尽量能够容错来避免大量版本控制

## 未来的方向是“微服务”吗？

我们写这篇文章的主要目的是解释微服务的主要思想和原则。通过花时间来做到这一点，我们清楚地认为微服务架构风格是一个重要的想法——值得认真考虑企业应用程序。我们最近使用这种方式构建了几个系统，并了解其他团队已经使用并支持这种方法。

我们了解到那些在某种程度上做为这种架构风格的实践先驱包括：亚马逊，Netflix，[Guardian](https://www.theguardian.com/) 和 [UK Government Digital Service](https://gds.blog.gov.uk/)，[realeastate.com.au](https://martinfowler.com/articles/realestate.com.au)，[comparethemarket.com](http://www.comparethemarket.com/)。2013 年的技术大会圈子充满了各种各样的，正在转向可以归类为微服务的公司——包括 Travis CI。此外，有很多组织长期以来一直在做我们称为微服务的东西，但没有使用过这个名字（通常被标记为 SOA——尽管如我们所说，SOA 有许多互相矛盾的形式[^15] ）

然而，尽管有这些积极的经验，但我们并不认为我们确信微服务是软件架构的未来发展方向。虽然到目前为止我们的经验与单体应用相比是积极的，但我们意识到没有足够的时间让我们做出充分的判断。

![](https://martinfowler.com/articles/microservices/images/sam-book.jpg)
>我们的同事 Sam Newman 在 2014 年的大部分时间都在撰写一本书，该书描述了我们构建微服务的经验。如果想进一步了解该主题，这应该是您的下一步

通常，您的架构决策的真正后果只有在开发它几年后才会明显。我们已经由带着强烈模块化愿望的优秀团队所做的一些项目，最终却构建出一个单体架构，并在几年内不断腐化。许多人认为微服务不太可能出现这种衰退，因为服务边界是明确的，很难随意捣乱。然而，对于那些开发时间足够长的各种系统，除非我们已经见识的足够多，否则我们无法真正评估微服务架构是如何成熟的。

人们可能会期望微服务成熟得很好。在组件化的任何努力中，成功取决于在组件中的适用程度。很难弄清楚组件边界的确切位置。“演进式”设计承认难以对边界进行正确定位，因此它将工作的重点放到了易于对边界进行重构之上。但是当您的组件是具有远程通信的服务时。那么重构比适用进程内库要困难的多。跨服务边界移动代码很困难，任何接口更改都需要在参与者之间协调，需要添加向后兼容性，测试变得更加复杂。

另一个问题是，如果组件没有干净利落地组成一个系统，那么您所做的就是将复杂性从组件内部转移到组件之间的连接。这样做的后果，不仅仅是移动复杂性，而是将其移动到一个不那么明确且难以控制的地方。当你在一个小而简单的组件内部查看时，人们很容易认为事情已经变得更好了，然而却忽略了服务之间的杂乱连接

最后，还有团队技能的因素。新技术往往被技术更加过硬的团队所采用。对于技术更加过硬的团队更更有效的一项技术，并不一定适用于技术略逊一筹的团队。我们已经看到很多不太熟练的团队构建混乱的单体架构，当微服务发生这种混乱时，会出现什么情况？这需要花时间来观察。一个糟糕的团队，总是会创建一个糟糕的系统——很难说微服务是减少了杂乱，还是让事情变得更糟。

我们听到一个合理的说法，不应该一上来就以微服务架构作为起点。相反，从[单体应用开始](https://martinfowler.com/bliki/MonolithFirst.html)，保持模块化。当单体系统出现问题时将其拆分为微服务。（虽然[这个建议并不理想](https://martinfowler.com/articles/dont-start-monolith.html)，但是好的进程内接口通常不是一个好的服务接口）

因此，我们谨慎乐观地写下这一点。到目前为止，我们已经看到了足够多的微服务风格，觉得它是[一条值得走的路](https://martinfowler.com/microservices/)。我们无法确定最终会在哪里结束，但软件开发的挑战之一是您只能根据您当前必须提供的不完善信息作出决策。

## 参考

虽然这不是一个详尽的列表，但是它们是微服务从业者可以从中吸取灵感来源，或者是那些倡导的理念与本所述内容详实的一些资料

博客和在线文章
* [Clemens Vasters’ blog on cloud at microsoft](http://blogs.msdn.com/b/clemensv/)
* [David Morgantini’s introduction to the topic on his blog](http://davidmorgantini.blogspot.com/2013/08/micro-services-introduction.htm)
* [12 factor apps from Heroku](http://12factor.net/)
* [UK Government Digital Service design principles](https://www.gov.uk/design-principles)
* [Jimmy Nilsson’s blog](http://jimmynilsson.com/blog/) and [article on infoq about Cloud Chunk Computing](http://www.infoq.com/articles/CCC-Jimmy-Nilsson)
* [Alistair Cockburn on Hexagonal architectures](http://alistair.cockburn.us/Hexagonal+architecture)

书籍
* [Release it](https://www.amazon.com/gp/product/0978739213?ie=UTF8&tag=martinfowlerc-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=0978739213)
* [Rest in practice](https://www.amazon.com/gp/product/0596805829?ie=UTF8&tag=martinfowlerc-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=0596805829)
* [Web API Design (free ebook)](https://pages.apigee.com/web-api-design-ebook.html). Brian Mulloy, Apigee.
* [Enterprise Integration Patterns](https://www.amazon.com/gp/product/0321200683?ie=UTF8&tag=martinfowlerc-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=0321200683)
* [Art of unix programming](https://www.amazon.com/gp/product/0131429019?ie=UTF8&tag=martinfowlerc-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=0131429019)
* [Growing Object Oriented Software, Guided by Tests](https://www.amazon.com/gp/product/0321503627?ie=UTF8&tag=martinfowlerc-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=0321503627)
* [The Modern Firm: Organizational Design for Performance and Growth](https://www.amazon.com/gp/product/0198293755?ie=UTF8&tag=martinfowlerc-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=0198293755)
* [Continuous Delivery: Reliable Software Releases through Build, Test, and Deployment Automation](https://www.amazon.com/gp/product/0321601912?ie=UTF8&tag=martinfowlerc-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=0321601912)
* [Domain-Driven Design: Tackling Complexity in the Heart of Software](https://www.amazon.com/gp/product/0321125215?ie=UTF8&tag=martinfowlerc-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=0321125215)

简报
* [Architecture without Architects](https://www.youtube.com/watch?v=qVyt3qQ_7TA). Erik Doernenburg
* [Does my bus look big in this?](http://www.infoq.com/presentations/soa-without-esb). Jim Webber and Martin Fowler, QCon 2008
* [Guerilla SOA](http://www.infoq.com/presentations/webber-guerilla-soa). Jim Webber, 2006
* [Patterns of Effective Delivery](http://vimeo.com/43659070).Daniel Terhorst-North, 2011.
* [Adrian Cockcroft's slideshare channel.](http://www.slideshare.net/adrianco)
* [Hydras and Hypermedia](http://vimeo.com/28608667). Ian Robinson, JavaZone 2010
* Justice will take a million intricate moves Leonard Richardson, Qcon 2008.
* [Java, the UNIX way](http://vimeo.com/74452550). James Lewis, JavaZone 2012
* [Micro services architecture](http://yow.eventer.com/yow-2012-1012/micro-services-architecture-by-fred-george-1286). Fred George, YOW! 2012
* [Democratising attention data at guardian.co.uk](http://gotocon.com/video#18). Graham Tackley, GOTO Aarhus 2013
* [Functional Reactive Programming with RxJava](http://gotocon.com/video#6). Ben Christensen, GOTO Aarhus 2013 (registration required).
* [Breaking the Monolith](http://www.infoq.com/presentations/Breaking-the-Monolith). Stefan Tilkov, May 2012.

论文
* L. Lamport,[“The Implementation of Reliable Distributed Multiprocess Systems”](http://research.microsoft.com/en-us/um/people/lamport/pubs/implementation.pdf), 1978
* L. Lamport, R. Shostak, M. Pease,["The Byzantine Generals Problem"](http://www.cs.cornell.edu/courses/cs614/2004sp/papers/lsp82.pdf), 1982 
* R.T. Fielding, ["Architectural Styles and the Design of Network-based Software Architectures"](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm), 2000
* E. A. Brewer, ["Towards Robust Distributed Systems"](http://www.cs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf), 2000
* E. Brewer, ["CAP Twelve Years Later: How the 'Rules' Have Changed"](http://www.infoq.com/articles/cap-twelve-years-later-how-the-rules-have-changed), 2012

## 总结

单体应用与微服务比较

### 单体应用

#### 特性
* 调用方便，都是在一个进程内进行调用（针对于 Java 来说，就是运行在一个 JVM 上的应用）
* 部署方式简单，
* 事务处理方式可以容易处理
* 由于都在一个进程内API 调用，不涉及网络的访问，因此出错的可能性要低很多

#### 优缺点

* 优点
    * 为人所熟知
    * 便于共享
    * 易于测试
    * 容易部署
* 缺点
    * 复杂性逐渐变高
    * 技术债务逐渐上升
    * 部署速度逐渐变慢
    * 阻碍技术创新
    * **无法按需伸缩**

### 微服务

#### 特性

* 每个微服务可独立运行在自己的进程里
* 一系列独立运行的微服务共同构建起了这个系统
* 每个服务为独立的业务开发，一个微服务一般玩某个特定的功能，比如：订单管理，用户管理等
* 微服务之间通过一些轻量的通讯机制进行通信，比如通过 REST API 或者 RPC 的方式调用

#### 优缺点

* 优点
    * 易于开发和维护
    * 启动较快
    * 局部修改容易部署
    * 技术栈不受限
    * 按需伸缩
    * DevOps
* 缺点
    * 运维复杂
    * 数据一致性问题
    * 集成测试复杂
    * **重复代码**
    * 监控困难
* 挑战
    * **运维要求较高**
    * 分布式的复杂性
    * 接口调整成本高
    * 重复你劳动

#### 设计原则
* 单一职责原则
* 服务自治原则
* 轻量级通信原则
* 接口明确原则

## 附录
* [Microservices](https://martinfowler.com/articles/microservices.html)
* 校验 • [CeaserWang](https://github.com/1156721874)

[^1]: The term "microservice" was discussed at a workshop of software architects near Venice in May, 2011 to describe what the participants saw as a common architectural style that many of them had been recently exploring. In May 2012, the same group decided on "microservices" as the most appropriate name. James presented some of these ideas as a case study in March 2012 at 33rd Degree in Krakow in [Microservices - Java, the Unix Way](http://2012.33degree.org/talk/show/67) as did Fred George [about the same time](https://www.slideshare.net/fredgeorge/micro-service-architecure). Adrian Cockcroft at Netflix, describing this approach as "fine grained SOA" was pioneering the style at web scale as were many of the others mentioned in this article - Joe Walnes, Daniel Terhorst-North, Evan Botcher and Graham Tackley.
[^2]: The term monolith has been in use by the Unix community for some time. It appears in [The Art of Unix Programming](https://www.amazon.com/gp/product/B003U2T5BA?ie=UTF8&tag=martinfowlerc-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=B003U2T5BA) to describe systems that get too big.
[^3]: Many object-oriented designers, including ourselves, use the term service object in the [Domain-Driven Design](https://www.amazon.com/gp/product/0321125215?ie=UTF8&tag=martinfowlerc-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=0321125215) sense for an object that carries out a significant process that isn't tied to an entity. This is a different concept to how we're using "service" in this article. Sadly the term service has both meanings and we have to live with the polyseme.
[^4]: We consider [an application to be a social construction](https://martinfowler.com/bliki/ApplicationBoundary.html) that binds together a code base, group of functionality, and body of funding.
[^5]: The original paper can be found on Melvyn Conway's website [here](http://www.melconway.com/Home/Committees_Paper.html).
[^6]: We can't resist mentioning Jim Webber's statement that ESB stands for ["Egregious Spaghetti Box"](http://www.infoq.com/presentations/soa-without-esb).
[^7]: Netflix makes the link explicit - until recently referring to their architectural style as fine-grained SOA.
[^8]: At extremes of scale, organisations often move to binary protocols - [protobufs](https://code.google.com/p/protobuf/) for example. Systems using these still exhibit the characteristic of smart endpoints, dumb pipes - and trade off transparency for scale. Most web properties and certainly the vast majority of enterprises don't need to make this tradeoff - transparency can be a big win.
[^9]: "YAGNI" or "You Aren't Going To Need It" is an [XP principle](http://c2.com/cgi/wiki?YouArentGonnaNeedIt) and exhortation to not add features until you know you need them.
[^10]: It's a little disengenuous of us to claim that monoliths are single language - in order to build systems on todays web, you probably need to know JavaScript and XHTML, CSS, your server side language of choice, SQL and an ORM dialect. Hardly single language, but you know what we mean.
[^11]: Adrian Cockcroft specifically mentions "developer self-service" and "Developers run what they wrote"(sic) in [this excellent presentation](http://www.slideshare.net/adrianco/flowcon-added-to-for-cmg-keynote-talk-on-how-speed-wins-and-how-netflix-is-doing-continuous-delivery) delivered at Flowcon in November, 2013.
[^12]: We are being a little disengenuous here. Obviously deploying more services, in more complex topologies is more difficult than deploying a single monolith. Fortunately, patterns reduce this complexity - investment in tooling is still a must though.
[^13]: In fact, Daniel Terhorst-North refers to this style as Replaceable Component Architecture rather than microservices. Since this seems to talk to a subset of the characteristics we prefer the latter.
[^14]: Kent Beck highlights this as one his design principles in [Implementation Patterns](https://www.amazon.com/gp/product/0321413091?ie=UTF8&tag=martinfowlerc-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=0321413091).
[^15]: And SOA is hardly the root of this history. I remember people saying "we've been doing this for years" when the SOA term appeared at the beginning of the century. One argument was that this style sees its roots as the way COBOL programs communicated via data files in the earliest days of enterprise computing. In another direction, one could argue that microservices are the same thing as the Erlang programming model, but applied to an enterprise application context.