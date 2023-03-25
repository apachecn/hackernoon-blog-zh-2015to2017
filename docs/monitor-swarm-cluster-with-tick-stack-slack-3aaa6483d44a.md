# 使用 TICK 堆栈和 Slack 监控集群集群

> 原文：<https://medium.com/hackernoon/monitor-swarm-cluster-with-tick-stack-slack-3aaa6483d44a>

![](img/4c078b071e01a4904c58b28585f89106.png)

在本文中，我将向您展示如何建立一个开源时间序列平台来监控您的 **Docker Swarm** cluster &发送关于 **Slack** 的通知，以防异常检测。

我们监控堆栈的组件:

![](img/bfa7efd96efe8154a77952acb0b95369.png)

用于收集和报告指标的插件驱动的服务器代理。

![](img/a9a459c80d8a5c240c868f72d6e1d0bb.png)

用于度量、事件和实时分析的可扩展时间序列数据库。

![](img/05322ccfa677f5fcdc3f17719ea5c1d5.png)

用于在数据基础上构建图表的实时可视化工具。

![](img/c85132fc44f745f13e6a1f34975cc6c3.png)

时间序列数据的处理、监控和警报框架。

![](img/733f5797ea62331aafe12d661ac9d8de.png)

实时团队消息应用程序。

