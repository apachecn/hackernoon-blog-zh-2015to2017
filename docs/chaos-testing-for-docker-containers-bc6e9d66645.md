# 码头集装箱的混沌测试

> 原文：<https://medium.com/hackernoon/chaos-testing-for-docker-containers-bc6e9d66645>

> 几周前，我在伦敦的[集装箱仓库](https://2017.container.camp/uk)展示了 [*码头工人*](https://hackernoon.com/tagged/docker) *集装箱*的*混沌测试。你可以在这篇文章的结尾找到原始的录音和幻灯片，为了可读性，我对文本做了一些小的编辑，并添加了一些链接以获得更多的上下文。*

# 介绍

软件开发是关于构建支持业务需求的软件服务。我们想要自动化和集成的业务流程越复杂，我们构建的软件系统就越复杂。解决方案的复杂性往往会随着时间和范围的增长而增长。

越来越复杂的原因可能各不相同。一些系统处理太多的问题，或者需要与外部服务和内部遗留系统进行大量集成。这些系统由具有不同技能的不同人员在几年内多次编写和重写，试图满足不断变化的业务需求，使用不同的技术，遵循不同的技术和架构趋势。

所以，我的观点是，构建软件，随着时间的推移，无意中变得越来越复杂，是很容易的。我们以前都做过，现在可能也在做。为复杂系统构建一个“好的”软件架构，并在一段时间内保持它的“好”能力，真的很难。

当您有太多的“移动”部分、集成和不断变化的需求时，在处理代码变更、安全升级、硬件现代化、多网络通信渠道等时，避免意外故障可能会成为一项“不可能完成的任务”。

# 事情发生了！

所有系统都会不时出现故障。你的软件系统也会失败。把这当成生活的现实。总会有一些事情会出错。无论我们如何努力，都无法构建完美的软件，我们所依赖的公司也是如此。即使是来自公司的最稳定和受尊敬的服务，实践 CI/CD，测试驱动开发(TDD/BDD)，有巨大的 QA 部门和定义良好的发布过程，也会失败。

仅举几个去年停机的例子:

1.  **IBM，1 月 26 日**

*   今年年初，当客户用来访问其 Bluemix 云基础设施的管理门户关闭几个小时时，IBM 的云信誉受到了打击。虽然底层基础架构实际上没有出现故障，但用户却沮丧地发现他们无法管理自己的应用程序、添加或删除支持工作负载的云资源。
*   IBM 表示，这个问题是间歇性的，源于对界面的拙劣更新。

2. **GitLab，1 月 31 日**

*   GitLab 广受欢迎的在线代码库 GibLab.com 遭遇了 18 小时的服务中断，最终无法完全修复。
*   当一名员工在维护过程中从错误的数据库服务器上删除了一个数据库目录时，问题就出现了。

3. **AWS，2 月 28 日**

*   [这就是震惊整个行业的停电事件](http://www.crn.com/news/cloud/300083958/aws-storage-outage-wreaking-havoc-on-web-services-providers.htm)。
*   一名亚马逊网络服务工程师试图在供应商的弗吉尼亚州数据中心调试 S3 存储系统时，意外地输入了错误的命令，导致互联网的大部分内容(包括 Slack、Quora 和 Trello 等许多企业平台)中断了四个小时。

4.**微软 Azure，3 月 16 日**

*   存储可用性问题困扰了微软的 Azure 公共云八个多小时，主要影响美国东部的客户。
*   一些用户在调配新存储或访问该地区的现有资源时遇到了困难。微软的一个工程团队后来发现，罪魁祸首是一个存储集群断电，变得不可用。

访问[停机。报告](http://outage.report/)或 [Downdetector](http://downdetector.com/) 以查看由最终用户报告的持续更新的长列表。

# 追逐软件质量

作为软件工程师，我们希望为我们正在构建的软件系统感到自豪。我们希望这些系统是高质量的，没有功能缺陷，安全漏洞，提供卓越的性能，对意外故障有弹性，自我修复，始终可用，易于维护和现代化。

每个新项目都是从“高质量”的图片开始的，没有人想创建蹩脚的软件，但是我们中很少有人(或者没有人)能够实现并保持所有良好的“能力”。那么，我们能做些什么来提高整体系统质量呢？我们应该做更多的测试吗？

我倾向于说“是”——软件测试至关重要。但是仅仅运行单元、功能和性能测试是不够的。

今天，利用我们拥有的所有令人惊奇的新技术，构建复杂的分布式系统变得更加容易。微服务架构是当今真正的趋势，各种容器技术支持这种架构。与过去相比，部署、扩展、链接、监控、更新和管理由多个“微服务”组成的分布式系统要容易得多。

当我们构建分布式系统时，我们从 [CAP 定理](https://en.wikipedia.org/wiki/CAP_theorem)中选择 **P** ( *分区容差*)，其次是 **A** ( *可用性* —最流行的选择)或 **C** ( *一致性*)。因此，我们需要找到一种测试 **AP** 或 **CP** 系统的好方法。

传统的测试规程和工具不能很好地回答*当生产中出现意外情况时，您的分布式系统会如何表现？*。当然，事实上，你可以从以前的失败中吸取教训，而且你绝对应该这么做。但是，从过去的经验中学习不应该是为未来的失败做准备的唯一方法。

等待生产中出现问题不是一个选项。*但是有什么选择呢？*

# 混沌工程

另一种选择是故意打碎东西。而[混沌工程](https://hackernoon.com/tagged/chaos-engineering)就是这样做的一种方法。混沌工程的理念就是*拥抱失败！*

分布式软件系统的混沌工程最初是由网飞推广的。

混沌工程定义了分布式软件系统弹性测试的经验方法。你正在通过进行*混沌实验*来测试一个系统。

典型的*混沌实验*:

*   定义系统的*正常/稳定*状态(例如，通过监控一组系统和业务指标)
*   伪随机注入故障(例如，通过终止虚拟机、终止容器或改变网络行为)
*   尝试通过偏离预期或稳态行为来发现系统弱点

破坏稳定状态越难，我们对系统的行为就越有信心。

# 混沌工程工具

当然，手动练习混沌工程也是可以的。但是有一些很好的工具可以使用。

网飞为在公共云(AWS)中实践混沌工程构建了许多有用的工具:

*   混乱猴子——杀死 EC2、杀死进程、烧毁 CPU、填满磁盘、分离卷、增加网络延迟等
*   混沌孔-移除整个 AWS 区域

这些都是非常好的工具，我鼓励你使用它们。但是当我开始我的新的基于容器的项目时(2 年前)，感觉这些工具为我想要创建的*混乱*提供了*错误的*粒度。我不仅想在真正的集群中创建*混沌*，还想在单个开发人员的机器上创建混沌，以便能够调试和调优我的应用程序。我在 Google 上搜索了 Docker 的 *Chaos Monkey，但是除了一些基本的 Bash 脚本之外什么也没找到。*

所以，我决定创造自己的工具。从第一天起，我就把它作为开源项目与社区分享。是混沌~~猴子~ ~疣猪对 Docker — [Pumba](https://github.com/gaia-adm/pumba)

# pumba——Docker 的混沌测试

*什么是 Pumba(a)？*

我们这些有孩子或曾经是 90 年代孩子的人应该记得迪士尼动画电影《狮子王**中的这个角色。在斯瓦希里语中，**彭彭**的意思是“*愚蠢、愚蠢、弱智、粗心、疏忽*”。我喜欢斯瓦希里语的意思。它和我想要创造的工具非常匹配。**

# Pumba 能做什么？

Pumba 通过注入不同的故障来扰乱正在运行的 Docker 运行时环境。Pumba 可以`kill`、`stop`、`remove`或`pause`码头集装箱。

Pumba 还可以进行网络仿真，模拟不同的网络故障，如:延迟、数据包丢失(使用不同的概率丢失模型)、带宽速率限制等等。对于网络仿真，Pumba 使用 Linux 内核流量控制`tc`和`netem`排队规则，在这里阅读更多。如果`tc`在目标容器中不可用，Pumba 使用一个带有`tc`的 *sidekick* 容器，将其连接到目标容器网络。

您可以将容器列表传递给 Pumba，或者只编写一个正则表达式来选择匹配的容器。如果不指定容器，Pumba 会试图扰乱所有正在运行的容器。使用`--random`选项，从提供的列表中随机选择一个目标容器。也可以定义一个可重复的时间间隔和持续时间参数，以更好地控制你想要创造的混乱*的数量。*

Pumba 可以作为 Linux、MacOS 和 Windows 的单一二进制文件，或者作为 Docker 容器。

```
# Download binary from https://github.com/gaia-adm/pumba/releases
curl https://github.com/gaia-adm/pumba/releases/download/0.4.6/pumba_linux_amd64 --output /usr/local/bin/pumba
chmod **+**x /usr/local/bin/pumba **&&** pumba **--**help

# Install with Homebrew (MacOS only)
brew install pumba **&&** pumba **--**help

# Use Docker image
docker run gaiaadm/pumba pumba **--**help
```

# Pumba 命令示例

首先，运行`pumba --help`获得可用命令和选项的帮助，运行`pumba <command> --help`获得特定命令和子命令的帮助。

```
# pumba help
pumba **--**help

# pumba kill help
pumba kill **--**help

# pumba netem delay help
pumba netem delay **--**help
```

杀死从`^test` regex 列表中随机选择的 Docker 容器。

```
# on main pane/screen, run 7 test containers that do nothing
**for** i in {0..7}; **do** docker run **-**d **--**rm **--**name test$i alpine tail **-**f /dev/null; done
# run an additional container with 'skipme' name
docker run **-**d **--**rm **--**name skipme alpine tail **-**f /dev/null

# run this command in another pane/screen to see running docker containers
watch docker ps **-**a

# go back to main pane/screen and kill (once in 10s) random 'test' container, ignoring 'skipme'
pumba **--**random **--**interval 10s kill re2:^test
# press Ctrl-C to stop Pumba at any time
```

使用*正常*分布模型，为`ping`容器的*工程*流量增加一个`3000ms` ( `+-50ms`)延迟`20`秒。

```
# run "ping" container on one screen/pane
docker run **-**it **--**rm **--**name ping alpine ping 8.8.8.8

# on second screen/pane, run pumba netem delay command, disturbing "ping" container; sidekick a "tc" helper container
pumba netem **--**duration 20s **--**tc**-**image gaiadocker/iproute2 delay **--**time 3000 jitter 50 **--**distribution normal ping
# pumba will exit after 20s, or stop it with Ctrl-C
```

为了演示丢包能力，我们需要三个屏幕/面板。我将使用`iperf`网络带宽测量[工具](https://iperf.fr/)。
在第一个面板上，运行 *server* docker container 和`iperf` on-board，并在那里启动一个 UDP 服务器。在第二个窗格中，用`iperf`启动*客户端* docker 容器，并向*服务器*容器发送数据报。然后，在第三个窗格中，运行`pumba netem loss`命令，向*客户端*容器添加一个丢包。享受混乱。

```
# create docker network
docker network create **-**d bridge testnet

# > Server Pane
# run server container
docker run **-**it **--**name server **--**network testnet **--**rm alpine sh **-**c "apk add --no-cache iperf; sh"
# shell inside server container: run a UDP Server listening on UDP port 5001
sh$ iperf **-**s **-**u **-**i 1

# > Client Pane
# run client container
docker run **-**it **--**name client **--**network testnet **--**rm alpine sh **-**c "apk add --no-cache iperf; sh"
# shell inside client container: send datagrams to the server -> see no packet loss
sh$ iperf **-**c server **-**u

# > Server Pane
# see server receives datagrams without any packet loss

# > Pumba Pane
# inject 20% packet loss into client container, for 1m
pumba netem **--**duration 1m **--**tc**-**image gaiadocker/iproute2 loss **--**percent 20 client

# > Client Pane
# shell inside client container: send datagrams to the server -> see ~20% packet loss
sh$ iperf **-**c server **-**u
```

# 会议和幻灯片

## ContainerCamp 英国 2017 年会议

## 上述会议的幻灯片

[**混沌工程**](https://www.slideshare.net/alexLM/chaos-engineering-for-docker) 来自 [**阿列克谢·列德涅夫**](https://www.slideshare.net/alexLM)

希望，你觉得这篇帖子有用。我期待您的评论和任何问题。

*原载于 2017 年 10 月 4 日*[*code fresh . io*](https://codefresh.io/blog/chaos_testing_docker)*。*