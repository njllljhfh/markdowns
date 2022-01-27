# 二、容器技术入门概念



UnionFS：AuFS



whiteout（白障）



/sys/fs/aufs



/var/lib/docker/aufs/mnt



- 登录docker后的提示：WARNING! Your password will be stored unencrypted in /home/njl/.docker/config.json.



- Docker 在镜像的设计中，引入了层（layer）的概念。也就是说，用户制作镜像的每一步操作，都会生成一个层，也就是一个增量 rootfs。使用多个增量 rootfs 联合挂载一个完整 rootfs 的方案，这就是容器镜像中“层”的概念。



- 需要注意的是，Dockerfile 中的每个原语执行后，都会生成一个对应的镜像层。即使原语本身并没有明显地修改文件的操作（比如，ENV 原语），它对应的层也会存在。只不过在外界看来，这个层是空的。



- docker commit，实际上就是在容器运行起来后，把最上层的“可读写层”，加上原先容器镜像的只读层，打包组成了一个新的镜像。当然，下面这些只读层在宿主机上是共享的，不会占用额外的空间。
- 而由于使用了联合文件系统，你在容器里对镜像 rootfs 所做的任何修改，都会被操作系统先复制到这个可读写层，然后再修改。这就是所谓的：`Copy-on-Write`。
- 而正如前所说，Init 层的存在，就是为了避免你执行 docker commit 时，把 Docker 自己对 /etc/hosts 等文件做的修改，也一起提交掉。



- 注意："容器进程"，是 Docker 创建的一个容器初始化进程 (dockerinit)，而不是应用进程 (ENTRYPOINT + CMD)。dockerinit 会负责完成根目录的准备、挂载设备和目录、配置 hostname 等一系列需要在容器内进行的初始化操作。最后，它通过 execv() 系统调用，让应用进程取代自己，成为容器里的 PID=1 的进程。





## 安装docker(ubuntu)

