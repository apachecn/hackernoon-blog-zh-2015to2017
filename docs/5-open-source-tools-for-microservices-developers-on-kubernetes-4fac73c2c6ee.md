# Kubernetes 上面向微服务开发者的 5 款开源工具

> 原文：<https://medium.com/hackernoon/5-open-source-tools-for-microservices-developers-on-kubernetes-4fac73c2c6ee>

Kubernetes 已经成为运行多容器应用程序(又名微服务)的事实上的标准。Kubernetes 具有强大的容器调度、服务发现、负载平衡等功能。但是在 Kubernetes 上开发软件的开发者呢？这里列出了开发人员使用 Kubernetes 构建应用程序的五个便利工具，以及使用它们的一些技巧。

# 库贝特尔

kubectl 是 Kubernetes 的标准命令行界面。您将需要 kubectl 来部署一个容器，找出它不运行的原因，等等。一些方便的`kubectl`命令包括:

*   `kubectl explain`提供 Kubernetes 资源的完整列表，从 pod 到 secrets 到 statefulsets。
*   `kubectl describe <resourcetype> <resource>`给出给定资源的大量状态信息。

由于资源名称往往很长(例如 kubernetes-node-em t8 . c . my project . internal)，所以花几分钟[在您的 shell 中设置制表符结束](https://kubernetes.io/docs/tasks/tools/install-kubectl/#enabling-shell-autocompletion)以节省您的大量输入。

# Kubernetes 小抄

所以它不完全是一个工具，但是 Kubernetes 备忘单有一个使用 Kubernetes 的有用命令的完整列表。把它做成书签，打印出来，保存在方便的地方。

# 远程呈现

厌倦了仅仅为了测试两行代码的更改而等待容器部署吗？虽然有几种不同的方法来为微服务设置[您的开发环境，但是](https://www.datawire.io/guide/development/development-environments-microservices/)[网真](https://www.telepresence.io/)可以让您采用混合模型。在这个模型中，您在笔记本电脑上本地编码服务，同时通过双向代理连接到 Kubernetes 集群中的其他服务。

借助网真，您在本地运行的服务:

*   拥有对 Kubernetes 环境变量、配置映射、机密等的完全访问权限
*   可以与集群上运行的服务进行通信
*   集群上运行的服务可以与本地运行的实例通信

不用说，不建议在生产中使用。但是在开发中，您可以:

*   编辑您的代码，并立即(没有部署！)测试所有服务的变化
*   运行一个对本地文件系统有完全访问权的 shell(只需键入`telepresence`)，这样您就可以在笔记本电脑上使用任何工具或脚本
*   在服务上运行调试器或探查器

# 迷你库贝

在没有 WiFi 但仍需要编码的飞机上？没问题。 [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) 允许您在笔记本电脑上运行 Kubernetes 实例。

另外，对于 Linux 用户来说，一个不太为人所知的事实是:[您可以在没有 VM 的情况下运行 minikube](https://github.com/kubernetes/minikube#linux-ci-installation-which-supports-running-in-a-vm-example-w-kubectl-installation)——如果您正在设置一个持续集成环境，这很方便。

# 卷曲

事实上，任何服务都会有一个 API。 [curl](https://curl.haxx.se/) 允许您从命令行发送和接收 HTTP 请求。如果你正在测试一个服务，`curl -v`会给你一组关于 HTTP 交互的信息。如果你的 API 使用 gRPC，curl 也支持带有 `[--http2](http://curl.haxx.se/docs/http2.html)` [标志](http://curl.haxx.se/docs/http2.html)的 [HTTP/2，尽管你需要在](http://curl.haxx.se/docs/http2.html) [Mac OS X](https://simonecarletti.com/blog/2016/01/http2-curl-macosx/) 上重新编译它。

顺便提一下，Kubernetes 备忘单为您提供了一个在集群上获得交互式 shell 的命令(`kubectl run -i --tty busybox --image=busybox -- sh)`)。然而，该命令使用了`curl`中没有的`busybox`图像！您可以通过 Telepresence(假设您的本地机器上安装了 curl)或者使用包含 curl 的不同 Linux 映像(例如`kubectl run -i tty archlinux --image=base/archlinux -- sh`)来解决这个问题。

# 结论

在 Kubernetes 上开发多容器应用程序，除了旧工具之外，还需要新工具。这些是我们在 Kubernetes 上构建云应用时发现有用的一些工具。如果您使用过任何其他有用的工具，请随时给我发电子邮件 [richard.li@datawire.io](mailto:richard.li@datawire.io) 。