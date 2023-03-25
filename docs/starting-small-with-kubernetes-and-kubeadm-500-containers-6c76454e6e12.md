# 从 Kubernetes 和 Kubeadm 开始:500 个容器？

> 原文：<https://medium.com/hackernoon/starting-small-with-kubernetes-and-kubeadm-500-containers-6c76454e6e12>

![](img/22847a06c06fca8668102a43a024e2d6.png)

Photo by [Thomas Kvistholt](http://unsplash.com/photos/oZPwn40zCK4?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

在 Vurt，我们正在用 Kubernetes 进行一些内部实验。在这篇测试和文章中，我们专门讨论了 kubeadm 作为安装程序，以及在裸机服务器上运行 k8s 节点。哦，还有 ZFS。

我们认为 Kubeadm 是一个重要的项目。Vurt 的团队在运营 OpenStack 云方面拥有丰富的经验，我们发现 k8s 社区在运营决策方面与 OpenStack 社区在历史上的做法存在明显差异。

这并不是说一个或另一个是正确的，而是有不同的选择可以做。

## **OpenStack:没有默认部署选项**

OpenStack 明确选择不支持某个特定的部署机制。所以有很多，而且一直都有新的。例如 [OpenStack Helm](https://git.openstack.org/cgit/openstack/openstack-helm/) 是一个最近创建的项目，将使用 Helm 和 Kubernetes 部署 OpenStack 云的控制平面。OpenStack 大概有五六个主要的安装和管理系统。

长话短说，这是选项#1: *没有默认部署系统*。相反，我们得到了许多“安装程序”，反过来，关于如何部署 OpenStack 的观点有些支离破碎。(我们甚至不要进入第 2 天的操作。)但是这个帖子不是关于 OpenStack 的，是关于 k8s 和 kubeadm 的。

## Kubernetes: Kubeadm

另一方面，Kubernetes 已经决定实现一个默认的部署机制: [kubeadm](https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/) 。当然还有许多其他的“发行版”和“安装程序”，但是 kubeadm 是一个真实的东西。

> 在 SIG-cluster-lifecycle，我们在过去的几个月里一直在努力开发 kubeadm，这是一个使 Kubernetes 安装起来非常容易的工具。我们从用户那里听说安装 Kubernetes 比它应该的要难，我们希望人们专注于编写伟大的分布式应用程序，而不是与基础设施争吵！— [k8s 博客](http://blog.kubernetes.io/2016/09/how-we-made-kubernetes-easy-to-install.html)

(这篇博文的一个重要部分是他们提到 kubeadm 不会提供服务器，即它将在一些现有的 Linux 服务器上部署 k8s。所以团队决定稍微限制一下范围。)

基本上，k8s 社区已经决定将有一个默认的安装程序作为 k8s 核心代码的一部分。这是一个有趣且潜在强大的决定。

## 尝试部署 500 个容器

在我们的一个测试中，我们决定在四台服务器上使用 kubeadm 执行 k8s 的快速部署:一台虚拟机和三个裸机节点。虚拟机和网络由 OpenStack 管理，目前物理节点自动部署在 OpenStack 之外。物理服务器必须在绑定中配置 1GB 网卡。

至于计算资源，裸机节点有 128GB 的内存和 32 个 CPU。虚拟机将充当 k8s 主机，它只有 4GB 内存和 2 个 CPU。我们的目标是部署 500 个集装箱。

值得注意的是，这个测试并不仅仅是让 500 个容器运行起来，它更多的是探索 kubeadm 做什么，以及我们可以用它的默认设置做什么，而不是“挑战”硬件和软件的极限。我们并不期望能够在不做一些配置更改的情况下在三个节点上运行 500 个容器，但是我们想尝试一下！

裸机节点还有一个 SSD(用于缓存)和一个配置了 ZFS 的 SATA 驱动器。此外，Docker 配置在节点上，并将 ZFS 作为映像支持引擎。如果可能的话，我们希望使用 ZFS 作为我们的默认存储系统，因此我们正在评估将 Linux 上的 ZFS(ZoL)用于 Docker/Kubernetes。

```
ubuntu@bm-node-1:/var/log$ sudo zpool list
NAME           SIZE  ALLOC   FREE  EXPANDSZ   FRAG    CAP  DEDUP  HEALTH  ALTROOT
zpool-docker   840G   380M   840G         -     0%     0%  1.00x  ONLINE  -
```

由于使用了 ZFS，我们不得不运行 kube init，并选择跳过飞行前检查。这是警告通知。

```
[preflight] Some fatal errors occurred:
 unsupported graph driver: zfs
[preflight] If you know what you are doing, you can skip pre-flight checks with `--skip-preflight-checks`
```

同样，在这个测试中，我们使用了(某种程度上)默认的 Weave 网络插件。Weave 的安装非常简单！

```
ubuntu@kube-master:~$ kubectl apply -f [https://git.io/weave-kube-1.6](https://git.io/weave-kube-1.6)
serviceaccount "weave-net" created
clusterrole "weave-net" created
clusterrolebinding "weave-net" created
daemonset "weave-net" created
```

首先配置 kube-master 节点和网络，然后初始化裸机节点。

## 创建部署

一旦 k8s 启动并运行(感谢 kubeadm！)我们使用了一个 nginx 示例部署，并从几个 pod 开始。然后我们扩大规模，最初是 50 个，然后是 100 个……最后达到 500 个。

```
ubuntu@kube-master:~$ cat deployment.yaml 
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 500 # tells deployment to run 2 pods matching the template
  template: # create pods using pod definition in this template
    metadata:
      # unlike pod-nginx.yaml, the name is not included in the meta data as a unique name is
      # generated from the deployment name
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

在我们试图从 250 增加到 500 之前，这些扩展行动进行得非常顺利。我们遇到的障碍是，默认情况下，每个节点的 pod 数量限制为 110 个。

```
ubuntu@kube-master:/var/log$ kubectl get nodes -o json | jq '.items[] | {name: .metadata.name, cap: .status.capacity}'
{
  "name": "kube-master",
  "cap": {
    "cpu": "2",
    "memory": "4046412Ki",
    "pods": "110"
  }
}
{
  "name": "bm-node-1",
  "cap": {
    "cpu": "32",
    "memory": "131999476Ki",
    "pods": "110"
  }
}
{
  "name": "bm-node-2",
  "cap": {
    "cpu": "32",
    "memory": "131999472Ki",
    "pods": "110"
  }
}
{
  "name": "bm-node-3",
  "cap": {
    "cpu": "32",
    "memory": "131999636Ki",
    "pods": "110"
  }
}
```

查看 [kublet 文档](https://kubernetes.io/docs/admin/kubelet/)，有一个设置 pod 限制的选项。

```
--max-pods int32                                          Number of Pods that can run on this Kubelet. (default 110)
```

总的来说，我们对这次测试非常满意。在一个小时左右的时间里，我们在四个节点上部署了一个 k8s 集群，部署了一个软件定义的网络系统(Weave ),并且几乎不费吹灰之力就创建了 250 个容器。在未来的测试中，我们将改变每个节点的 pod 限制，看看会发生什么！当然，裸机节点可以运行更多，尤其是如果这些容器实际上什么也不做的话。

## 测试客户

我们目前正在寻找少量的测试客户加入我们的旅程，在托管的私有基础设施上进行高级 Kubernetes 部署。如果您的组织希望参与此事，请发送电子邮件至 vurt@vurtcloud.com 或使用我们的 [**联系表**](https://vurtcloud.com/contactus.html) **。**

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)