[官方文档](https://docs.docker.com/engine/install/ubuntu/)



## 09、从容器到容器云：谈谈Kubernetes的本质

- 一个“容器”，实际上是一个由 Linux Namespace、Linux Cgroups 和 rootfs 三种技术构建出来的进程的隔离环境。



从这个结构中我们不难看出，一个正在运行的 Linux 容器，其实可以被“一分为二”地看待：

- 一组联合挂载在 `/var/lib/docker/overlay2/3b7161c4/merged` 上的 rootfs，这一部分我们称为“容器镜像”（Container Image），是容器的静态视图；
- 一个由 Namespace+Cgroups 构成的隔离环境，这一部分我们称为“容器运行时”（Container Runtime），是容器的动态视图。
- 更进一步地说，作为一名开发者，我并不关心容器运行时的差异。因为，在整个“开发 - 测试 - 发布”的流程中，真正承载着容器信息进行传递的，是容器镜像，而不是容器运行时。



### Kubernetes 项目的架构

![Kubernetes项目的架构](./images/Kubernetes项目的架构.webp)

我们可以看到，Kubernetes 项目的架构，跟它的原型项目 Borg 非常类似，都由 Master 和 Node 两种节点组成，而这两种角色分别对应着控制节点和计算节点。

其中，控制节点，即 Master 节点，由三个紧密协作的独立组件组合而成，它们分别是负责 API 服务的 **kube-apiserver**、负责调度的 **kube-scheduler**，以及负责容器编排的 **kube-controller-manager**。整个集群的持久化数据，则由 kube-apiserver 处理后保存在 Etcd 中。

而计算节点上最核心的部分，则是一个叫作 kubelet 的组件。

在 Kubernetes 项目中，**kubelet** 主要负责同容器运行时（比如 Docker 项目）打交道。而这个交互所依赖的，是一个称作 **CRI（Container Runtime Interface）**的远程调用接口，这个接口定义了容器运行时的各项核心操作，比如：启动一个容器需要的所有参数。

这也是为何，Kubernetes 项目并不关心你部署的是什么容器运行时、使用的什么技术实现，只要你的这个容器运行时能够运行标准的容器镜像，它就可以通过实现 CRI 接入到 Kubernetes 项目当中。

而具体的容器运行时，比如 Docker 项目，则一般通过 **OCI（Open Container Initiative，开放容器标准）**这个容器运行时规范同底层的 Linux 操作系统进行交互，即：把 CRI 请求翻译成对 Linux 操作系统的调用（操作 Linux Namespace 和 Cgroups 等）。

此外，kubelet 还通过 **gRPC 协议** 同一个叫作 Device Plugin 的插件进行交互。这个插件，是 Kubernetes 项目用来管理 GPU 等宿主机物理设备的主要组件，也是基于 Kubernetes 项目进行机器学习训练、高性能作业支持等工作必须关注的功能。

而 kubelet 的另一个重要功能，则是调用网络插件和存储插件为容器配置 **网络** 和 **持久化存储**。这两个插件与 kubelet 进行交互的接口，分别是 **CNI（Container Networking Interface）**和 **CSI（Container Storage Interface）**。



### Kubernetes 项目核心功能的“全景图”

![Kubernetes项目核心功能的全景图](./images/Kubernetes项目核心功能的全景图.webp)

按照这幅图的线索，我们从容器这个最基础的概念出发，首先遇到了容器间“紧密协作”关系的难题，于是就扩展到了 Pod；有了 Pod 之后，我们希望能一次启动多个应用的实例，这样就需要 Deployment 这个 Pod 的多实例管理器；而有了这样一组相同的 Pod 后，我们又需要通过一个固定的 IP 地址和端口以负载均衡的方式访问它，于是就有了 Service。

可是，如果现在两个不同 Pod 之间不仅有“访问关系”，还要求在发起时加上授权信息。最典型的例子就是 Web 应用对数据库访问时需要 Credential（数据库的用户名和密码）信息。那么，在 Kubernetes 中这样的关系又如何处理呢？

Kubernetes 项目提供了一种叫作 Secret 的对象，它其实是一个保存在 Etcd 里的键值对数据。这样，你把 Credential 信息以 Secret 的方式存在 Etcd 里，Kubernetes 就会在你指定的 Pod（比如，Web 应用的 Pod）启动时，自动把 Secret 里的数据以 Volume 的方式挂载到容器里。这样，这个 Web 应用就可以访问数据库了。

除了应用与应用之间的关系外，应用运行的形态是影响 “**如何容器化这个应用**” 的第二个重要因素。

为此，Kubernetes 定义了新的、基于 Pod 改进后的对象。比如 Job，用来描述一次性运行的 Pod（比如，大数据任务）；再比如 DaemonSet，用来描述每个宿主机上必须且只能运行一个副本的守护进程服务；又比如 CronJob，则用于描述定时任务等等。

如此种种，正是 Kubernetes 项目定义容器间关系和形态的主要方法。

可以看到，Kubernetes 项目并没有像其他项目那样，为每一个管理功能创建一个指令，然后在项目中实现其中的逻辑。这种做法，的确可以解决当前的问题，但是在更多的问题来临之后，往往会力不从心。

相比之下，在 Kubernetes 项目中，我们所推崇的使用方法是：

- 首先，通过一个“编排对象”，比如 Pod、Job、CronJob 等，来描述你试图管理的应用；
- 然后，再为它定义一些“服务对象”，比如 Service、Secret、Horizontal Pod Autoscaler（自动水平扩展器）等。这些对象，会负责具体的平台级功能。

这种使用方法，就是所谓的 “**声明式 API**” 。这种 API 对应的 “**编排对象**” 和 “**服务对象**”，都是 Kubernetes 项目中的 API 对象（API Object）。

这就是 Kubernetes 最核心的设计理念。



# 三、Kubernetes集群搭建与实践

## 安装k8s

### 1、安装kubelet kubeadm kubectl

为了让你的 Linux 节点上的 iptables 能够正确地查看桥接流量，你需要确保在你的 `sysctl` 配置中将 `net.bridge.bridge-nf-call-iptables` 设置为 1。例如：

```shell
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sudo sysctl --system
```

打开 `/etc/apt/sources.list` 文件，添加一行

```shell
vim /etc/apt/sources.list
deb https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial main

# 然后执行
sudo apt-get update
```

再次执行安装 K8s 的命令，如果出现：

```
The following signatures couldn't be verified because the public key is not available
```

则执行下面命令，为期添加 key。

```c#
curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | sudo apt-key add 
```

添加 Kubernetes `apt` 仓库：

```shell
#echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-#xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] http://mirrors.ustc.edu.cn/kubernetes/apt kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

更新 `apt` 包索引，安装 kubelet、kubeadm 和 kubectl，并锁定其版本：

```
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

关闭 swap

```shell
# 临时关闭
swapoff -a
# 永久关闭
vim /etc/fstab  # 注释掉最后一行的swap
```

kubelet 无法自启动，要修改docker配置，配置cgroupdriver=systemd

```shell
# 修改 /etc/docker/daemon.json 
# 修改前
{
    "registry-mirrors": ["https://y0qd3iq.mirror.aliyuncs.com"]
}

# 修改后
{
    "exec-opts": [
        "native.cgroupdriver=systemd"
    ],
    "registry-mirrors": ["https://y0qd3iq.mirror.aliyuncs.com"]
}

# 重启docker
systemctl restart docker

systemctl daemon-reload
systemctl enable kubelet
# 重置kubeadm
kubeadm reset
```



### 2、创建一个Master节点

```
kubeadm init
```

由于`k8s.gcr.io` 上的镜像国内拉不下来，要用 docker 拉取 k8s 需要的相应版本的全部镜像

```shell
# 列出k8s需要的镜像列表
[root@node1]# kubeadm config images list 
k8s.gcr.io/kube-apiserver:v1.22.4
k8s.gcr.io/kube-controller-manager:v1.22.4
k8s.gcr.io/kube-scheduler:v1.22.4
k8s.gcr.io/kube-proxy:v1.22.4
k8s.gcr.io/pause:3.5
k8s.gcr.io/etcd:3.5.0-0
k8s.gcr.io/coredns/coredns:v1.8.4

# 修改为
docker pull registry.aliyuncs.com/google_containers/kube-apiserver:v1.22.4
docker pull registry.aliyuncs.com/google_containers/kube-controller-manager:v1.22.4
docker pull registry.aliyuncs.com/google_containers/kube-scheduler:v1.22.4
docker pull registry.aliyuncs.com/google_containers/kube-proxy:v1.22.4  # 子节点也需要
docker pull registry.aliyuncs.com/google_containers/pause:3.5  # 子节点也需要
docker pull registry.aliyuncs.com/google_containers/etcd:3.5.0-0
docker pull registry.aliyuncs.com/google_containers/coredns:1.8.4
# 对上述 aliyuncs 的镜像 重新打 tag，并删除 旧tag的镜像（即修改为 kubeadm config images list 中指定的镜像的tag）
# 例如
docker tag registry.aliyuncs.com/google_containers/coredns:1.8.4 k8s.gcr.io/coredns/coredns:v1.8.4  # 重新打tag
docker rmi registry.aliyuncs.com/google_containers/coredns:1.8.4  # 删除修改前的镜像

docker tag registry.aliyuncs.com/google_containers/pause:3.5 k8s.gcr.io/pause:3.5
docker rmi registry.aliyuncs.com/google_containers/pause:3.5

docker tag registry.aliyuncs.com/google_containers/kube-proxy:v1.22.4 k8s.gcr.io/kube-proxy:v1.22.4
docker rmi registry.aliyuncs.com/google_containers/kube-proxy:v1.22.4


# 将上述过程编写为shell脚本
touch pullk8s.sh
chmod +x pullk8s.sh
vim pullk8s.sh
# 写入如下内容
#!/bin/bash
# 用root用户执行该脚本
for i in `kubeadm config images list`
do
    imageName=${i#k8s.gcr.io/}
    if [[ $imageName == coredns* ]]
    then
        arr=(${imageName/:/ })
        name=${arr[0]}
        tag=${arr[1]}
        tag=${tag#v}
        newImageName=coredns:$tag
        docker pull registry.aliyuncs.com/google_containers/$newImageName
        docker tag registry.aliyuncs.com/google_containers/$newImageName k8s.gcr.io/$imageName
        docker rmi registry.aliyuncs.com/google_containers/$newImageName
    else
        docker pull registry.aliyuncs.com/google_containers/$imageName
        docker tag registry.aliyuncs.com/google_containers/$imageName k8s.gcr.io/$imageName
        docker rmi registry.aliyuncs.com/google_containers/$imageName
    fi
done
```

再次 执行 `kubeadm init` 报错 ```[kubelet-check] It seems like the kubelet isn't running or healthy.```

```python
# 修改 /etc/docker/daemon.json 
# 修改前
{
    "registry-mirrors": ["https://y0qd3iq.mirror.aliyuncs.com"]
}

# 修改后
{
    "exec-opts": [
        "native.cgroupdriver=systemd"
    ],
    "registry-mirrors": ["https://y0qd3iq.mirror.aliyuncs.com"]
}

# 重启docker
systemctl restart docker

systemctl daemon-reload
systemctl enable kubelet
# 重置kubeadm
kubeadm reset
```

再次 执行

```
kubeadm init
```

安装成功

```shell
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 10.211.55.10:6443 --token coufe1.3uwoap6boj0a8ipl \
        --discovery-token-ca-cert-hash sha256:f387d63ef6130cf2b7b9c03098b2bc583d41aee924acc909794344eb5e42d692 
```

### 3、部署网络插件(每个节点都需要安装)

```
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```

节点如果没有拉取 weave 相关的镜像，手动拉取

```
docker pull ghcr.io/weaveworks/launcher/weave-kube:2.8.1
docker pull ghcr.io/weaveworks/launcher/weave-npc:2.8.1
```

部署子节点的时候，需要如下docker镜像，这些镜像都拉取后，再将子节点加入主节点（即，执行 kubeadm join 命令）

```
ghcr.io/weaveworks/launcher/weave-kube:2.8.1
ghcr.io/weaveworks/launcher/weave-npc:2.8.1
k8s.gcr.io/kube-proxy:v1.22.4
k8s.gcr.io/pause:3.5
```





## kubeadm init 的工作流程

当你执行 kubeadm init 指令后，kubeadm 首先要做的，是一系列的检查工作，以确定这台机器可以用来部署 Kubernetes。这一步检查，我们称为“Preflight Checks”，它可以为你省掉很多后续的麻烦。

其实，Preflight Checks 包括了很多方面，比如：

- Linux 内核的版本必须是否是 3.10 以上？
- Linux Cgroups 模块是否可用？
- 机器的 hostname 是否标准？
- 在 Kubernetes 项目里，机器的名字以及一切存储在 Etcd 中的 API 对象，都必须使用标准的 DNS 命名（RFC 1123）。
- 用户安装的 kubeadm 和 kubelet 的版本是否匹配？
- 机器上是不是已经安装了 Kubernetes 的二进制文件？
- Kubernetes 的工作端口 10250/10251/10252 端口是不是已经被占用？
- ip、mount 等 Linux 指令是否存在？Docker 是否已经安装？
- ……

在通过了 Preflight Checks 之后，kubeadm 要为你做的，是生成 Kubernetes 对外提供服务所需的各种证书和对应的目录。

Kubernetes 对外提供服务时，除非专门开启“不安全模式”，否则都要通过 HTTPS 才能访问 kube-apiserver。这就需要为 Kubernetes 集群配置好证书文件。

kubeadm 为 Kubernetes 项目生成的证书文件都放在 Master 节点的 /etc/kubernetes/pki 目录下。在这个目录下，最主要的证书文件是 ca.crt 和对应的私钥 ca.key。

证书生成后，kubeadm 接下来会为其他组件生成访问 kube-apiserver 所需的配置文件。这些文件的路径是：/etc/kubernetes/xxx.conf：

```shell
ls /etc/kubernetes/
admin.conf controller-manager.conf kubelet.conf scheduler.conf
```

接下来，kubeadm 会为 Master 组件生成 Pod 配置文件。我已经在上一篇文章中和你介绍过 Kubernetes 有三个 Master 组件 kube-apiserver、kube-controller-manager、kube-scheduler，而它们都会被使用 Pod 的方式部署起来。

你可能会有些疑问：这时，Kubernetes 集群尚不存在，难道 kubeadm 会直接执行 docker run 来启动这些容器吗？

当然不是。

在 Kubernetes 中，有一种特殊的容器启动方法叫做 **“Static Pod”** 。它允许你把要部署的 Pod 的 YAML 文件放在一个指定的目录里。这样，当这台机器上的 kubelet 启动时，它会自动检查这个目录，加载所有的 Pod YAML 文件，然后在这台机器上启动它们。

从这一点也可以看出，kubelet 在 Kubernetes 项目中的地位非常高，在设计上它就是一个完全独立的组件，而其他 Master 组件，则更像是辅助性的系统容器。

在 kubeadm 中，Master 组件的 YAML 文件会被生成在 `/etc/kubernetes/manifests` 路径下。

```shell
/etc/kubernetes/manifests$ ls
etcd.yaml  kube-apiserver.yaml  kube-controller-manager.yaml  kube-scheduler.yaml
```

在这一步完成后，kubeadm 还会再生成一个 Etcd 的 Pod YAML 文件，用来通过同样的 Static Pod 的方式启动 Etcd。所以，最后 Master 组件的 Pod YAML 文件如下所示：

```shell
$ ls /etc/kubernetes/manifests/
etcd.yaml  kube-apiserver.yaml  kube-controller-manager.yaml  kube-scheduler.yaml
```

而一旦这些 YAML 文件出现在被 kubelet 监视的 /etc/kubernetes/manifests 目录下，kubelet 就会自动创建这些 YAML 文件中定义的 Pod，即 Master 组件的容器。

Master 容器启动后，kubeadm 会通过检查 localhost:6443/healthz 这个 Master 组件的健康检查 URL，等待 Master 组件完全运行起来。

然后，kubeadm 就会为集群生成一个 bootstrap token。在后面，只要持有这个 token，任何一个安装了 kubelet 和 kubadm 的节点，都可以通过 kubeadm join 加入到这个集群当中。

这个 token 的值和使用方法，会在 kubeadm init 结束后被打印出来。

在 token 生成之后，kubeadm 会将 ca.crt 等 Master 节点的重要信息，通过 ConfigMap 的方式保存在 Etcd 当中，供后续部署 Node 节点使用。这个 ConfigMap 的名字是 cluster-info。

kubeadm init 的最后一步，就是安装默认插件。Kubernetes 默认 kube-proxy 和 DNS 这两个插件是必须安装的。它们分别用来提供整个集群的服务发现和 DNS 功能。其实，这两个插件也只是两个容器镜像而已，所以 kubeadm 只要用 Kubernetes 客户端创建两个 Pod 就可以了。



## kubeadm join 的工作流程

这个流程其实非常简单，kubeadm init 生成 bootstrap token 之后，你就可以在任意一台安装了 kubelet 和 kubeadm 的机器上执行 kubeadm join 了。

可是，为什么执行 kubeadm join 需要这样一个 token 呢？

因为，任何一台机器想要成为 Kubernetes 集群中的一个节点，就必须在集群的 kube-apiserver 上注册。可是，要想跟 apiserver 打交道，这台机器就必须要获取到相应的证书文件（CA 文件）。可是，为了能够一键安装，我们就不能让用户去 Master 节点上手动拷贝这些文件。

所以，kubeadm 至少需要发起一次“不安全模式”的访问到 kube-apiserver，从而拿到保存在 ConfigMap 中的 cluster-info（它保存了 APIServer 的授权信息）。而 bootstrap token，扮演的就是这个过程中的安全验证的角色。

只要有了 cluster-info 里的 kube-apiserver 的地址、端口、证书，kubelet 就可以以“安全模式”连接到 apiserver 上，这样一个新的节点就部署完成了。

接下来，你只要在其他节点上重复这个指令就可以了。















# 常用命令

```shell
# 镜像列表
sudo docker images

# 容器列表
sudo docker ps

# 删除镜像
sudo docker rmi eeb27ee6b893

# 后台运行镜像helloworld，将容器内的80端口映射到宿主机的4000端口
docker run -d -p 4000:80 helloworld

# 进入正在运行的容器（容器id：4ddf4638572d）
sudo docker exec -it 4ddf4638572d /bin/sh
# 在容器内部，退出容器
exit

# 查看正在运行的容器的进程id（容器id：7d9064ad84fc）
sudo docker inspect --format '{{ .State.Pid }}' 7d9064ad84fc

# 查看 kubelet 的日志
journalctl -f -u kubelet

# 查看集群中的全部节点
kubectl get nodes  

# 查看集群中名字为 node1 节点的详细状态
kubectl describe node node1


# 查看pods？？？
kubectl get pods -n kube-system

# 
kubectl get all --namespace=kubernetes-dashboard

#
kubectl get pods --all-namespaces

# 查看指定pod的详细信息
kubectl describe pod <pod-name> --namespace=kubernetes-dashboard

# 生成子节点加入主节点的token
kubeadm token create
# token列表
kubeadm token list
```





# 问题解决

## Ubuntu Linux下修改docker镜像源

解决用 Dockerfile 生成镜像时一直处于waiting状态的问题

```shell
# 增加 Docker 的镜像源配置文件 /etc/docker/daemon.json，如果没有配置过镜像该文件默认是不存的，在其中增加如下内容：
{
  "registry-mirrors": ["https://y0qd3iq.mirror.aliyuncs.com"]
}
```

