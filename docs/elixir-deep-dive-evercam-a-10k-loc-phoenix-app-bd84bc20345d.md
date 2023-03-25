# Elixir Deep Dive: Evercam，10K LOC Phoenix 应用程序

> 原文：<https://medium.com/hackernoon/elixir-deep-dive-evercam-a-10k-loc-phoenix-app-bd84bc20345d>

![](img/b29352fc87eb90134be4ee3b2fc6992a.png)

Photo by [Shutter Fotos](https://www.flickr.com/photos/shutterfotos/)

2015 年春天， [Evercam](https://evercam.io) ，一家建筑延时和项目管理摄像软件公司，开源 [evercam-server](https://github.com/evercam/evercam-server) 。evercam-server 是一款 Phoenix 应用程序，可以与连接的相机进行通信，通过电子邮件发送静态图像，共享实时视频流等。

开源 evercam-server 是送给 Elixir 社区的一份精美礼物。虽然很容易找到小的示例 Phoenix 应用程序，但是要找到在生产环境中运行的具有重要代码库的应用程序就比较困难了。evercam-server 检查这些框:它是 evercam 堆栈的关键部分，有 10.4K 行组织清晰的代码和稳定的提交流。

我花了一些时间从 Rubyist 的角度探索 evercam-server，寻找有趣的模式。下面是对该应用程序的深入介绍，从简单的部分开始，最后介绍该应用程序更高级的部分。

# 长生不老药/二郎神盒子里有很多

evercam-server 最有趣的一点是:Elixir 所依赖的 Erlang 的健壮性消除了像我这样的 Ruby 开发人员所习惯的许多外部依赖性。从后台任务到 Cron 再到缓存，evercam-sever 利用了 Erlang 已经提供的服务。

# 通过任务的内部通信集成

evercam-server 使用 Intercom——SaaS 企业的 CRM 来跟踪关键的客户事件，如用户注册或取消。当用户注册时，evercam-server 会联系内部通信 API。这是通过`[Task.start/1](https://hexdocs.pm/elixir/Task.html#start/1)`完成的，因为不需要等待结果:

注意`Intercom.get_user/1`对内部通信 API 进行 HTTP 调用。将它包含在传递给`Task.start/1`的函数中，而不是在请求期间内联进行 HTTP 调用是有意义的。

我不清楚 evercam-server 如何处理内部通信 API 的连接问题，以防止内部通信出现通信问题。在 Ruby 世界中，我已经通过 Sidekiq 执行了一个类似的内部通信集成——它将重试创建一个用户——以及一个单独的预定 Cron 任务，该任务遍历我们的客户并验证他们是否存在于内部通信中。

# 使用“with”处理错误情况

Elixir 的管道操作符非常适合通过一组操作来转换数据。然而，当函数返回不一致的数据时，链接可能会中断。

例如，看看下面的代码。`Intercom.get_user/1`尝试查找本地 Evercam `User`的相关对讲机用户记录:

```
Intercom.get_user(user) |> fn({_,json}) -> json.intercom_id end
```

如果对 Intercom 的外部 HTTP 调用失败，`json`有效负载将不会包含`intercom_id`，我们将会失败，并显示一个不明错误。用管道正确处理这个问题会产生一些[特别难看的代码](http://openmymind.net/Elixirs-With-Statement/)。

幸运的是，Elixir 1.2 引入了`[with/1](https://hexdocs.pm/elixir/Kernel.SpecialForms.html#with/1)`，evercam-server 广泛使用它来清楚地处理错误。看看控制器是如何更新`Snapmail`记录的:

只要`<-`的左边是匹配的，执行就会继续。如果匹配失败，执行跳转到`else`块。

# 预加载与 ActiveRecord 风格的关联加载

如果您来自 Ruby on Rails，您会注意到在 Ecto 模型查询中的一个主要区别是对预加载关联的显式调用:

与 ActiveRecord 不同，如果在运行时访问相关记录，Ecto 不会加载它们。虽然这是更多的前期开发工作，但它减少了影响生产的与数据库相关的性能问题(如 N+1 查询)的数量。

> 旁注:在我的日常工作中，我们正在构建一个 Elixir 应用程序监控服务，以跟踪 Ecto 查询、HTTP 调用等的性能。
> [注册我们的测试版](https://apm.scoutapp.com/elixir)。

# 用于缓存的 ConCache

如果您来自 Ruby，您可能已经使用 Redis 作为缓存。在 Elixir 的保护下，Erlang 有了本地缓存选项。

Evercam 使用 [ConCache](https://github.com/sasa1977/con_cache) ，它使用 [Erlang Term Storage](http://erlang.org/doc/man/ets.html) (ETS)来满足其缓存需求。ConCache 的一个优点是:与 Redis 支持的有限类型相比，您可以存储所有类型的药剂。例如，下面的代码在 ConCache 中存储了一个`User`而不是一个`User` id:

# 使用 Hackney 和 HTTPoison 进行 HTTP 调用和池化

evercam_server 使用 [SeaweedFS](https://github.com/chrislusf/seaweedfs) ，一个分布式文件存储系统，来存储来自相机的图像捕获(快照)。evercam_server 使用 SeaweedFS HTTP API 来上传和下载图像。

由于创建和关闭 HTTP 连接的开销很大，因此为上传和下载定义了一个连接池:

[httposin](https://github.com/edgurgel/httpoison)Elixir 客户端用于 HTTP 调用。HTTPoison 由 Erlang HTTP 客户端 [Hackney](https://github.com/benoitc/hackney) 提供支持。特定于 Hackney 的选项(如连接池)可以传递给 HTTPoison。例如，`seaweedfs_upload_pool`用于保存图像:

# 计划作业的量程

evercam_server 利用 [quantum_elixir](https://github.com/c-rack/quantum-elixir) 来运行类似 Cron 的调度任务，例如清理短期文件存储和发送提醒电子邮件:

与 Cron 相比，Quantum 提供了许多好处。一些亮点:

*   在特定的酏剂节点上运行任务
*   停用和激活任务
*   一秒粒度任务调度
*   长时间运行的作业超时

# 发送快照邮件

或许这款 Elixir 应用程序中最有趣的流程是定期重复的静态相机图像捕捉，称为`Snapmail`。这确实展示了一些我在像 Ruby 这样的语言中没有使用过的灵丹妙药和模式。

![](img/88cbb81abe1bbd6405661dbf7272879c.png)

当 Snapmail 插入数据库时，会创建三个新进程。**每个** `**Snapmail**` **数据库行**有 3 个进程。如果你像我一样来自 Ruby，为数据库的每一行创建进程听起来非常沉重。

当然，我们现在处于长生不老之地，加工过程很便宜。这些进程——通过`SnapmailSupervisor.start_snapmailer/1`启动——启动一个`Snapmailer` GenServer，该 GenServer 启动`Poller`和`GenEvent`链接的进程。`Poller`在设定的时间醒来，从相机中抓拍照片，并发送一封包含静态图像的电子邮件。

`Snapmailer`和`Poller`是链接的进程:如果`Poller`死亡，它也会杀死关联的`Snapmailer`进程。然后`SnapmailSupervisor`会重生`Snapmailer`，这又会产生一个新的`Poller`。在`Snapmailer`和`GenEvent`进程之间存在相同的行为。

# 知道另一个大型凤凰应用程序吗？

如果你知道——或者正在开发——一个大型开源 Phoenix 应用程序，添加一个响应。很高兴看到生产质量的凤凰应用程序。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)