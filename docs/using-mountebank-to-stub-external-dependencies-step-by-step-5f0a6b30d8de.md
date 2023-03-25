# 使用 Mountebank 清除外部依赖关系:一步一步。

> 原文：<https://medium.com/hackernoon/using-mountebank-to-stub-external-dependencies-step-by-step-5f0a6b30d8de>

您是否在一个需要处理大量数据设备的项目中？

测试您的应用程序流是否很难，因为它们依赖于许多不同的外部资源？

您的结构是否足够复杂，以至于无法创建样本数据，要么是因为太难，要么是因为假数据不能代表实际数据？

[性能](https://hackernoon.com/tagged/performance)是不是在你正在使用的系统上测试一些你做梦都想不到的事情？

如果以上任何一个问题适用于你的现实， [Henrique Souza](https://medium.com/u/b1f67e499f7?source=post_page-----5f0a6b30d8de--------------------------------) 和我可能有解决你问题的办法。

你知道我们通常如何为单元和集成测试创建 [**模拟**、**存根**或**探子**](http://martinfowler.com/articles/mocksArentStubs.html) 吗，当我们并不真的想测试应用程序的特定部分时？

有一些工具可以对外部资源做同样的事情。在我们的现实中，我们想要测试我们系统的性能，而不考虑外部资源返回数据所花费的时间。我们希望测试能够代表系统的实际流程，使用实际的数据——这样我们就不会犯这样的错误:测试一些极其简单的东西的性能，或者测试一些在真实场景中永远不会发生的非常复杂的东西的性能。

出于这些原因，我们开始看一看[江湖骗子](http://www.mbtest.org/)。 [Mountebank](https://hackernoon.com/tagged/mountebank) 是一款开源工具，帮助您从实际的外部服务获取实际的响应数据，存储它们并启动一个服务，该服务复制外部资源的端点，返回它最初存储的响应数据。

我们创建了下面的分步教程来演示这个过程的一个简单示例，主要是因为我们发现，每次我们必须向新开发人员解释它时，我们都必须从头开始重新学习一切—而在 Mountebank 的网页中没有很多简单完整的示例。

1.  首先:[安装](http://www.mbtest.org/docs/install)江湖骗子。您也可以使用以下方式安装它:

```
npm install -g mountebank
```

2.启动江湖骗子。(./mb 也可以)

3.在本例中，我们在本地部署外部资源。如果您将它部署在其他地方，可以跳过这一步。

4.创建一个冒名顶替者，就像这样:

```
curl -i -X POST -H 'Content-Type: application/json' [http://127.0.0.1:2525/imposters](http://127.0.0.1:2525/imposters) --data '{
  "port": 6569,
  "protocol": "http",
  "name": "proxyOnce",
  "stubs": [
    {
      "responses": [
        {
          "proxy": {
            "to": "[http://localhost:8080](http://localhost:8080)",
            "mode": "proxyOnce",
            "predicateGenerators": [
              {
                "matches": {
                  "method": true,
                  "path": true,
                  "query": true,
                  "body": true
                }
              }
            ]
          }
        }
      ]
    }
  ]
}'
```

您可以根据需要修改示例，只需确保 mountebank 端口(默认为 2525)和将代理的端口(在本例中为 6569)是正确的。
其他选项是只代理第一个请求(模式 **proxyOnce** )，使用 **http** ，定义实际服务的地址( [http://localhost:8080](http://localhost:8080) )，并说存根请求应该匹配方法、路径、查询和主体以返回存储的结果。有关更多信息，[他们有关于此事的文档](http://www.mbtest.org/docs/api/proxies)。

5.在 6569 端口做你想存根的请求，它会在第一时间重定向到你的 8080。

6.现在您已经有了存根化的请求，如果您正在本地运行外部服务，您可以停止它并检查 Mountebank 是否仍在为您存根化的请求返回数据。

7.为了能够重用您清除的请求，您所要做的就是告诉 mountebank 将它们存储在一个文件中。这应该在 mountebank 仍在运行的情况下完成。

```
mb save --savefile impostorsPopulated.json
```

8.现在，您可以随时复制这些状态，只需使用保存的文件重新启动 mountebank。

```
mb --configfile impostorsPopulated.json
```

使用这种方法，我们能够对我们的后端进行性能测试，并且很少对我们的前端进行性能测试，我们希望您也能从中受益！

这篇文章有帮助吗？我们错过了什么吗？让我们知道！:)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！