注意:这篇文章中使用的所有代码都在我的 [Github](https://github.com/mlabouardy/swarm-tick) 上。

**1 —群组设置**

如果您已经有了一个现有的 **Swarm 集群**，您可以跳过这一部分，如果没有使用以下脚本来设置一个具有 3 个节点的**Swarm**(1 个**管理器** & 2 个**工作器**):

发出以下命令:

> chmod +x setup.sh
> 。/setup.sh

上述命令的输出如下:

![](img/8c5f9112747c5dac73481ca4d2738f38.png)

**2 —堆栈设置**

创建完成后，通过 **SSH** 连接到您的**管理器节点**，并克隆以下存储库:

> https://github.com/mlabouardy/swarm-tick.git

![](img/5518c5bf0bad04fcfc6fee9b873baea6.png)

为了启动所有这些容器，我使用了 **docker-compose** :

发出以下命令来部署堆栈:

> docker stack deploy — compose 文件 docker-compose.yml tick

等待节点从 **DockerHub** 中提取图像:

![](img/c330c32c5314b1664c87e9633629368d.png)

拉出后，您应该会看到服务正在运行:

![](img/7d2d6ea328def7b1399dd5e40b231413.png)

在[**http://IP:8888**](http://IP:8888)(**chrono graf**Dashboard)上打开浏览器，正确配置数据源:

![](img/e1317a853e1490a18c5ee2044c808295.png)

**3 —系统使用仪表板**

点击“**创建仪表板**，并为仪表板指定一个名称:

![](img/9f701fb39ac499f4c68a4007fa3fd68b.png)

在添加图形之前，我们将使用一个叫做**仪表板模板变量**的概念，来创建动态的&交互式图形。在我们的度量查询中，我们将使用变量来代替像节点名和容器名这样的硬编码。因此，点击前面创建的仪表板顶部的“**模板变量**”。并且，创建一个名为 *:host:* 的变量，如下所示:

![](img/5905a4c99b762279144992535950d410.png)

注意:目前，还没有为用 **swarm 全局模式** ( [Github](https://github.com/moby/moby/pull/24973) )创建的服务设置主机名的解决方案。这就是为什么我们得到的是 id 列表而不是名字

您现在可以使用仪表板顶部的下拉菜单来选择*:主机:*模板变量的不同选项:

![](img/c405161decf56a4ccaffbd141efaeeb3.png)

现在是时候创建我们的第一个图表了，所以点击“**添加图表**按钮。

**3.1 —每个节点的内存使用量**

要创建查询，您可以使用**查询构建器**，或者，如果您已经熟悉 **InfluxQL** ，您可以在文本输入中手动输入查询:

> 从“vm_metrics”中选择 mean(“free”)作为“mean_free”，mean(“used”)作为“mean_used”，mean(“total”)作为“mean_total”。“自动发电机”。" mem_vm "其中 time >:dashboard time:AND " host " =:host:GROUP BY:interval:FILL(null)

![](img/c3d10bcc4025ca218a42fe96e4c13c9e.png)

我们的查询计算度量 *mem_vm* 中字段键 *free* 、 *used* 和 *total* 的平均值，并按照时间和节点名称对它们进行分组。

您可以通过点击“**选项**选项卡来更改图形类型、X 轴和 Y 轴格式:

![](img/f38d5c894d583e6f561cb24a9316cbe6.png)

仪表板上的一个可视化效果并不十分有趣，所以我添加了更多的图表来展示更多的可能性:

**3.2 —每个节点的 CPU 使用率**

> 从“vm_metrics”中选择 mean(“usage _ user”)作为“mean_usage_user”，mean(“usage _ system”)作为“mean_usage_system”。“自动发电机”。" cpu_vm "其中 time >:dashboard time:AND " host " =:host:GROUP BY:interval:FILL(null)

![](img/4751ce788afea5d439432ff0c37ffe4e.png)

**3.3 —每个节点的磁盘使用量**

> 从“vm_metrics”中选择 mean(“free”)作为“mean_free”，mean(“total”)作为“mean_total”，mean(“used”)作为“mean_used”。“自动发电机”。" disk_vm "其中 time >:dashboard time:AND " host " =:host:GROUP BY:interval:FILL(null)

![](img/63e7e20d7d3fbd4c8950a9fd9b4be9d1.png)

我们最终得到了这样一个漂亮的仪表板:

![](img/edf64de125adf1c32347cb502cbd4af2.png)

让我们创建另一个仪表板来监控在**集群**上运行的 **Docker 容器**。

**4 —群体服务仪表板**

创建第二个名为“ **Services** ”的仪表板，并创建一个模板变量来存储集群上运行的服务列表:

![](img/f01ac0892d353051ec8c3df9738487fa.png)

您可以按服务名过滤 now 指标:

![](img/b82e2c04dc86dd05514d5639c6ca2e48.png)

**4.1 —每个服务的内存使用量**

> 从“docker_metrics”中选择 mean(“usage _ percent”)作为“mean_usage_percent”。“自动发电机”。" docker_container_mem_docker "其中 time > :dashboardTime:和" com . docker . swarm . service . name " =:container:GROUP BY:interval:FILL(null)

![](img/f2803f0244d335863e72c27516f2f42b.png)

**4.2 —每项服务的 CPU 使用率**

> 从“docker_metrics”中选择 mean(“usage _ percent”)作为“mean_usage_percent”。“自动发电机”。" docker_container_cpu_docker "其中 time > :dashboardTime:和" com . docker . swarm . service . name " =:container:GROUP BY:interval:FILL(null)

![](img/98742667d9c26be85a5f021040332d14.png)

**4.3 —网络发送/接收**

> 从“docker_metrics”中选择 mean(“tx _ packets”)作为“mean_tx_packets”，mean(“rx _ packets”)作为“mean_rx_packets”。“自动发电机”。" docker_container_net_docker "其中 time > :dashboardTime:和" com . docker . swarm . service . name " =:container:GROUP BY:interval:FILL(null)

![](img/8c6cade7065b82c21904bc5337df4d5d.png)

**4.4 —每个服务的 IO 读/写**

> 从“docker_metrics”中选择 mean(“io _ serviced _ recursive _ write”)作为“mean_io_recursive_write_write”，mean(“io _ serviced _ recursive _ read”)作为“mean _ io _ serviced _ recursive _ read”。“自动发电机”。" docker_container_blkio_docker "其中 time > :dashboardTime:和" com . docker . swarm . service . name " =:container:GROUP BY:interval:FILL(null)

![](img/f0b7ba44abb787c5d8ca13cd9c22ef44.png)

结果:

![](img/c02229876705ac84df7b59eba73de66a.png)

注意:您可以更进一步，通过创建另一个模板变量，按运行服务的节点过滤指标:

![](img/042eeda40d13e8425db5d65c0a032c53.png)

让我们看看如果我们在 **Swarm** 上创建另一个服务会发生什么:

> docker service create-name API-constraint node . role = = worker-p 5000:5000 mlabouardy/books-API

如果您返回到 **Chronograf** ，您应该会看到服务已经自动添加到容器下拉列表中:

![](img/8b89a9082e83abe3023358cd677d112b.png)

就是这样！现在，您已经具备了使用 **Chronograf** 构建漂亮的数据可视化和仪表盘的基础。

Kapacitor 是拼图的最后一块。我们现在知道了如何存储、获取和读取指标，现在您需要对它们进行详细说明，以做一些类似警报或主动监控的事情。

所以在“**配置**选项卡上，点击“**添加配置**”:

![](img/4491229fa8028f9f6d47cce63beff10f.png)

添加新的 **Kapacitor** 实例如下，并启用 **Slack** :

![](img/86e66be8a0fb11cc6e52f79ae069a807.png)

注意:更新 **Slack** 频道& **Webhook URL** 以防你在本教程开始时没有更新 *kapacitor.conf* 文件。你可以通过进入这个[页面](https://api.slack.com/incoming-webhooks)获得一个 **Webhook URL** :

![](img/6aa41cd2dbf4a66a2fd4521d3f41e89a.png)

**5 —警报定义**

**5.1 —高内存利用率警报**

通过访问“**警报**”页面，导航至“**规则配置”**页面，并单击右上角的“**构建规则**”按钮:

![](img/5237531a11981b427c8e708d7cb19960.png)

如果**内存**使用率超过 **60%** ，我们将触发警报:

![](img/2e0c85541fb5385a5cbf6819ceeae8fd.png)

接下来，我们选择 **Slack** 作为事件处理器，并配置警告消息:

![](img/4121ea628257932e41579a3de7e474ec.png)

注意:如果您在初始**松弛**配置中指定了默认通道，则不需要在**警告消息**部分包含一个**松弛**通道。

**5.2 —高 CPU 利用率警报**

创建第二个规则，在 **CPU** 使用率超过 **40%** 时触发警报:

![](img/7b132a4938679a11f6178752ca8c0ad3.png)

警报端点:

![](img/1c494750ffedc989acaa2168432dbf61.png)

保存规则，就万事俱备了！

![](img/7bd0f3cd6b9ce8fd880b56b8ade0fae0.png)

现在我们的警报规则已经定义好了，让我们通过在集群上创建一些负载来测试它们。

**6 —压力测试**

我用了 **stress** ，一个生成工作量的工具。它会产生 CPU、内存、I/O 和磁盘压力。

**6.1 —给 CPU 施加压力**

> docker 运行— rm -it 进度/压力— cpu 4 —超时 20 秒

注意:根据您的 CPU 类型，确保相应地更换' **4** '。

几秒钟后，您应该会收到一个**时差**通知:

![](img/1126277a364c8156cfc92b9932673f30.png)

**Kapacitor** 触发警报，如果警报解除，还会恢复它们(状态正常)。

**6.2 —强化记忆**

> docker 运行— rm -it 进度/压力—虚拟机 3 —超时 20 秒

它将使用三个进程对内存施加压力，每个进程大约有 **256 Mb** (用选项*–VM-bytes*覆盖)。

让它运行几秒钟:

![](img/0c94e48d82ab39f9827237589cb981e7.png)

就是这样！您已经成功地为您的 **Swarm 集群**建立了一个高度可扩展的分布式监控平台，并且只使用了开源项目。

![](img/3049f70108424edfdc543c54dbc562cf.png)