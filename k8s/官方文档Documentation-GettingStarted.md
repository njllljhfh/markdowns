# 官方文档Documentation-GettingStarted

不同的Kubernetes解决方案满足不同的需求：如易于维护、安全、控制、可用的资源需求，学习操作和管理集群所需的专业知识的需求。

您可以在本地机器、云、on-prem数据中心上部署Kubernetes集群，或者选择托管的Kubernetes集群。您还可以跨各种云提供商或裸机环境创建自定义解决方案。

更简单地说，您可以在学习和生产环境中创建Kubernetes集群。



**Learning environment**

如果您正在学习Kubernetes，请使用基于docker的解决方案（Kubernetes社区支持的工具，或生态系统中的工具）在本地机器上建立Kubernetes集群。

| Community                                                    | Ecosystem                                                    |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [Minikube](https://kubernetes.io/docs/setup/learning-environment/minikube/) | [CDK on LXD](https://www.ubuntu.com/kubernetes/docs/install-local) |
| [kind (Kubernetes IN Docker)](https://kubernetes.io/docs/setup/learning-environment/kind/) | [Docker Desktop](https://www.docker.com/products/docker-desktop) |
|                                                              | [Minishift](https://docs.okd.io/latest/minishift/)           |
|                                                              | [MicroK8s](https://microk8s.io/)                             |
|                                                              | [IBM Cloud Private-CE (Community Edition)](https://github.com/IBM/deploy-ibm-cloud-private) |
|                                                              | [IBM Cloud Private-CE (Community Edition) on Linux Containers](https://github.com/HSBawa/icp-ce-on-linux-containers) |
|                                                              | [k3s](https://k3s.io/)                                       |



**Production environment**

在为生产环境评估解决方案时，请考虑在操作Kubernetes集群（或抽象）时哪些方面希望自己管理，哪些想要承包给 Kubernetes 供应商。

For a list of [Certified Kubernetes](https://github.com/cncf/k8s-conformance/#certified-kubernetes) providers，see “[Partners](https://kubernetes.io/partners/#conformance)”.



# 一、生产环境

（Production environment）

## 1.1、容器运行时

（Container runtimes）

Docker

https://kubernetes.io/docs/setup/production-environment/container-runtimes/#docker



## 1.2、使用部署工具安装Kubernetes

（Installing Kubernetes with deployment tools）

### 1.2.1、使用kubeadm引导集群

（Bootstrapping clusters with kubeadm）

#### 1.2.1.1、安装kubeadm

（Installing kubeadm）

这个页面展示了如何安装 `kubeadm` toolbox。

##### 1.2.1.1.1、安装前

- 一个或多个装有以下系统的机器：
    - Ubuntu 16.04+
    - Debian 9+
    - CentOS 7
    - Red Hat Enterprise Linux (RHEL) 7
    - Fedora 25+
    - HypriotOS v1.0.1+
    - Container Linux (tested with 1800.6.0)

- 每台机器有2G或更多的内存(如果内存不足，应用程序的空间就所剩无几)
- 2个或更多的cpu
- 集群中所有机器之间的网络连接畅通（公共或私有网络都可以）
- 每个节点的唯一主机名、MAC地址和product_uuid。详情请看[这里](#1.2.1.1.2、验证每个节点的MAC地址和product_uuid是惟一的)。
- 确认您机器上的某些端口是打开的。详情请看[这里]()。
- 禁用Swap。为了使kubelet正常工作，您$\color{red}{\Large 必须}$禁用Swap。



##### 1.2.1.1.2、验证每个节点的MAC地址和product_uuid是惟一的

（Verify the MAC address and product_uuid are unique for every node）

- 您可以使用命令 `ip link` 或 `ifconfig -a  ` 获得网络接口的MAC地址
- 可以使用 `sudo cat /sys/class/dmi/id/product_uuid` 命令来检查product_uuid

硬件设备很可能有唯一的地址，尽管一些虚拟机可能有相同的值。Kubernetes使用这些值来唯一地标识集群中的节点。如果这些值不是每个节点唯一的，则安装过程可能会失败（[fail](https://github.com/kubernetes/kubeadm/issues/31)）。



##### 1.2.1.1.3、检查网络适配器

（Check network adapters）

如果您有一个以上的网络适配器，并且您的Kubernetes组件无法通过默认路由访问，我们建议您添加IP路由，以便Kubernetes集群地址可以通过合适的适配器访问。



##### 1.2.1.1.4、让iptables看到桥接的通信流

（Letting iptables see bridged traffic）

为了让您的Linux节点的iptables正确地看到桥接的通信流，您应该确保在 `sysctl` 配置中将 `net.bridge.bridge-nf-call-iptables` 的值设置为 1 ，例如。

```bash
cat <<EOF > /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system
```

确保在此步骤之前加载了 `br_netfilter `模块。这可以通过运行 `lsmod | grep br_netfilter` 来实现。显式地调用 `modprobe br_netfilter` 来加载它。

更多详情请参见网络插件需求（ [Network Plugin Requirements](https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#network-plugin-requirements) ）。



##### 1.2.1.1.5、检查所需的端口

（Check required ports）

**Control-plane node(s)**

| Protocol | Direction | Port Range | Purpose                 | Used By              |
| :------- | :-------- | :--------- | :---------------------- | :------------------- |
| TCP      | Inbound   | 6443*      | Kubernetes API server   | All                  |
| TCP      | Inbound   | 2379-2380  | etcd server client API  | kube-apiserver, etcd |
| TCP      | Inbound   | 10250      | Kubelet API             | Self, Control plane  |
| TCP      | Inbound   | 10251      | kube-scheduler          | Self                 |
| TCP      | Inbound   | 10252      | kube-controller-manager | Self                 |

**Worker node(s)**

| Protocol | Direction | Port Range  | Purpose            | Used By             |
| :------- | :-------- | :---------- | :----------------- | :------------------ |
| TCP      | Inbound   | 10250       | Kubelet API        | Self, Control plane |
| TCP      | Inbound   | 30000-32767 | NodePort Services† | All                 |

 [NodePort Services](https://kubernetes.io/docs/concepts/services-networking/service/) 的默认端口范围。

任何标有 `*` 的端口号都是可覆盖的，因此您需要确保您提供的任何自定义端口也是打开的。

虽然etcd端口包含在控制平面节点中，但是您也可以在外部或自定义端口上托管自己的etcd集群。

您使用的pod网络插件（见下面）也可能需要打开某些端口。由于每个pod网络插件不同，请参阅有关插件的文档来获取该插件所需的端口。



##### 1.2.1.1.6、安装运行时

（Installing runtime）

要在Pods中运行容器，Kubernetes使用容器运行时（[container runtime](https://kubernetes.io/docs/reference/generated/container-runtime)）。

默认情况下，Kubernetes使用容器运行时接口（[Container Runtime Interface（CRI）](https://kubernetes.io/docs/concepts/overview/components/#container-runtime)）与您选择的容器运行时进行对接。

如果没有指定运行时，kubeadm将通过扫描已知的Unix域套接字（Unix domain sockets）列表，自动尝试检测已安装的容器运行时。下表列出了容器运行时及其相关的套接字路径：

| Runtime    | Path to Unix domain socket        |
| :--------- | :-------------------------------- |
| Docker     | `/var/run/docker.sock`            |
| containerd | `/run/containerd/containerd.sock` |
| CRI-O      | `/var/run/crio/crio.sock`         |

如果检测到Docker和containerd，则Docker优先。这是必要的，因为Docker 18.09中包含containerd，而且两者都是可检测的，即使你只安装Docker。如果检测到其他两个或更多的运行时，kubeadm会报错并退出。

kubelet通过内置的`dockershim` CRI 实现与 Docker 集成。

有关更多信息，请参见容器运行时（[container runtimes](https://kubernetes.io/docs/setup/production-environment/container-runtimes/)）。



##### 1.2.1.1.7、安装 kubeadm, kubelet and kubectl

[Installing kubeadm, kubelet and kubectl](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#installing-kubeadm-kubelet-and-kubectl)

您将在所有的机器上安装这些软件包：

- `kubeadm` ：引导集群的命令。
- `kubelet` ：在集群中的所有机器上运行的组件，它执行启动pod、容器之类的操作。
- `kubectl` ：用来与您的集群进行通信的命令行工具。

`kubeadm` **不会**为您安装或管理 `kubelet` 或 `kubectl` ，因此您需要确保它们与您希望kubeadm为您安装的Kubernetes控制平面的版本匹配。如果不这样做，就会出现版本倾斜（version skew）的风险，从而导致意外的、错误的行为。但是支持kubelet和控制平面之间的一个小版本偏差，不过kubelet版本可能永远不会超过API服务器版本。例如，运行1.7.0的kubelets应该与1.8.0 API服务器完全兼容，但反之则不然。

有关安装kubectl的信息，请参阅 [Install and set up kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)。

> 警告：这些说明从任何系统升级中排除了所有Kubernetes包。这是因为kubeadm和Kubernetes需要特别注意升级（[special attention to upgrade](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/)）。

有关版本倾斜（version skews）的更多信息，请参见：

- Kubernetes [version and version-skew policy](https://kubernetes.io/docs/setup/release/version-skew-policy/)
- Kubeadm-specific [version skew policy](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#version-skew-policy)

在CentOS系统中安装（$\color {red}{\Large 此处将谷歌的镜像换成了阿里云的镜像}$）：

```bash
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=http://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=http://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg http://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF

# Set SELinux in permissive mode (effectively disabling it)
setenforce 0
sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config

yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes

systemctl enable --now kubelet
```

> 注意：
>
> 通过运行 `setenforce 0` 和 `sed...` 将 SELinux 设置为允许模式，可以有效地禁用它。这是允许容器访问主机文件系统所必需的，例如pod网络就需要主机文件系统。在kubelet中SELinux的支持得到改进之前，您必须这样做。

kubelet现在每隔几秒钟就重新启动一次，因为它在一个crashloop中等待kubeadm告诉它该做什么。



##### 1.2.1.1.8、在控制平面节点上配置kubelet使用的cgroup驱动程序

（Configure cgroup driver used by kubelet on control-plane node）

当使用 Docker 时，kubeadm 将在运行时自动检测 kubelet 的 cgroup驱动程序，并将其设置在 `/var/lib/kubelet/kubeadm-flags.env` 文件中。

如果你使用不同的CRI，你必须修改文件 `/etc/default/kubelet` （在CentOS、 RHEL、 Fedora中要修改的文件为`/etc/sysconfig/kubelet` ）来设置`cgroup-driver`的值，像这样：

```bash
KUBELET_EXTRA_ARGS=--cgroup-driver=<value>
```

这个文件将被 `kubeadm init` 和 `kubeadm join ` 用于为kubelet获取额外的用户定义的参数。

请注意，只有当CRI的 cgroup driver 不是 `cgroupfs` 时，您才需要这样做，因为这已经是kubelet中的默认值了。

需要重新启动kubelet：

```bash
systemctl daemon-reload
systemctl restart kubelet
```

对其他容器运行时（如CRI-O和containerd）的 cgroup driver 的自动检测正在进行中。（原文：The automatic detection of cgroup driver for other container runtimes like CRI-O and containerd is work in progress.）

##### 1.2.1.1.9、分析解决问题

如果您在使用kubeadm时遇到困难，请咨询我们的 [troubleshooting docs](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/troubleshooting-kubeadm/)。



##### 1.2.1.1.10、What's next

- 使用kubeadm创建集群（[Using kubeadm to Create a Cluster](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)）

 

#### 1.2.1.2、使用kubeadm创建单个控制平面集群

（Creating a single control-plane cluster with kubeadm）