# 将 Flogo 应用部署到 Kubernetes

> 原文：<https://medium.com/hackernoon/deploying-flogo-apps-to-kubernetes-43a74742e323>

![](img/f87bb8f0ec1d92bdd9806443ac6527a9.png)

借助 [Project Flogo](https://flogo.io) ，您可以可视化地创建超轻边缘微服务，并在任何地方运行它们。但是，如果您想使用最强大的容器管理平台之一 [Kubernetes](https://hackernoon.com/tagged/kubernetes) 来运行那些极其轻量级的微服务，该怎么办呢？

# **先决条件**

如 Kubernetes 网站所述

> Kubernetes 是一个用于自动化部署、扩展和管理容器化应用程序的开源系统。它将组成应用程序的容器分组到逻辑单元中，以便于管理和发现。

如果你还没有建立自己的 Kubernetes 集群，我绝对推荐你看看 [minikube](https://github.com/kubernetes/minikube) 。该团队做出了惊人的努力，以最小的安装工作量，使在本地运行您自己的集群变得非常容易。

由于 Kubernetes 是针对容器化应用的，这意味着我们必须从我们的 [Flogo](https://hackernoon.com/tagged/flogo) 应用创建一个 Docker 映像，并将其推送到 Kubernetes 集群可访问的注册表中。在下面的例子中，我将使用 Docker Cloud，但是根据您的喜好，您可以选择任何容器注册表。

# **Flogo 应用**

由于这篇文章更多的是关于在 Kubernetes 上运行应用程序，而不是如何创建应用程序，我只是使用了 [Flogo 文档](https://tibcosoftware.github.io/flogo/getting-started/quickstart/)中的教程。这个应用程序有一个简单的 HTTP 接收器监听端口 *8080* 并发回一个默认字符串。如果你想使用不同的应用程序，当然也是可以的！

# **创建一个 Docker 图像**

Flogo 将自己描述为一个 *_Ultralight Edge 微服务框架 _* ，因此将使用它构建的应用程序容器化应该不会增加太多开销。幸运的是，今天你有一大堆小型基础容器可用，从 [alpine](https://hub.docker.com/_/alpine/) 到 debian(还有 [jessie-slim](https://hub.docker.com/r/library/debian/tags/jessie-slim/) )。我最喜欢的三个是:

```
$ docker imagesdebian jessie-slim a870c469749c 10 days ago 79.1MBalpine latest 053cde6e8953 11 days ago 3.97MBbitnami/minideb latest c5693017e0d4 3 weeks ago 53.6MB
```

我有一个在 Linux 上运行的应用程序，大约 7.4MB，因为我想尽可能降低开销，所以我将使用 *alpine* 来运行这个程序。将 alpine 与我的 Flogo 应用程序结合起来，应该会产生大约 12MB 的图像，我认为这已经很不错了。为了构建图像，我们需要一个 Dockerfile 文件:

```
# Dockerfile for flogoapp# VERSION 0.0.1# The FROM instruction initializes a new build stage and sets the Base Image for subsequent instructions.# We’re using alpine because of the small sizeFROM alpine# The ADD instruction copies new files, directories or remote file URLs from <src> and adds them to the filesystem of the image at the path <dest>.# We’ll add the flogoapp, built using the Web UI, to the working directoryADD flogoapp.dms .# The EXPOSE instruction informs Docker that the container listens on the specified network ports at runtime.# The app we’re using listens on port 8080 by defaultEXPOSE 8080# The main purpose of a CMD is to provide defaults for an executing container.# In our case we simply want to run the appCMD ./flogoapp.dms
```

要在此基础上构建应用程序，您只需运行以下命令:

```
docker build . -t <your username>/flogoalpine
```

在我的例子中，它以一个非常小的图像结束，大概是我期望的大小！

```
REPOSITORY TAG IMAGE ID CREATED SIZEretgits/flogoalpine latest e7bc672e009e About an hour ago 11.7MB
```

如前所述，我将利用 [Docker Cloud](https://cloud.docker.com) 来推送我的图像，以便 Kubernetes 集群可以访问它们。一个简单的命令就可以让图像可用:-)

```
docker push <your username>/flogoalpine
```

这就是码头工人的部分，让我们去 Kubernetes！

# **创建“部署”**

Kubernetes 中的*部署*是一个控制器，它为 pod 和副本集提供声明性更新。从本质上说，它让您能够公开更新您的应用程序，这意味着零停机时间！

示例“deployment.yaml”文件可能如下所示。这将在 Kubernetes 上创建一个部署，其中有一个副本(因此我们的应用程序的一个实例正在运行),其中容器的名称为“flogoapp ”,它将提取“T8”作为要运行的容器。请特别注意“containerPort ”,因为这将确保可以从外部访问该端口(尽管仍在群集内)

```
apiVersion: extensions/v1beta1kind: Deploymentmetadata: name: flogoapp-deploymentspec: replicas: 1 template: metadata: labels: app: flogoapp spec: containers: - name: flogoapp image: <image name> imagePullPolicy: Always ports: - containerPort: 8080
```

现在创建一个部署，您可以运行

```
kubectl create -f deployment.yaml
```

在 kubectl cli 工具中，或者使用仪表板，您可以看到您的部署状态:

```
$ kubectl get deploymentsNAME DESIRED CURRENT UP-TO-DATE AVAILABLE AGEflogoapp-deployment 1 1 1 1 50m
```

我们的应用正在运行！现在，我们需要确保我们也可以从外部访问它…

# **创建一个“服务”**

Kubernetes 文档对为什么需要*服务*有很好的解释，所以我让[他们](https://kubernetes.io/docs/concepts/services-networking/service)来讲述这个故事

> 库伯内特豆荚是会死的。他们出生，当他们死去，他们不会复活。复制控制器特别是动态地创建和销毁 pod(例如，当放大或缩小或者当进行滚动更新时)。虽然每个 Pod 都有自己的 IP 地址，但即使是这些 IP 地址也不能长期保持稳定。这就产生了一个问题:如果 Kubernetes 集群中的一组 Pods(让我们称之为后端)向其他 Pods(让我们称之为前端)提供功能，那么这些前端如何发现并跟踪该组中的后端呢？

因此，这些服务在逻辑上将 pod 分组在一起，并确保即使当 pod 消失时，您也不必更改 IP 地址。一个服务可以有许多不同的功能和更多的配置选项，所以让我们创建一个相当简单的服务。

下面的“service.yaml”文件简单地定义了服务“flogoapp ”,该服务将我们已经部署的应用程序的端口 8080 直接绑定到端口 30061，我们可以从集群外部访问该端口。

```
apiVersion: v1kind: Servicemetadata: name: flogoapp labels: app: flogoappspec: selector: app: flogoapp ports: - port: 8080 protocol: TCP nodePort: 30061 type: LoadBalancer
```

要在 Kubernetes 中创建服务，只需运行:

```
kubectl create -f service.yaml
```

在 kubectl cli 工具中，或者使用仪表板，您可以看到服务的状态，就像您的部署一样:

```
$ kubectl get servicesNAME TYPE CLUSTER-IP EXTERNAL-IP PORT(S) AGEflogoapp LoadBalancer 10.0.0.110 <pending> 8989:30061/TCP 1hkubernetes ClusterIP 10.0.0.1 <none> 443/TCP 1d
```

这也负责将应用程序暴露在集群之外。所以我们还有最后一项任务！

# **访问您的应用程序！**

现在访问应用程序非常简单。首先，我们需要 Kubernetes 集群的外部 IP 地址。如果你正在运行 minikube，你可以通过运行“minikube ip”来获得。使用 cURL，您现在可以从应用程序调用 API，并查看应用程序的内部 Flogo ID。

```
$ curl [http://192.168.99.100:30061/helloworld](http://192.168.99.100:30061/helloworld){“id”:”006257ffaf5fb1e9621914dcd0203af8"}
```

# **结论**

我们采用了一个简单的 Flogo 应用程序，将该应用程序添加到 Docker 容器中，并将其部署到 Kubernetes。Flogo 本身非常强大和轻便。结合 Kubernetes 的强大功能和灵活性，您可以在一个非常酷且强大的平台上运行超轻微服务。如果你想尝试 Flogo 项目，请访问我们的[网页](http://www.flogo.io)或 GitHub [项目](https://github.com/tibcosoftware/flogo)。