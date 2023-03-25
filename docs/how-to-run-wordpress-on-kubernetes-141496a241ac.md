# 如何在 Kubernetes 上运行 WordPress

> 原文：<https://medium.com/hackernoon/how-to-run-wordpress-on-kubernetes-141496a241ac>

![](img/02b52cd1779740505c8470fb887cd09d.png)

本指南涵盖了以最简单的方式创建 K8s 卷和配置带有 MySQL 数据库的 WP 实例——对于新手来说是对 [Kubernetes](https://hackernoon.com/tagged/kubernetes) 的完美介绍。

它是关于[**dockering**](https://buddy.works/guides/wordpress-docker-kubernetes-part-1)和 [**自动化**](https://buddy.works/guides/wordpress-docker-kubernetes-part-2) **[WordPress](https://hackernoon.com/tagged/wordpress) 项目的交付** 的文章的直接后续，允许您将 Docker 的强大功能引入您的开发设置。现在是时候在一群谷歌驱动的节点上释放它了。

# 在 K8s 集群中配置持久卷

由于群集中只有一个节点，我们将使用一个`hostPath`卷。这种类型的卷将路径从节点的文件系统挂载到 K8s 中。

# 创建卷. yml

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: local-pv-1
  labels:
    type: local
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /k8/volume/pv-1
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: local-pv-2
  labels:
    type: local
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /k8/volume/pv-2
```

此配置将在节点路径中创建两个读/写 10GB 卷:

```
/k8/volume/pv-1
/k8/volume/pv-2
```

# 创建您的 K8s 卷

要创建卷，请运行

```
kubectl apply -f volumes.yml
```

您可以运行下面的命令来检查一切是否正确:

```
kubectl get pv

NAME       CAPACITY ACCESSMODES RECLAIMPOLICY STATUS CLAIM STORAGECLASS REASON AGE
local-pv-1 10Gi     RWO         Retain        Available                        13s
local-pv-2 10Gi     RWO         Retain        Available                        13s
```

# 喜欢你读的书吗？[点击此处查看完整指南！](https://buddy.works/guides/how-run-wordpress-on-kubernetes)

![](img/237fecb458d889482966108f09dae68f.png)

其他职位:

## 1.[如何对 WordPress 站点进行分类](/@BuddyWorks/how-to-dockerize-wordpress-sites-part-1-8bf6aa131d1)

## 2.[如何用 Docker 和 Buddy 实现 WordPress 交付自动化](/@BuddyWorks/how-to-automate-wordpress-delivery-with-docker-and-buddy-part-2-ddd5f80f813a)