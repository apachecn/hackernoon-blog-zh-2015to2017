# Kubernetes 的优雅关闭

> 原文：<https://medium.com/hackernoon/graceful-shutdown-in-kubernetes-435b98794461>

我一直在研究如何在 [Kubernetes](https://hackernoon.com/tagged/kubernetes) 中优雅地关闭 HTTP 服务。令人惊讶的是，我在这个话题上发现了相互矛盾的观点，所以我决定自己做测试。如果你对结果感兴趣，请继续阅读。

# 研究

在研究不同的关闭方法之前，让我们看看 Kubernetes 官方文档对这个主题是怎么说的[2]。以下是该文档的摘录(我跳过了我认为不相关的部分):

```
1\. User sends command to delete Pod, with default grace period (30s)
3\. Pod shows up as “Terminating” when listed in client commands
6\. The processes in the Pod are sent the TERM signal.
7\. (simultaneous with 3), Pod is removed from endpoints list for service, and are no longer considered part of the set of running pods for replication controllers. Pods that shutdown slowly can continue to serve traffic as load balancers (like the service proxy) remove them from their rotations.
```

读到这里，看起来有可能在收到一个`SIGTERM`之后有新的请求进来(在 6 和 7 之间)。这意味着如果进程只是监听`SIGTERM`并在接收时退出，一些请求将不会被服务。

这还不够清楚，所以我开始研究。我偶然发现的第一个帖子[1]提出了以下流程:

```
1\. Process receives SIGTERM.
2\. Process returns 5XX response for (failureThreshold * periodSeconds) to its readiness probe, so it will be unregistered from the list of valid endpoints.
3\. Process closes its listener, waits for all ongoing requests to finish and exists.
```

这种方法表明，进程应该通过就绪探测失败来通知它正在退出。期望一个进程知道它正在运行的底层基础设施似乎很奇怪。更有甚者，看了评论更添困惑。有一个注释指出，通知您的进程正在退出不需要**而需要**，但是确实有一些请求会在`SIGTERM`被发送后被路由到容器。确实很奇怪。我们继续挖吧。

我发现了一个关于正常关机的 Stackoverflow 问题[3]，答案如下。

```
I ran some experiments to find out exactly what happens.The pod will briefly (<1s) continue receiving requests after shutdown is initiated, so you need to either catch SIGTERM or install a preStop hook so you can wait for them (and finish serving current requests).However, once the shutdown has been initiated, the readiness probe no longer matters, you don't need to change its state to stop receiving requests. (But before that a failing readiness probe will lead to your pod receiving no more traffic.)
```

更多的研究只是产生了更多的结果，表明这两种方法，使事情更加不清楚。在这一点上，我决定我必须自己做一些实验。

# 实验

我想检查的第一件事是请求在`SIGTERM`之后是否被路由到容器。最后，我使用 minikube[6]构建了一个本地集群，并部署了 test Go 程序(名为`kuber`)，该程序在获得`SIGTERM`后对所有收到的请求进行计数。它是通过服务和入口公开的。

我用`ab`(Apache HTTP server 基准测试[工具](https://hackernoon.com/tagged/tool)运行了一个测试，当测试运行时，我删除了服务请求的 pod。

```
ab -n 10000 -c 10 [https://192.168.99.100/api/info](https://192.168.99.100/api/info) & sleep 3 && kubectl delete pods/kuber-2370255394-v93lk
```

这是测试程序的输出:

```
$ kubectl logs -f kuber-2263169569-r527k
2017/05/07 18:26:47 Serving...
2017/05/07 18:27:24 Shutting down due to terminated
2017/05/07 18:27:39 Requests served after shutdown signal: 2216
2017/05/07 18:27:39 Exiting
```

事实证明，在收到关机信号后，确实有可能得到请求。在停止 HTTP 服务器之前的 15 秒钟等待期间，服务了 2000 多个请求。

然而，我没有发现程序在实际退出之前等待的时间与请求数量之间有任何关联。我测试的下一步是使用就绪探针。我增强了测试程序，所以一旦收到终止信号，它就开始返回`503 Service Unavailable`。这也没有改变行为。

# 未来的工作

上面的测试符合在单节点 Kubernetes 设置中使用`kubectl`删除单个 pod 的情况。当一个节点被终止时，测试在多节点 Kubernetes 设置中会发生什么是一个好主意。关于停止将请求路由到关闭 pod 所需的时间，这可能会产生不同的结果。

# 外卖食品

以下是我的实验中一些明显和不太明显的收获

*   确保您的进程确实收到了终止信号。如果您使用一个 shell 命令来启动它，例如`/bin/sh -c myapp`，请确保您使用的 shell(本例中为`sh`)正在向您的程序发送`TERM`信号。比如在 Alpine 上，`/bin/sh`不转发信号。
*   在关闭 HTTP 服务器之前添加一些宽限期，以避免失败的请求。
*   如果应用程序在`SIGTERM`后未能通过准备就绪检测，这不会有任何影响，但拥有这种机制不会有什么坏处，实际上可能是正确的做法。这可能取决于您正在使用的负载平衡层的实现。
*   如果你正在使用 Go，你应该订阅`syscall.SIGTERM`而不是`os.Interrupt`。

如果你对这个话题有任何经验，请在下面的评论区分享，这样这种困惑就一劳永逸地被赶走了。

## 来源

[1][https://blog . rising stack . com/graceful-shut down-node-js-kubernetes/](https://blog.risingstack.com/graceful-shutdown-node-js-kubernetes/)

[2][https://kubernetes . io/docs/concepts/workloads/pods/pod/# termination-of-pods](https://kubernetes.io/docs/concepts/workloads/pods/pod/#termination-of-pods)

[3][http://stack overflow . com/questions/40545581/do-kubernetes-pods-still-receive-requests-after-receiving-sigterm](http://stackoverflow.com/questions/40545581/do-kubernetes-pods-still-receive-requests-after-receiving-sigterm)

[4][http://stack overflow . com/questions/31574038/how-can-I-ensure-scaling-in-kubernetes](http://stackoverflow.com/questions/31574038/how-can-i-ensure-graceful-scaling-in-kubernetes)

[5][http://www . bite-code . com/2015/07/27/implementing-graceful-shut down-for-docker-containers-in-go-part-2/](http://www.bite-code.com/2015/07/27/implementing-graceful-shutdown-for-docker-containers-in-go-part-2/)

[6]https://github.com/kubernetes/minikube

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！