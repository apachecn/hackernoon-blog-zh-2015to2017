# 用于深度学习的 GPU 和 Kubernetes 第 1/3 部分

> 原文：<https://medium.com/hackernoon/gpus-kubernetes-for-deep-learning-part-1-3-d8eebe0dd6fe>

几周前，我分享了一个关于建立一个[DYI GPU 集群的附带项目，让 k8s](https://hackernoon.com/installing-a-diy-bare-metal-gpu-cluster-for-kubernetes-364200254187) 与 [Kubernetes](https://hackernoon.com/tagged/kubernetes) 一起玩，相对于 AWS g2 实例有一个适当的 ROI。

当 AWS 落后于旧的 nVidia K20s 卡(最新的驱动程序不再支持这些卡)时，这非常有趣。但是随着 [P 系列](https://aws.amazon.com/ec2/instance-types/#p2) (p2.xlarge，8xlarge 和 16xlarge)的加入，新卡是 k80，具有 12GB RAM，比以前的更强大。

百度刚刚在 Kubernetes 博客上发布了一篇关于 PaddlePaddle 设置的[帖子，但他们只关注了 CPU。我认为在 AWS 上观察 Kubernetes 添加一些](http://blog.kubernetes.io/2017/02/run-deep-learning-with-paddlepaddle-on-kubernetes.html) [GPU](https://hackernoon.com/tagged/gpu) 节点的设置会很有趣，然后在其上练习深度学习框架。医生说有可能…

这篇文章是 3 个系列的第一篇:设置 GPU 集群(这篇博客)，向 Kubernetes 集群添加存储(紧接着)，最后在集群上运行深度学习培训(正在进行，在 MWC 之后……)。

# 这个计划

在这篇博客中，我们将:

1.  以开发模式在 AWS 上部署 k8s(无 HA、共置 etcd、控制平面和 PKI)
2.  使用 GPU 部署 2 个节点(p2.xlarge 和 p2.8xlarge 实例)
3.  仅使用 CPU 部署 3 个节点(m4.xlarge)
4.  验证 GPU 可用性

# 要求

接下来，重要的是:

*   你了解 Kubernetes 101
*   您拥有 AWS 的管理员凭据
*   如果你关注了其他帖子，你就会知道我们将使用 Kubernetes 的 [Canonical 发行版，因此一些关于 Ubuntu、Juju 和 Canonical 生态系统的知识会有所帮助。](https://www.ubuntu.com/cloud/kubernetes)

# 前戏

*   确保你已经安装了 Juju。

在 Ubuntu 上，

```
sudo apt-add-repository ppa:juju/stable
sudo apt update
sudo apt install -yqq juju 
```

对于其他操作系统，查找[正式文档](https://jujucharms.com/docs/2.0/getting-started-general)

然后使用您的凭证连接到 AWS 云，请阅读[此页面](https://jujucharms.com/docs/2.0/help-aws)

*   最后复制这个回购协议，以获得所有的来源

```
git clone [https://github.com/madeden/blogposts](https://github.com/madeden/blogposts) ./
cd blogposts/k8s-gpu-cloud
```

好吧！让我们开始 GPU 化世界吧！

# 部署集群

## 助推器

像往常一样，从自举序列开始。请注意，p2 实例仅在美国西部-2、美国东部-1 和欧盟西部-2 以及美国政府地区可用。我在欧盟运行 p2 实例时遇到了问题，因此我建议使用美国地区。

```
juju bootstrap aws/us-east-1 — credential canonical — constraints “cores=4 mem=16G root-disk=64G” 
# Creating Juju controller “aws-us-east-1” on aws/us-east-1
# Looking for packaged Juju agent version 2.1-rc1 for amd64
# Launching controller instance(s) on aws/us-east-1…
# — i-0d48b2c872d579818 (arch=amd64 mem=16G cores=4)
# Fetching Juju GUI 2.3.0
# Waiting for address
# Attempting to connect to 54.174.129.155:22
# Attempting to connect to 172.31.15.3:22
# Logging to /var/log/cloud-init-output.log on the bootstrap machine
# Running apt-get update
# Running apt-get upgrade
# Installing curl, cpu-checker, bridge-utils, cloud-utils, tmux
# Fetching Juju agent version 2.1-rc1 for amd64
# Installing Juju machine agent
# Starting Juju machine agent (service jujud-machine-0)
# Bootstrap agent now started
# Contacting Juju controller at 172.31.15.3 to verify accessibility…
# Bootstrap complete, “aws-us-east-1” controller now available.
# Controller machines are in the “controller” model.
# Initial model “default” added.
```

## 部署实例

一旦控制器准备就绪，我们就可以开始部署服务了。在我之前的帖子中，我使用了*包*，这是部署复杂应用程序的快捷方式。

如果你已经熟悉 Juju，你可以运行`juju deploy src/k8s-gpu.yaml`并跳到这一节的末尾。对于其他对细节感兴趣的人，这次我们将手动部署，并检查部署的逻辑。

Kubernetes 由 5 个独立的应用程序组成:Master、Worker、法兰绒(网络)、etcd(集群状态存储数据库)和 easyRSA(加密通信并提供 x509 证书的 PKI)。
在 Juju 中，每个应用都由一个*符咒*建模，这是一个如何部署它的配方。

在部署时，您可以对 Juju 进行约束，可以是非常具体的(实例类型)，也可以是宽松的(内核数量)。对于后者，Juju 将选择最便宜的实例来匹配您在目标云上的约束。

首先是部署应用程序:

```
juju deploy cs:~containers/kubernetes-master-11 --constraints "cores=4 mem=8G root-disk=32G"
# Located charm "cs:~containers/kubernetes-master-11".
# Deploying charm "cs:~containers/kubernetes-master-11".
juju deploy cs:~containers/etcd-23 --to 0
# Located charm "cs:~containers/etcd-23".
# Deploying charm "cs:~containers/etcd-23".
juju deploy cs:~containers/easyrsa-6 --to lxd:0
# Located charm "cs:~containers/easyrsa-6".
# Deploying charm "cs:~containers/easyrsa-6".
juju deploy cs:~containers/flannel-10
# Located charm "cs:~containers/flannel-10".
# Deploying charm "cs:~containers/flannel-10".
juju deploy cs:~containers/kubernetes-worker-13 --constraints "instance-type=p2.xlarge" kubernetes-worker-gpu
# Located charm "cs:~containers/kubernetes-worker-13".
# Deploying charm "cs:~containers/kubernetes-worker-13".
juju deploy cs:~containers/kubernetes-worker-13 --constraints "instance-type=p2.8xlarge" kubernetes-worker-gpu8
# Located charm "cs:~containers/kubernetes-worker-13".
# Deploying charm "cs:~containers/kubernetes-worker-13".
juju deploy cs:~containers/kubernetes-worker-13 --constraints "instance-type=m4.2xlarge" -n3 kubernetes-worker-cpu
# Located charm "cs:~containers/kubernetes-worker-13".
# Deploying charm "cs:~containers/kubernetes-worker-13".
```

这里您可以看到 Juju 中一个有趣的属性，我们以前从未接触过:命名您部署的服务。我们两次部署了相同的 kubernetes-worker charm，但两次使用了 GPU，另一次没有。这为我们提供了一种对某一类型的实例进行分组的方法，代价是要复制一些命令。

还要注意我们部署的 charms 中的修订号。修订并不直接与他们部署的软件版本紧密相关。如果你忽略了它们，Juju 会选择最新的版本，就像 Docker 对图片做的那样。

## 添加关系和公开软件

现在应用程序已经部署好了，我们需要告诉 Juju 它们是如何联系在一起的。例如，Kubernetes master 需要证书来保护其 API。因此，**kubernetes-master:certificates**和 **easyrsa:client** 之间存在关联。

这种关系意味着一旦两个应用程序连接起来，一些脚本将运行来查询 EasyRSA API 以创建所需的证书，然后将它们复制到 k8s 主机上的正确位置。

然后，这些关系在集群中创建状态，charms 可以对此做出反应。

本质上，非常高的层次，把 Juju 看作应用程序部署的发布-订阅实现。集群内部或外部的每个动作都向公共总线发送一条消息，charms 可以对这些消息做出反应并执行附加动作，修改整体状态……等等，直到达到平衡。

让我们添加关系:

```
juju add-relation kubernetes-master:certificates easyrsa:client
juju add-relation etcd:certificates easyrsa:client
juju add-relation kubernetes-master:etcd etcd:db
juju add-relation flannel:etcd etcd:db
juju add-relation flannel:cni kubernetes-master:cnifor TYPE in cpu gpu gpu8
do 
 juju add-relation kubernetes-worker-${TYPE}:kube-api-endpoint kubernetes-master:kube-api-endpoint
 juju add-relation kubernetes-master:cluster-dns kubernetes-worker-${TYPE}:kube-dns
 juju add-relation kubernetes-worker-${TYPE}:certificates easyrsa:client
 juju add-relation flannel:cni kubernetes-worker-${TYPE}:cni
 juju expose kubernetes-worker-${TYPE}
donejuju expose kubernetes-master
```

注意最后的**曝光**命令。
这些是 Juju 在云中为实例的特定端口打开防火墙的指令。有些是在 charms 中预定义的(Kubernetes 主 API 是 6443，Workers 为 ingresses 打开 80 和 443 ),但是如果需要，您也可以强制它们(例如，当您在部署后的实例中手动添加内容时)

## 添加 CUDA

CUDA 还没有正式的魅力(即将推出！！)，但是你可以在 [GitHub](https://github.com/SaMnCo/layer-nvidia-cuda) 上找到我的 demoware 实现。针对本文，它已更新到 CUDA 8.0.61 和驱动程序 375.26。

确保您有可用的魅力工具，克隆并构建 CUDA 魅力:

```
sudo apt install charm charm-tools# Exporting the ENV
mkdir -p ~/charms ~/charms/layers ~/charms/interfaces
export JUJU_REPOSITORY=${HOME}/charms
export LAYER_PATH=${JUJU_REPOSITORY}/layers
export INTERFACE_PATH=${JUJU_REPOSITORY}/interfaces# Build the charm
cd ${LAYER_PATH}
git clone [https://github.com/SaMnCo/layer-nvidia-cuda](https://github.com/SaMnCo/layer-nvidia-cuda) cuda
charm build cuda
```

这将在 JUJU_REPOSITORY 中创建一个名为 **builds** 的新文件夹，并在那里创建另一个名为 **cuda** 的文件夹。

现在你可以施展魅力了

```
juju deploy --series xenial $HOME/charms/builds/cuda
juju add-relation cuda kubernetes-worker-gpu
juju add-relation cuda kubernetes-worker-gpu8
```

这将需要相当长的时间，因为 CUDA 的安装时间很长(CDK 大约需要 10 分钟，而 CUDA 可能需要 15 分钟)。

然而，最后状态应该显示:

```
juju status
Model    Controller     Cloud/Region   Version
default  aws-us-east-1  aws/us-east-1  2.1-rc1App                     Version  Status       Scale  Charm              Store       Rev  OS      Notes
cuda                             active           2  cuda               local         2  ubuntu  
easyrsa                 3.0.1    active           1  easyrsa            jujucharms    6  ubuntu  
etcd                    2.2.5    active           1  etcd               jujucharms   23  ubuntu  
flannel                 0.7.0    active           6  flannel            jujucharms   10  ubuntu  
kubernetes-master       1.5.2    active           1  kubernetes-master  jujucharms   11  ubuntu  exposed
kubernetes-worker-cpu   1.5.2    active           3  kubernetes-worker  jujucharms   13  ubuntu  exposed
kubernetes-worker-gpu   1.5.2    active           1  kubernetes-worker  jujucharms   13  ubuntu  exposed
kubernetes-worker-gpu8  1.5.2    active           1  kubernetes-worker  jujucharms   13  ubuntu  exposedUnit                       Workload     Agent      Machine  Public address  Ports           Message
easyrsa/0*                 active       idle       0/lxd/0  10.0.0.122                      Certificate Authority connected.
etcd/0*                    active       idle       0        54.242.44.224   2379/tcp        Healthy with 1 known peers.
kubernetes-master/0*       active       idle       0        54.242.44.224   6443/tcp        Kubernetes master running.
  flannel/0*               active       idle                54.242.44.224                   Flannel subnet 10.1.76.1/24
kubernetes-worker-cpu/0    active       idle       4        52.86.161.22    80/tcp,443/tcp  Kubernetes worker running.
  flannel/4                active       idle                52.86.161.22                    Flannel subnet 10.1.79.1/24
kubernetes-worker-cpu/1*   active       idle       5        52.70.5.49      80/tcp,443/tcp  Kubernetes worker running.
  flannel/2                active       idle                52.70.5.49                      Flannel subnet 10.1.63.1/24
kubernetes-worker-cpu/2    active       idle       6        174.129.164.95  80/tcp,443/tcp  Kubernetes worker running.
  flannel/3                active       idle                174.129.164.95                  Flannel subnet 10.1.22.1/24
kubernetes-worker-gpu8/0*  active       idle       3        52.90.163.167   80/tcp,443/tcp  Kubernetes worker running.
  cuda/1                   active       idle                52.90.163.167                   CUDA installed and available
  flannel/5                active       idle                52.90.163.167                   Flannel subnet 10.1.35.1/24
kubernetes-worker-gpu/0*   active       idle       1        52.90.29.98     80/tcp,443/tcp  Kubernetes worker running.
  cuda/0*                  active       idle                52.90.29.98                     CUDA installed and available
  flannel/1                active       idle                52.90.29.98                     Flannel subnet 10.1.58.1/24Machine  State    DNS             Inst id              Series  AZ
0        started  54.242.44.224   i-09ea4f951f651687f  xenial  us-east-1a
0/lxd/0  started  10.0.0.122      juju-65a910-0-lxd-0  xenial  
1        started  52.90.29.98     i-03c3e35c2e8595491  xenial  us-east-1c
3        started  52.90.163.167   i-0ca0716985645d3f2  xenial  us-east-1d
4        started  52.86.161.22    i-02de3aa8efcd52366  xenial  us-east-1e
5        started  52.70.5.49      i-092ac5367e31188bb  xenial  us-east-1a
6        started  174.129.164.95  i-0a0718343068a5c94  xenial  us-east-1cRelation      Provides                Consumes                Type
juju-info     cuda                    kubernetes-worker-gpu   regular
juju-info     cuda                    kubernetes-worker-gpu8  regular
certificates  easyrsa                 etcd                    regular
certificates  easyrsa                 kubernetes-master       regular
certificates  easyrsa                 kubernetes-worker-cpu   regular
certificates  easyrsa                 kubernetes-worker-gpu   regular
certificates  easyrsa                 kubernetes-worker-gpu8  regular
cluster       etcd                    etcd                    peer
etcd          etcd                    flannel                 regular
etcd          etcd                    kubernetes-master       regular
cni           flannel                 kubernetes-master       regular
cni           flannel                 kubernetes-worker-cpu   regular
cni           flannel                 kubernetes-worker-gpu   regular
cni           flannel                 kubernetes-worker-gpu8  regular
cni           kubernetes-master       flannel                 subordinate
kube-dns      kubernetes-master       kubernetes-worker-cpu   regular
kube-dns      kubernetes-master       kubernetes-worker-gpu   regular
kube-dns      kubernetes-master       kubernetes-worker-gpu8  regular
cni           kubernetes-worker-cpu   flannel                 subordinate
juju-info     kubernetes-worker-gpu   cuda                    subordinate
cni           kubernetes-worker-gpu   flannel                 subordinate
juju-info     kubernetes-worker-gpu8  cuda                    subordinate
cni           kubernetes-worker-gpu8  flannel                 subordinate
```

让我们看看 nvidia-smi 给了我们什么:

```
juju ssh kubernetes-worker-gpu/0 sudo nvidia-smi
Tue Feb 14 13:28:42 2017       
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 375.26                 Driver Version: 375.26                    |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Tesla K80           On   | 0000:00:1E.0     Off |                    0 |
| N/A   33C    P0    81W / 149W |      0MiB / 11439MiB |     95%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID  Type  Process name                               Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
```

在更强大的 8xlarge 上，

```
juju ssh kubernetes-worker-gpu8/0 sudo nvidia-smi
Tue Feb 14 13:59:24 2017       
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 375.26                 Driver Version: 375.26                    |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Tesla K80           On   | 0000:00:17.0     Off |                    0 |
| N/A   41C    P8    31W / 149W |      0MiB / 11439MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
|   1  Tesla K80           On   | 0000:00:18.0     Off |                    0 |
| N/A   36C    P0    70W / 149W |      0MiB / 11439MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
|   2  Tesla K80           On   | 0000:00:19.0     Off |                    0 |
| N/A   44C    P0    57W / 149W |      0MiB / 11439MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
|   3  Tesla K80           On   | 0000:00:1A.0     Off |                    0 |
| N/A   38C    P0    70W / 149W |      0MiB / 11439MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
|   4  Tesla K80           On   | 0000:00:1B.0     Off |                    0 |
| N/A   43C    P0    57W / 149W |      0MiB / 11439MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
|   5  Tesla K80           On   | 0000:00:1C.0     Off |                    0 |
| N/A   38C    P0    69W / 149W |      0MiB / 11439MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
|   6  Tesla K80           On   | 0000:00:1D.0     Off |                    0 |
| N/A   44C    P0    58W / 149W |      0MiB / 11439MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
|   7  Tesla K80           On   | 0000:00:1E.0     Off |                    0 |
| N/A   38C    P0    71W / 149W |      0MiB / 11439MiB |     39%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID  Type  Process name                               Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
```

是的。！我们有我们预期的 8 个 GPU，因此 8x 12GB = 96GB 视频内存！

在此阶段，我们只在主机上启用了它们。现在让我们在 Kubernetes 中添加 GPU 支持。

# 在 Kubernetes 中添加 GPU 支持

默认情况下，CDK 在启动 API 服务器和 Kubelets 时不会激活 GPU。我们需要手动操作(目前)。

## 主更新

在主节点上，更新**/etc/default/kube-API server**以添加:

```
# Security Context
KUBE_ALLOW_PRIV="--allow-privileged=true"
```

在重新启动 API 服务器之前。这可以通过编程实现:

```
juju show-status kubernetes-master --format json | \
    jq --raw-output '.applications."kubernetes-master".units | keys[]' | \
    xargs -I UNIT juju ssh UNIT "echo -e '\n# Security Context \nKUBE_ALLOW_PRIV=\"--allow-privileged=true\"' | sudo tee -a /etc/default/kube-apiserver && sudo systemctl restart kube-apiserver.service"
```

所以现在 Kube API 将接受运行特权容器的请求，这是 GPU 工作负载所需要的。

## 工作节点

在每个 worker 上， **/etc/default/kubelet** 来添加 GPU 标签，所以看起来像:

```
# Security Context
KUBE_ALLOW_PRIV="--allow-privileged=true"# Add your own!
KUBELET_ARGS="--experimental-nvidia-gpus=1 --require-kubeconfig --kubeconfig=/srv/kubernetes/config --cluster-dns=10.1.0.10 --cluster-domain=cluster.local"
```

在重新启动服务之前。

这可以通过

```
for WORKER_TYPE in gpu gpu8
do
    juju show-status kubernetes-worker-${WORKER_TYPE} --format json | \
        jq --raw-output '.applications."kubernetes-worker-'${WORKER_TYPE}'".units | keys[]' | \
        xargs -I UNIT juju ssh UNIT "echo -e '\n# Security Context \nKUBE_ALLOW_PRIV=\"--allow-privileged=true\"' | sudo tee -a /etc/default/kubelet"juju show-status kubernetes-worker-${WORKER_TYPE} --format json | \
    jq --raw-output '.applications."kubernetes-worker-'${WORKER_TYPE}'".units | keys[]' | \
    xargs -I UNIT juju ssh UNIT "sudo sed -i 's/KUBELET_ARGS=\"/KUBELET_ARGS=\"--experimental-nvidia-gpus=1\ /' /etc/default/kubelet && sudo systemctl restart kubelet.service"done
```

## 测试我们的设置

现在我们想知道集群是否真的启用了 GPU。要进行验证，请使用 nvidia-smi pod 运行作业:

```
kubectl create -f src/nvidia-smi.yaml
```

然后稍等片刻，运行 log 命令:

```
kubectl logs $(kubectl get pods -l name=nvidia-smi -o=name -a)
Tue Feb 14 14:14:57 2017       
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 375.26                 Driver Version: 375.26                    |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Tesla K80           Off  | 0000:00:17.0     Off |                    0 |
| N/A   47C    P0    56W / 149W |      0MiB / 11439MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
|   1  Tesla K80           Off  | 0000:00:18.0     Off |                    0 |
| N/A   39C    P0    70W / 149W |      0MiB / 11439MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
|   2  Tesla K80           Off  | 0000:00:19.0     Off |                    0 |
| N/A   48C    P0    57W / 149W |      0MiB / 11439MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
|   3  Tesla K80           Off  | 0000:00:1A.0     Off |                    0 |
| N/A   41C    P0    70W / 149W |      0MiB / 11439MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
|   4  Tesla K80           Off  | 0000:00:1B.0     Off |                    0 |
| N/A   47C    P0    58W / 149W |      0MiB / 11439MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
|   5  Tesla K80           Off  | 0000:00:1C.0     Off |                    0 |
| N/A   40C    P0    69W / 149W |      0MiB / 11439MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
|   6  Tesla K80           Off  | 0000:00:1D.0     Off |                    0 |
| N/A   48C    P0    59W / 149W |      0MiB / 11439MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
|   7  Tesla K80           Off  | 0000:00:1E.0     Off |                    0 |
| N/A   41C    P0    72W / 149W |      0MiB / 11439MiB |    100%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID  Type  Process name                               Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
```

that 在这里感兴趣的是，pod 可以看到所有的卡，即使我们只共享了 **/dev/nvidia0** char 设备。在运行时，我们会遇到问题。
如果你想运行多个 GPU 容器，你需要像我们在第二个 yaml 文件(nvidia-smi-8.yaml)中所做的那样共享所有的 char 设备

# 结论

我们到达了我们 3 部分旅程的第一个里程碑:集群启动并运行，GPU 被激活，Kubernetes 现在将欢迎 GPU 工作负载。

如果您是一名数据科学家或运行 Kubernetes 工作负载，可以受益于 GPU，这已经为您提供了一种优雅而快速的管理设置的方式。但是通常在这种情况下，您还需要在实例之间有可用的存储，无论是为了共享数据集还是为了交换结果。

Kubernetes 提供了许多连接储物的选项。在博客的第二部分，我们将看到如何自动添加 EFS 存储到我们的实例，然后把它很好地用于一些数据集！

同时，如果您在云中有具体的使用案例，请随时联系我，讨论操作细节。我很乐意帮助你建立自己的 GPU 集群，让你开始学习科学！

# 拆卸

只要你愿意，你可以拆掉这个集群。这些实例可能很昂贵，因此在不使用它们时关闭它们并不是一个坏主意。

```
juju kill-controller aws/us-east-1
```

这将要求确认，然后摧毁一切…但现在，你只是几个命令和咖啡远离重建它，所以这不是一个问题。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！