---
title: 【译】• 面向服务的架构
date: 2019-06-19 21:20:40
categories: Translation
tag: [SOA,SpringBoot]
---

在学习过程中，我们首先需要将学习知识的基本概念搞清楚，而搞清楚概念最权威的方式是查阅 **[英文版 • 维基百科](https://en.wikipedia.org)** ，或者是对应知识的官方文档上面查找相关的知识。这样学习才能学习到知识的精华，而不是阅读经过别人转译过的文章。因此，这篇文章仅是本人在学习 SOA 基本概念时，对维基百科知识的一个汇总翻译记录，<font color=#FF0000>不建议朋友把这篇文章当做你的学习资料</font>，具体请查阅[Service-oriented architecture](https://en.wikipedia.org/wiki/Service-oriented_architecture)。

面向服务的架构（SOA）是一种[软件设计](https://en.wikipedia.org/wiki/Software_design)风格。 SOA 服务通过[应用组件](https://en.wikipedia.org/wiki/Application_components)，通过网络[通信协议](https://en.wikipedia.org/wiki/Communications_protocol)的方式向其他组件提供服务。SOA 的基本原则是独立于厂商，独立于产品以及独立于技术[^1]。服务是一种功能的离散独立单元，可以远程访问并独立运行与更新，例如在线查询信用卡账单。

一个服务在诸多 SOA 定义中有 4 个属性[^2]：
1. 它逻辑上代表具有指定结果的业务活动
2. 它是自包含的
3. 它对于消费者来说是[黑盒](https://en.wikipedia.org/wiki/Black_box)（不可见）
4. 它可能包含其他基础服务[^3]

不同的服务可以联合起来构建大型的[软件应用](https://en.wikipedia.org/wiki/Software_applications)[^4]，SOA 遵循模块化[编程思想](https://en.wikipedia.org/wiki/Modular_programming)，SOA 集成了分布式，独立维护和独立部署的软件组件，它通过技术和标准促使组件通过网络进行通信和协作，尤其是通过 IP 网络

## 概览

在 SOA 中，服务的使用是描述怎样通过[元数据](https://en.wikipedia.org/wiki/Metadata)[传递消息](https://en.wikipedia.org/wiki/Message_passing)和解析消息的协议。该元数据描述了服务的功能特性和服务质量特征。SOA 目标旨在允许用户将大块的功能组合在一起来去构成一个应用，形成纯粹由现有服务构建并以临时方式组合的应用程序。一个服务会向调用者提供一个简单的接口，它抽象出作为黑盒子的底层复杂性。其他用户可以在不了解其内部实现的情况下访问这些独立服务[^5]。

## 定义概念

相关的流行语服务导向促进了[面向服务](https://en.wikipedia.org/wiki/Service-orientation)间的[松耦合](https://en.wikipedia.org/wiki/Loose_coupling)。SOA 将功能分为不同的单元或服务，哪些开发人员可以通过网络访问，以便允许用户在应用程序的生产中组合和重用它们。这些服务及其相应的消费者通过明确定义的共享格式，传递数据或通过协调两个或更多服务之间的活动来互相通信

2009 年 10 月发布了关于 SOA 的宣言，其中提出了 6 个核心价值，如下所示
1. **商业价值**比技术战略更重要
2. **战略目标**比项目特定的利益更重要
3. **内在的互操作**性比定制集成更重要
4. **共享服务**比特定用途实现更重要
5. **灵活性**比优化更重要
6. **演进式**比追求初始化完美更重要

SOA 可看作是连续统一体的一部分，其范围从旧的[分布式计算](https://en.wikipedia.org/wiki/Distributed_computing)概念和[模块化编程](https://en.wikipedia.org/wiki/Modular_programming)[^6] [^9]，通过 SOA，以及 [mashups](https://en.wikipedia.org/wiki/Mashup_(web_application_hybrid))，[SaaS](https://en.wikipedia.org/wiki/SaaS) 和[云计算](https://en.wikipedia.org/wiki/Cloud_computing)的当前实践（有些人认为是 SOA 的后代）[^10]

## 原理

尽管许多行业已发布了自己的原则，但没有与 SOA 确切相关的行业标准，其中一些[^11] [^12] [^13] [^14]包括以下内容

### [标准地服务契约](https://en.wikipedia.org/wiki/Standardized_service_contract)

服务遵循标准通信协议，有一组给定服务中的一个或多个服务描述文档共同定义

### [服务自治](https://en.wikipedia.org/w/index.php?title=Service_reference_autonomy&action=edit&redlink=1)

服务之间的关系被最小化到它们只知道存在的等级

### [服务松耦合](https://en.wikipedia.org/w/index.php?title=Service_location_transparency&action=edit&redlink=1)

无论网络位于何处，都可以从网络中的任何位置调用服务

### [服务长寿](https://en.wikipedia.org/w/index.php?title=Service_longevity&action=edit&redlink=1)

服务应该设计为长寿，在可能的情况下，如果不需要新功能，服务应该避免强迫消费者进行更改。如果今天能调用的服务，到明天也应该能调用统一的服务

### [服务抽象化](https://en.wikipedia.org/wiki/Service_abstraction)

服务充当黑盒，它们内在的逻辑对消费者是隐藏的

### [服务自治](https://en.wikipedia.org/wiki/Service_autonomy_principle)

服务是独立的，从设计时和运行时的角度控制它们封装的功能

### [服务无状态化](https://en.wikipedia.org/wiki/Service_statelessness_principle)

服务是无状态的，即返回请求的值或提供异常，从而最大限度的减少资源使用

### [服务粒度](https://en.wikipedia.org/wiki/Service_granularity_principle)

确保服务具有足够的规模和范围的原则。服务向用户提供的功能必修三相关的

### 服务规范化

服务被分解或合并作为最小化冗余。在某些情况下，可能无法完成，这些是需要性能优化，访问和聚合的情况[^15]

### [服务可组合性](https://en.wikipedia.org/wiki/Service_composability_principle)

服务可用于组成其他服务

### [服务发现](https://en.wikipedia.org/wiki/Service_discovery)

服务补充了交流元数据，通过它可以有效地发现和解释它们

### [服务可重用性](https://en.wikipedia.org/wiki/Service_reusability_principle)

将逻辑分为多个服务，以促进代码的复用

### 服务[封装](https://en.wikipedia.org/wiki/Encapsulation_(computer_science))

许多最初未在 SOA 下计划的服务可能会被封装或成为 SOA 的一部分

## 模式

每个SOA构建块都可以扮演以下三种角色中的任何一种：

### 服务提供者

它创建 Web 服务并将其信息提供给服务注册。每个提供者都会讨论大量的方法，以及为什么要公开哪些服务，哪些更重要：安全性或易用性，提供服务的价格等等。提供者还必须决定应该为给定的代理服务列出服务的类别[^16]以及使用该服务需要那种协议

### 服务代理，服务注册或服务存储

其主要功能是使用任何潜在的请求者都能获取有关 Web 服务的信息。实施的人决定代理的范围。公开的代理随处可见，但私有的代理只能向有限的公开代理开放。UDDI 是一种早期的，不再主动支持的 [Web 服务发现](https://en.wikipedia.org/wiki/Web_Services_Discovery)。

### 服务消费者

它使用各种查找操作在代理注册中查找，然后绑定到服务提供者以调用其中一个 Web 服务。无论服务消费者需要哪种服务，它们都必须通过代理，将其与相应的服务绑定，然后使用它。如果服务提供多种服务，它们可以访问多种服务。

服务消费者-提供者关系由[标准化服务契约](https://en.wikipedia.org/wiki/Standardized_service_contract)[^17]管理，其中包括业务部分，功能部分和技术部分。

[服务组合模式](https://en.wikipedia.org/wiki/Service_composability_principle)有两种广泛的高级架构风格：[服务编排](https://en.wikipedia.org/wiki/Service_choreography#Service_choreography_and_service_orchestration)。不受特定体系结构风格约束的较低级别的企业集成模式在 SOA 设计中任然具有相关性和合格性[^18] [^19] [^20]。

## 实现方法

SOA 可以通过 [Web 服务](https://en.wikipedia.org/wiki/Web_service)[^21]实现。这样做是为了使功能模块可以通过独立于平台和编程语言的标准协议访问。这些服务既可以代表新应用程序，也可以代表现有遗留系统的包装，使其具备网络功能。[^22]

实现通用使用 Web 服务标准构建 SOA。一个例子是 [SOAP](https://en.wikipedia.org/wiki/SOAP)，它在 2003 年从 W3C[^23] 推荐 1.2 版本后获得广泛的行业认可。这些标准（也称为 [Web 服务规范](https://en.wikipedia.org/wiki/List_of_web_service_specifications)）还提供了更强的互操作性以及对锁定到专有供应商软件的一些保护。但是，也可以使用任何其他基于服务的技术（如 [Jini](https://en.wikipedia.org/wiki/Jini)，[CORBA](https://en.wikipedia.org/wiki/CORBA) 或者 [REST](https://en.wikipedia.org/wiki/Representational_State_Transfer)）实现 SOA

架构可以独立于特定技术运行，因此可以使用多种技术实现，包括：
* 基于 WSDL 和 [SOAP](https://en.wikipedia.org/wiki/SOAP) 的 [Web 服务](https://en.wikipedia.org/wiki/Web_services)
* 消息传递，例如，使用 ActiveMQ, JMS, RabbitMQ
* RESTful HTTP，具有 [Representational 状态转移](https://en.wikipedia.org/wiki/Representational_state_transfer)（REST），构成自己的基于约束的架构风格
* [OPC-UA](https://en.wikipedia.org/wiki/OPC_Unified_Architecture)
* [WCF](https://en.wikipedia.org/wiki/Windows_Communication_Foundation)（Microsoft 的 Web 服务实现，构成 WCF 的一部分）
* [Apache Thrift](https://en.wikipedia.org/wiki/Apache_Thrift)
* [gRPC](https://en.wikipedia.org/wiki/GRPC)
* [SORCER](https://en.wikipedia.org/wiki/SORCER)

实现可以使用这些协议中的一个或多个，例如，可以使用文件系统机制来遵循符合 SOA 概念的进程间定义的接口规范来传递数据。关键是具有已定义接口的独立服务，可以调用它们以标准方式执行其任务，而无需预先知道调用应用程序的服务，并且没有应用程序具有或需要知道服务如何实际执行其任务。SOA 支持开发通过松耦合和可[互操作](https://en.wikipedia.org/wiki/Interoperable)的服务构建的应用程序

这些服务独立于底层平台和编程语言的正式定义（或契约，例如 WSDL）进行互操作。接口定义[隐藏了实施](https://en.wikipedia.org/wiki/Information_hiding)特定语言的服务实现。因此，基于 SOA 的系统可以独立于开发技术和平台（例如Java，.NET等）运行。例如，运行在.NET平台上的 C# 和 用 [JavaEE](https://en.wikipedia.org/wiki/Java_Platform,_Enterprise_Edition) 平台上运行的 Java 编写的服务都可以公共复合应用程序（或客户端）使用。在任一平台上运行的应用程序也可以使用在另一个平台上运行的服务作为重用的 Web 服务。托管环境还可以包括 COBOL 遗留系统并将其作为软件服务提供。[^24]

诸如 [BPEL](https://en.wikipedia.org/wiki/BPEL) 之类的[高级编程语言](https://en.wikipedia.org/wiki/High-level_programming_language)以及诸如 [WS-CDL](https://en.wikipedia.org/wiki/WS-CDL) 和 [WS-Coordination](https://en.wikipedia.org/wiki/WS-Coordination) 之类的规范通过提供一种定义和支持将细粒度服务编排成更粗粒度的业务服务的方法来扩展服务的概念，架构师可以将其合并到[复合应用](https://en.wikipedia.org/wiki/Composite_applications)程序或[门户](https://en.wikipedia.org/wiki/Enterprise_portal)中实现的工作流和业务流中。

[面向服务的建模](https://en.wikipedia.org/wiki/Service-oriented_modeling)是一个 SOA 架构，可识别指导 SOA 从业者对其面向服务的资产进行概念化，分析，设计和构建的各种规程。[面向服务的建模框架（SOMF）](https://en.wikipedia.org/wiki/Service-oriented_modeling#Service-oriented_modeling_framework)提供了一种建模语言和一个工作架构映射，描述了有助于成功的面向服务的建模方法的各种组件。它说明了识别服务开发方案的“做什么”方面的主要元素。该模型使从业者能够制定[项目计划](https://en.wikipedia.org/wiki/Project_plan)并确定面向服务的计划的里程碑。SOMF 还提供了一种通用的建模符号，已解决业务和 IT 组织之间的一致性问题。

## 组织利益

一些[企业架构师](https://en.wikipedia.org/wiki/Enterprise_architect)认为，SOA 可以帮助企业更快，更经济地响应不断变化的市场条件。[^26]这种体系结构促进了宏（服务）级别的重用，而不是微（类）级别的重用。它还可以简化现有 IT（传统）资产的互联和使用。

![SOA的元素，由Dirk Krafzig，Karl Banke和Dirk Slama撰写 [^25]](https://res.cloudinary.com/incoder/image/upload/v1561944855/blog/elements_of_soa.png)

使用SOA，我们的想法是组织可以从整体上看待问题。企业拥有更多地整体控制权。从理论上讲，不会有大量的开发人员使用任何工具集让他们满意，但他们将编码为业务中设定标准。他们还可以开发企业级 SOA，封装面向业务的基础架构。SOA 被描述为汽车驾驶员提供效率的高速公路系统。关键在于，如果每个人都有车，但在任何地方都没有高速公路，那么事情就会受到限制和混乱，无论是视图快速或有效地到达任何地方。IBM Web 服务副总裁 Michael Liebow 表示 SOA 是“建设的高速公路”。[^27]

![SOA元模型，The Linthicum Group，2007](https://res.cloudinary.com/incoder/image/upload/v1561944919/blog/soa_meta-model.svg)

<font color=#FF0000>在某些方面，SOA可以被视为架构演变而不是革命</font>。它捕获了以前软件架构的许多[最佳实践](https://en.wikipedia.org/wiki/Best_practice)。例如，在通信系统中，很少开发使用真正静态绑定与网络中的其他设备通信的解决方案。通过采用 SOA 方法，此类系统可以将自己定位为强调定义明确，高度可互操作的接口的重要性。SOA 的其他前身包括[基于组件的软件工程](https://en.wikipedia.org/wiki/Component-based_software_engineering)和远程对象的面向对象分析和设计（OOAD），例如，在 [CORBA](https://en.wikipedia.org/wiki/CORBA) 中。

服务包括仅通过正式定义的页面可用的独立功能单元。服务可以是某种易于生产和改进的“纳米企业”。服务也可以是作为子下属服务的协调工作而构建的“大型企业”。SOA 的成熟部署有效地定义了组织的 API。

将服务实施视为大型项目的单独项目的原因包括：
1. 分离将业务概念推广到业务，即服务可以快速独立地从组织中常见的较大且移动较慢的项目中提供。业务开始了解回调服务的系统和简化的用户界面。这提倡[敏捷](https://en.wikipedia.org/wiki/Agility)。也就是说，它醋精了业务创新并加快了产品上市时间[^28]。
2. 分离促进了服务于消费项目的脱钩。这样可以鼓励良好的设计，因为服务的设计不需要知道消费者是谁。
3. 服务的文档和测试文件未嵌入较大项目的详细信息中。当服务需要在后续需要重用时，这很重要。

SOA 承诺间接简化测试。服务是自治的，无状态的，具有完全记录的接口，并且与实现的关注点是分开的。如果组织拥有适当定义的测试数据，则会构建响应的存根，以便在构建服务时对测试数据做出反应。可以构建测试环境，其中原始和超出范围的服务是存根，而网格的其余ubuf 是完整服务的测试部署。由于每个接口都有完整的文档，并附有完整的回归测试文档，因此可以轻松识别测试服务中的问题。测试演变为仅仅验证测试服务是否根据其文档运行，并发现环境中所有服务的文档和测试用例存在的差距。管理[幂等](https://en.wikipedia.org/wiki/Agility)服务的数据状态是唯一的复杂性。

实例可能有助于将服务记录到有用的级别。Java community Process 中的一些 API 文档提供了良好的示例。由于这些是详尽无遗漏的，工作人员通常只使用重要的子集。JSR-89 的 ossjsa.pdf 文件中举例说明了这样做一个文件[^29]。

## 批评

SOA 已与 [Web 服务](https://en.wikipedia.org/wiki/Web_service)混淆[^30]，但是，Web 服务只是实现构成 SOA 风格的模式的一种选择。在非本机或二进制形式的远程过程调用（RPC）的情况下，应用程序可能运行的更慢并且需要更多地处理能力，从而增加了成本。大多数实现都会产生这些开销，但 SOA 可以使用技术实现（例如，[Java Business Integration（JBI）](https://en.wikipedia.org/wiki/Java_Business_Integration)，[Windows Communication Foundation（WCF）](https://en.wikipedia.org/wiki/Windows_Communication_Foundation)和 [data distribution service（DDS）](https://en.wikipedia.org/wiki/Data_distribution_service)），它们不依赖与远程过程调用或通过 XML 进行转换。与此同时，新兴的开源 XML 解析技术（如 [VTD-XML](https://en.wikipedia.org/wiki/VTD-XML)）和各种 XML 兼容的二进制格式有望显著提高 SOA 性能。使用 [JSON](https://en.wikipedia.org/wiki/JSON) 而不是 XML 实现的服务不会受到这种性能的问题的影响[^31] [^32] [^33]。

有状态服务要求消费者和提供者共享相同的特定于消费者的上下文，该上下文包含在提供者和消费者之间交换的消息中或由其引入。如果服务提供者需要为每个消费者保留共享上下文，则此约束的缺点是它可能会降低服务提供者的整体可伸缩性。它还增加了服务提供者和消费者之间的耦合，使交换服务提供者更加困难[^34]。最终，一些批评者认为 SOA 服务仍然受到他们所代表的应用程序的限制[^35]。

SOA 的体系结构面临的主要挑战是管理元数据。基于 SOA 的环境包括许多彼此之间进行通信以执行任务的服务。由于设计可能涉及多个服务一起工作，因此应用程序可能会产生数百万条消息。进一步的服务可能属于不同的组织甚至是竞争公司，造成巨大的信任问题。因此 SOA 治理进入了事务的计划[^36]。

SOA 面临的另一个主要问题是缺乏统一的测试架构。没有工具可以提供在 SOA 的体系结构中测试这些服务所需的功能。困难的主要原因是：[^37]
* 异质性和解决方案的复杂性
* 由于自主服务的集成，大量的测试组合
* 包含来自不同和竞争供应商的服务
* 由于新功能和服务的可用性，[平台](https://en.wikipedia.org/wiki/Platform_as_a_service)不断变化

请参阅 drops.dagstuhl.de[^38]，了解有关[软件服务工程](https://en.wikipedia.org/wiki/Service-oriented_modeling)的其他挑战，部分解决方案和研究路线图

## 扩展和变体

### 事件驱动的体系结构

主要文章：[事件驱动的架构](https://en.wikipedia.org/wiki/Event-driven_architecture)

### Web2.0

[Tim O'Reilly](https://en.wikipedia.org/wiki/Tim_O%27Reilly) 创造了“[Web2.0](https://en.wikipedia.org/wiki/Web_2.0)”一词来描述一种快速增长的基于网络的应用程序[^39]。经历了广泛报道的主题涉及 Web2.0与 SOA 体系机构之间的关系。

SOA 是将应用程序逻辑封装在具有统一定义的接口的服务中并通过发现机制公开可用的哲学。复杂性-隐藏和重用的概念，以及松耦合服务的概念，激发了研究人员详细阐述两种哲学，SOA 和 Web2.0及其各自应用之间的相似性。一些人认为 Web2.0和 SOA 具有显著不同的元素，因此不能被视为“平行哲学”，而其他人认为这两个概念是互补的，并将 Web2.0视为全球 SOA[^40]。

Web2.0和 SOA 的理念满足了不同的用户需求，从而暴露了设计方面的差异以及实际应用中使用的技术。但是，截止 2008 年，用例展示了结合 Web2.0和 SOA 技术和原则的潜力[^40]。

### 微服务

主要文章：[微服务](https://en.wikipedia.org/wiki/Microservices)

微服务是对用于构建[分布式软件系统](https://en.wikipedia.org/wiki/Distributed_computing)的面向服务的体系结构的现代解释。微服务架构[^41]中的服务是通过[网络](https://en.wikipedia.org/wiki/Computer_network)互相通信以实现目标的[过程](https://en.wikipedia.org/wiki/Process_(computing))。这些服务使用技术不可知[协议](https://en.wikipedia.org/wiki/Communications_protocol)[^42]，这有助于封装语言和框架的选择，使他们的选择成为服务内部的一个问题。<font color=#FF0000>微服务是 SOA 的一种新的实现方法</font>，自 2014 年（以及 [DevOps](https://en.wikipedia.org/wiki/DevOps) 推出以后）开始流行，并且强调持续部署和其他的=敏捷实践[^43]。

微服务没有一个共同商定的定义。在文献中可以找到以下特征和原理：
* 细粒度接口（可独立部署的服务）
* 业务驱动的开发（例如域驱动设计）
* IDEAL 云应用架构
* 多语言编程和持久化存储
* 轻量级容器部署
* 分散的持续交付
* DevOps 提供全面的服务监控

## 其他

* [松耦合](https://en.wikipedia.org/wiki/Loose_coupling)
* [OASIS SOA 参考模型](https://en.wikipedia.org/wiki/OASIS_SOA_Reference_Model)
* [服务粒度原则](https://en.wikipedia.org/wiki/Service_granularity_principle)
* [SOA 治理](https://en.wikipedia.org/wiki/SOA_governance)
* [软件架构](https://en.wikipedia.org/wiki/Software_architecture)
* [面向服务的通信](https://en.wikipedia.org/wiki/Service-oriented_communications)
* [面向服务的应用程序开发](https://en.wikipedia.org/wiki/Service-oriented_development_of_applications)
* [面向服务的分布式应用程序](https://en.wikipedia.org/wiki/Service-oriented_distributed_applications)

## 参考

[^1]:  "[Chapter 1: Service Oriented Architecture (SOA)](https://web.archive.org/web/20160206132542/https://msdn.microsoft.com/en-us/library/bb833022.aspx)". msdn.microsoft.com. Archived from [the original](https://msdn.microsoft.com/en-us/library/bb833022.aspx) on February 6, 2016. Retrieved September 21, 2016.
[^2]: "[Service-Oriented Architecture Standards - The Open Group](https://publications.opengroup.org/standards/soa)". www.opengroup.org.
[^3]: "[What Is SOA?](https://web.archive.org/web/20160819141303/http://opengroup.org/soa/source-book/soa/soa.htm)". www.opengroup.org. Archived from [the original](http://www.opengroup.org/soa/source-book/soa/soa.htm) on August 19, 2016. Retrieved September 21, 2016.
[^4]: Velte, Anthony T. (2010). Cloud Computing: A Practical Approach. McGraw Hill. [ISBN](https://en.wikipedia.org/wiki/International_Standard_Book_Number) [978-0-07-162694-1](https://en.wikipedia.org/wiki/Special:BookSources/978-0-07-162694-1).
[^5]: "[Migrating to a service-oriented architecture, Part 1](https://web.archive.org/web/20081209120916/http://www-128.ibm.com/developerworks/library/ws-migratesoa/)". December 9, 2008. Archived from the original on December 9, 2008. Retrieved September 21, 2016.
[^6]: Michael Bell (2008). "Introduction to Service-Oriented Modeling". Service-Oriented Modeling: Service Analysis, Design, and Architecture. Wiley & Sons. p. 3. [ISBN](https://en.wikipedia.org/wiki/International_Standard_Book_Number) [978-0-470-14111-3](https://en.wikipedia.org/wiki/Special:BookSources/978-0-470-14111-3).
[^7]: Michael Bell (2010). SOA Modeling Patterns for Service-Oriented Discovery and Analysis. Wiley & Sons. p. 390. [ISBN](https://en.wikipedia.org/wiki/International_Standard_Book_Number) [978-0-470-48197-4](https://en.wikipedia.org/wiki/Special:BookSources/978-0-470-48197-4).
[^8]:  "[SOA Manifesto](http://www.soa-manifesto.org/)". www.soa-manifesto.org. Retrieved September 21, 2016.
[^9]: Thomas Erl (June 2005). About the Principles. Serviceorientation.org
[^10]: "[Application Platform Strategies Blog: SOA is Dead; Long Live Services](http://apsblog.burtongroup.com/2009/01/soa-is-dead-long-live-services.html)". Apsblog.burtongroup.com. January 5, 2009. Retrieved August 13, 2012.
[^11]: Yvonne Balzer [Improve your SOA project plans](http://www-128.ibm.com/developerworks/webservices/library/ws-improvesoa/), IBM, July 16, 2004
[^12]: Microsoft Windows Communication Foundation team (2012). "[Principles of Service Oriented Design](http://msdn.microsoft.com/en-us/library/bb972954.aspx)". msdn.microsoft.com. Retrieved September 3, 2012.
[^13]: Principles by [Thomas](https://en.wikipedia.org/wiki/Thomas_Erl) Erl of SOA Systems Inc. [eight specific service-orientation principles](http://soaprinciples.com)
[^14]:  M. Hadi Valipour; Bavar AmirZafari; Kh. Niki Maleki; Negin Daneshpour (2009). "A brief survey of software architecture concepts and service oriented architecture". 2009 2nd IEEE International Conference on Computer Science and Information Technology. pp. 34–38. [doi:10.1109/ICCSIT.2009.5235004](https://doi.org/10.1109%2FICCSIT.2009.5235004). [ISBN](https://en.wikipedia.org/wiki/International_Standard_Book_Number) [978-1-4244-4519-6](https://en.wikipedia.org/wiki/Special:BookSources/978-1-4244-4519-6).
[^15]: Tony Shan (2004). "Building a service-oriented e Banking platform". IEEE International Conference on Services Computing, 2004. (SCC 2004). Proceedings. 2004. pp. 237–244. [doi:10.1109/SCC.2004.1358011](https://doi.org/10.1109%2FSCC.2004.1358011). [ISBN](https://en.wikipedia.org/wiki/International_Standard_Book_Number) [978-0-7695-2225-8.2004](https://en.wikipedia.org/wiki/Special:BookSources/978-0-7695-2225-8)
[^16]: Duan, Yucong; Narendra, Nanjangud; Du, Wencai; Wang, Yongzhi; Zhou, Nianjun. "[Exploring Cloud Service Brokering from an Interface Perspective](http://ieeexplore.ieee.org/document/6928915/)". [IEEE](https://en.wikipedia.org/wiki/IEEE).
[^17]: Duan, Yucong. "[A Survey on Service Contract](http://ieeexplore.ieee.org/document/6299375/)". [IEEE](https://en.wikipedia.org/wiki/IEEE).
[^18]: Olaf Zimmermann, Cesare Pautasso, Gregor Hohpe, Bobby Woolf (2016). "[A Decade of Enterprise Integration Patterns](http://ieeexplore.ieee.org/document/7368007/)". IEEE Software. 33 (1): 13–19. [doi](https://en.wikipedia.org/wiki/Digital_object_identifier):[10.1109/MS.2016.11](https://doi.org/10.1109%2FMS.2016.11).
[^19]: Rotem-Gal-Oz, Arnon (2012). SOA Patterns. Manning Publications. [ISBN](https://en.wikipedia.org/wiki/International_Standard_Book_Number) [978-1933988269](https://en.wikipedia.org/wiki/Special:BookSources/978-1933988269).
[^20]: K. Julisch et al., [Compliance by Design – Bridging the Chasm between Auditors and IT Architects](http://soadecisions.org/download/ComplianceByDesign-AAM.pdf). Computers & Security, Elsevier. Volume 30, Issue 6-7, Sep.-Oct. 2011.
[^21]:  Brandner, M., Craes, M., Oellermann, F., Zimmermann, O., Web Services-Oriented Architecture in Production in the Finance Industry, Informatik-Spektrum 02/2004, Springer-Verlag, 2004
[^22]: "[www.ibm.com](http://www.ibm.com/support/knowledgecenter/en/SSEQTP_6.1.0/com.ibm.websphere.base.iseries.doc/info/iseries/ae/cwbs_soawbs.html)". Retrieved September 10, 2016.
[^23]: "[SOAP Version 1.2 の公開について (W3C 勧告)](http://www.w3.org/2003/06/soap12-pressrelease)" (in Japanese). W3.org. Retrieved August 13, 2012.
[^24]: Okishima, Haruhiru (2006). ". ["Case Study of System Architecture that use COBOL assets"](http://www.fujitsu.com/global/documents/about/resources/publications/fstj/archives/vol42-3/paper18.pdf)" (PDF).
[^25]: Enterprise SOA. Prentice Hall, 2005
[^26]: Christopher Koch [A New Blueprint For The Enterprise](http://www.cio.com.au/index.php/id;1350140708), CIO Magazine, March 1, 2005
[^27]: Elizabeth Millard (January 2005). "Building a Better Process". Computer User. Page 20.
[^28]: Brayan Zimmerli (November 11, 2009) [Business Benefits of SOA](https://web.archive.org/web/20101105063545/http://www.brayan.com/projects/BenefitsOfSOA/default.htm), University of Applied Science of Northwestern Switzerland, School of Business
[^29]: J[SR-000089 OSS Service Activation API Specification 1.0 Final Release](https://web.archive.org/web/20110726070810/https://cds.sun.com/is-bin/INTERSHOP.enfinity/WFS/CDS-CDS_Developer-Site/en_US/-/USD/ViewProductDetail-Start?ProductRef=7854-oss_service_activation-1.0-fr-spec-oth-JSpec%40CDS-CDS_Developer). sun.com
[^30]: Joe McKendrick. "[Bray: SOA too complex; 'just vendor BS'](http://www.zdnet.com/blog/service-oriented/bray-soa-too-complex-just-vendor-bs/597)". ZDNet.
[^31]: Jimmy Zhang (February 20, 2008) "[Index XML Documents with VTD-XML](http://xml.sys-con.com/read/453082.htm)". XML Journal.
[^32]: Jimmy Zhang (August 5, 2008) "[i-Technology Viewpoint: The Performance Woe of Binary XML](http://soa.sys-con.com/read/250512.htm)". Microservices Journal.
[^33]: Jimmy Zhang (January 9, 2008) "[Manipulate XML Content the Ximple Way](http://www.devx.com/xml/Article/36379)". devx.com.
[^34]: "[The Reason SOA Isn't Delivering Sustainable Software](http://www.jpmorgenthal.com/morgenthal/?p=31)". jpmorgenthal.com. June 19, 2009. Retrieved June 27, 2009.
[^35]: "[SOA services still too constrained by applications they represent](http://www.zdnet.com/article/soa-services-still-too-constrained-by-applications-they-represent/)". zdnet.com. June 27, 2009. Retrieved June 27, 2009.
[^36]: "[Governance Layer](https://www.opengroup.org/soa/source-book/soa_refarch/governance.htm)". www.opengroup.org. Retrieved September 22, 2016.
[^37]: "[How to Efficiently Test Service Oriented Architecture | WSO2 Inc](http://wso2.com/library/articles/2014/04/how-to-efficiently-test-service-oriented-architecture/)". wso2.com. Retrieved September 22, 2016.
[^38]: http://drops.dagstuhl.de/opus/volltexte/2009/2046/pdf/09021_abstracts_collection.2046.pdf
[^39]: "[What Is Web 2.0](http://www.oreillynet.com/pub/a/oreilly/tim/news/2005/09/30/what-is-web-20.html)". Tim O'Reilly. September 30, 2005. Retrieved June 10, 2008.
[^40]: Christoph Schroth & Till Janner (2007). "[Web 2.0 and SOA: Converging Concepts Enabling the Internet of Services](http://www.alexandria.unisg.ch/Publikationen/37270)". IT Professional 9 (2007), Nr. 3, pp. 36–41, IEEE Computer Society. Retrieved February 23, 2008.
[^41]: Dragoni, Nicola; Giallorenzo, Saverio; Alberto Lluch Lafuente; Mazzara, Manuel; Montesi, Fabrizio; Mustafin, Ruslan; Safina, Larisa (2016). "Microservices: yesterday, today, and tomorrow". [arXiv](https://en.wikipedia.org/wiki/ArXiv):[1606.04036v1](https://arxiv.org/abs/1606.04036v1) [cs.SE](https://arxiv.org/archive/cs.SE).
[^42]: James Lewis and Martin Fowler. "[Microservices](http://martinfowler.com/articles/microservices.html)".
[^43]: Balalaie, A.; Heydarnoori, A.; Jamshidi, P. (May 1, 2016). "[Microservices Architecture Enables DevOps: Migration to a Cloud-Native Architecture](http://ieeexplore.ieee.org/lpdocs/epic03/wrapper.htm?arnumber=7436659)". IEEE Software. 33 (3): 42–52. [doi](https://en.wikipedia.org/wiki/Digital_object_identifier):[10.1109/MS.2016.64](https://doi.org/10.1109%2FMS.2016.64). [hdl](https://en.wikipedia.org/wiki/Handle_System):[10044/1/40557](https://hdl.handle.net/10044%2F1%2F40557). [ISSN](https://en.wikipedia.org/wiki/International_Standard_Serial_Number) [0740-7459](https://www.worldcat.org/issn/0740-7459).
