# 官网文档 Document-Concepts



# 一、概述（Overview）

[官网文档-概述-什么是k8s](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/)

## 1.1、什么是Kubernetes

这一页是Kubernetes的概述。

- [时光倒流](#1.1.1、时光倒流)
- [为什么需要 Kubernetes？它能做什么？](#1.1.2、为什么需要 Kubernetes？它能做什么？)
- [Kubernetes 不是什么？](#1.1.3、Kubernetes 不是什么？)
- [接下来的内容](#1.1.4、接下来的内容)

### 1.1.1、时光倒流

Kubernetes 是一个可移植的、可扩展的、用于管理容器化工作负载和服务的开源平台，它促进了声明性配置和自动化。它有一个庞大的、快速增长的生态系统。Kubernetes的服务、支持和工具随处可见。

Kubernetes 这个名字来源于希腊语，意思是舵手或飞行员。2014年，谷歌开放了Kubernetes项目的源代码。Kubernetes [基于谷歌在大规模运行生产工作负载方面的15年经验](https://ai.google/research/pubs/pub43438) ，以及来自社区的最佳想法和实践。

让我们通过时间回溯，看看为什么 Kubernetes 是如此有用。

![](./images/概述/1-1_container_evolution.svg)

**传统部署时代** ：早期，组织在物理服务器上运行应用程序。无法为物理服务器中的应用程序定义资源边界，这导致了资源分配问题。例如，如果一个物理服务器上运行多个应用程序，那么在某些情况下，一个应用程序可能会占用大部分资源，从而导致其他应用程序性能下降。对此的解决方案是在不同的物理服务器上运行每个应用程序。但是这并没有实现扩展，因为资源没有得到充分利用，并且维护许多物理服务器的成本很高。



**虚拟化部署时代** ：虚拟化作为一种解决方案被引入。它允许您在单个物理服务器的CPU上运行多个虚拟机(VM)。虚拟化允许在 VM 之间隔离应用程序，并提供一定程度的安全性，因为一个应用程序的信息不能被另一个应用程序随意地访问。

虚拟化可以更好地利用物理服务器中的资源，并提供更好的可扩展性，因为可以方便地添加或更新应用程序，从而降低硬件成本，等等。通过虚拟化，您可以将一组物理资源表示为 ***一次性虚拟机集群***（disposable virtual machines）。

每个VM是一个完整的机器，运行所有组件，包括它自己的操作系统，运行在虚拟硬件之上。



**容器部署时代**：容器类似于VM，但是它们具有宽松的隔离属性，以便在应用程序之间共享操作系统(OS)。因此，容器被认为是轻量级的。与VM类似，容器有自己的文件系统、CPU、内存、进程空间等等。由于它们与底层基础设施解耦，因此可以跨 云 和 OS发行版 移植。

集装箱已经变得很流行，因为它们有额外的好处，比如:

- **敏捷的应用程序创建和部署**：与使用VM镜像相比，增加了容器镜像创建的方便性和效率。

- **持续开发、集成和部署**：提供可靠且频繁的容器镜像构建和部署，具有快速且轻松的回滚(由于镜像不可变性)。
- **开发和操作关注点分离**：在构建/发布时而不是部署时创建应用程序容器镜像，从而将应用程序与基础设施解耦。
- 可观察性不仅能显示操作系统级的信息和度量，还能显示应用程序的健康状况和其他信号。
- **跨开发、测试和生产的环境一致性**：在笔记本电脑上运行与在云中运行相同。
- **云 和 OS分布式 的可移植性**: 可运行在Ubuntu上，RHEL， CoreOS， on-prem，Google Kubernetes Engine，还有其他地方。
- **以应用程序为中心的管理**：将抽象级别从 在虚拟硬件上运行操作系统 提高到 使用逻辑资源在操作系统上运行应用程序。
- **低耦合、分布式、弹性、解放的微服务**：应用程序被分解成更小的独立部分，可以动态地部署和管理——而不是运行在一台大型单用途机器上的单片堆栈。
- **资源隔离**：可预测的应用程序性能。
- **资源利用**：效率高，密度大。



### 1.1.2、为什么需要 Kubernetes？它能做什么？

容器是捆绑和运行应用程序的好方法。在生产环境中，您需要管理运行应用程序的容器，并确保没有停机时间。例如，如果一个容器发生故障，则需要启动另一个容器。如果这个行为由一个系统来处理不是更容易吗?

这就是救星 Kubernetes！Kubernetes为您提供了一个运行有恢复能力的分布式系统的框架。它负责处理应用程序的扩展和故障转移，提供部署模式，等等。例如，Kubernetes可以轻松地管理系统的canary部署。

Kubernetes为您提供：

- **服务发现和负载平衡**

    Kubernetes 可以使用DNS名称或自己的IP地址暴露（expose）容器。如果到容器的通信量很高，Kubernetes能够实现负载均衡并分配网络通信量，从而使部署保持稳定。

- **存储编排**
    Kubernetes 允许您自动挂载自己选择的存储系统，比如本地存储、公有云提供商等等。

- **自动发布和回滚**
    您可以使用 Kubernetes 描述所部署容器的期望状态，并且可以以受控的速率将实际状态更改为期望状态。例如，您可以自动化Kubernetes来为您的部署创建新的容器，删除现有的容器，并将它们的所有资源继承到新的容器中。

- **自动二进制打包**
    您为 Kubernetes 提供了一组集群节点，它可以使用这些节点来运行 容器化的任务。告诉 Kubernetes 每个容器需要多少CPU和内存(RAM)。Kubernetes 可以在您的节点上放置容器，以充分利用您的资源。

- **自愈性**
    Kubernetes 重新启动失败的容器，替换容器，杀死不响应 您的用户定义的健康检查 的容器，并且在它们准备好服务之前不会向客户公开（advertise）它们。

- **秘密和配置管理**
    Kubernetes 允许您存储和管理敏感信息，比如密码、OAuth tokens 和 SSH keys。您可以部署和更新秘密和应用程序配置，而不需要重新构建容器镜像，也不需要在堆栈配置中公开秘密。



### 1.1.3、Kubernetes 不是什么？

Kubernetes 不是一个传统的、包含所有功能的PaaS(平台即服务)系统。由于Kubernetes是在容器级别而不是在硬件级别操作的，所以它提供了一些PaaS产品常见的通用特性，例如部署、扩展、负载平衡、日志记录和监视。但是，Kubernetes 不是单一的，这些默认的解决方案是可选的和可插拔的。Kubernetes 为构建开发人员平台提供了构建块，但是在重要的地方保留了用户的选择和灵活性。

Kubernetes:

- 不限制所支持的应用程序类型。Kubernetes的目标是支持非常多样化的工作负载，包括无状态、有状态和数据处理工作负载。如果一个应用程序可以在容器中运行，那么它应该可以在Kubernetes上运行。
- 不部署源代码，也不构建应用程序。持续集成、交付和部署(CI/CD)工作流由组织文化和偏好以及技术需求决定。
- 不提供应用程序级的服务，如中间件(例如， message buses)、数据处理框架(例如，Spark)、数据库(例如，MySQL)、缓存，也不提供集群存储系统(例如，Ceph)作为内置服务。这些组件可以运行在Kubernetes上，并且/或者可以由运行在Kubernetes上的应用程序通过可移植的机制(如[Open Service Broker](https://openservicebrokerapi.org/))访问。
- 不指定日志记录、监视或警报解决方案。它提供了一些集成作为概念的证明，以及收集和导出指标的机制。
- 不提供也不强制要求配置语言/系统(例如，Jsonnet)。它提供了一个声明性API，可以被任意形式的声明性规范作为目标。
- 不提供也不采用任何全面的机器配置、维护、管理或自修复系统。
- 此外，Kubernetes不仅仅是一个编排系统。事实上，它消除了对编排的需要。编排的技术定义是执行已定义的工作流：首先执行A，然后执行B，然后执行C。相反，Kubernetes包含一组独立的、可组合的控制流程，这些流程不断地将当前状态驱动到所提供的期望状态。你怎么从A点到C点都不重要，也不需要集中控制。这使得系统更容易使用，并且更强大、健壮、有弹性（适应能力强）和易扩展。



### 1.1.4、接下来的内容

- 来看看K8s的组件（[Kubernetes Components](https://kubernetes.io/docs/concepts/overview/components/)）。

- 准备好开始（ [Get Started](https://kubernetes.io/docs/setup/)）了么？



## 1.2、k8s组件

[官方文档-概述-k8s组件](https://kubernetes.io/docs/concepts/overview/components/)

当您部署Kubernetes时，您将得到一个集群。

Kubernetes 集群由一组称为节点的工作机器组成，节点运行容器化的应用程序。每个集群至少有一个工作节点。

工作 node(节点) 中管理着作为应用程序组件的 pods。Control Plane 管理集群中的工作节点和pod。在生产环境中，Control Plane 通常跨多台计算机运行，集群通常运行多个节点，提供容错和高可用性。

本文档概述了拥有一个完整的、工作的Kubernetes集群所需的各种组件。

下面是Kubernetes集群的关系图，所有组件都联系在一起。

![](./images/概述/1-2_components-of-kubernetes.png)



- [控制平面组件（Control Plane Components）](#1.2.1、控制平面组件(Control Plane Components))
- [节点组件（Node Components）](#1.2.2、节点组件（Node Components）)
- [插件（Addons）](#1.2.3、插件（Addons）)
- [接下来是什么](#1.2.4、接下来是什么)



### 1.2.1、控制平面组件（Control Plane Components）

控制平面的组件做出关于集群的全局决策(例如，调度)，以及检测和响应集群事件(例如，当部署的`replicas`字段不满足时启动一个新的pod)。

控制平面组件可以在集群中的任何机器上运行。但是，为了简单起见，设置脚本通常在同一台机器上启动所有控制平面组件，并且不在这台机器上运行用户容器。有关 `multi-master-VM` 设置的示例，请参见 [构建高可用性集群](https://kubernetes.io/docs/admin/high-availability/) 。

#### 1.2.1.1、kube-apiserver

API服务器是公开 Kubernetes API 的 Kubernetes 控制平面的一个组件。API 服务器是 Kubernetes 控制平面的前端。

Kubernetes API 服务器的主要实现是 [kube-apiserver](https://kubernetes.io/docs/reference/generated/kube-apiserver/) 。kube-apiserver 被设计成水平扩展的——也就是说，它通过部署更多实例来扩展。您可以运行 kube-apiserver 的多个实例，并在这些实例之间均衡流量。

#### 1.2.1.2、etcd

具有一致性和高可用性的键值存储，用作 Kubernetes 所有集群的数据备份存储。

如果您的 Kubernetes 集群使用etcd作为其备份存储，请确保对这些数据有 [备份](https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/#backing-up-an-etcd-cluster) 计划。

您可以在 [官方文档](https://etcd.io/docs/) 中找到关于etcd的详细信息。

#### 1.2.1.3、kube-scheduler



#### 1.2.1.4、kube-controller-manager



#### 1.2.1.5、cloud-controller-manager





### 1.2.2、节点组件（Node Components）





### 1.2.3、插件（Addons）





### 1.2.4、接下来是什么



