# NodeJs 比 JSP 快吗？

> 原文：<https://medium.com/hackernoon/is-nodejs-faster-than-jsp-f74463ed54ee>

最近，我和一位软件工程师聊天，慢慢地我们谈到了 JSP 这个话题。对于那些不知道 JSP 是什么的人来说，它代表 Java Server Pages(JSP ),一种建于 20 世纪 90 年代的技术。总之，*我是那些使用 JSP 开发 html 页面的开发者之一。*

他问“你为什么还在使用*旧的* JSP 页面？到底是谁干的？”

我的回答是“我希望我的页面尽可能快，如果不是最快的话。所以我使用 JSP”

他说“JSP 不可能比 Node 更快。否则人们为什么会搬到 Node？”

只有一个办法可以知道。:)

让我们进入测试细节，

**硬件**

[华硕 Zenbook Pro](https://www.asus.com/us/Notebooks/ASUS-ZenBook-Pro-UX501VW/) 笔记本电脑，搭载*英特尔 i7* 处理器，16GB 内存。CPU 有 8 个核心。

**软件**

Ubuntu 16.04，Tomcat8+Java8 和 NodeJS 7.5.0+PM2 模块。

**代码**

对于 JSP，我没有写任何代码。从[https://tomcat.apache.org/tomcat-7.0-doc/appdev/sample/](https://tomcat.apache.org/tomcat-7.0-doc/appdev/sample/)下载示例 WAR 文件，并复制到 Tomcat 的 webapps 文件夹中。

对于节点，从[https://blog . rising stack . com/your-first-node-js-http-server/](https://blog.risingstack.com/your-first-node-js-http-server/)复制 index.js 文件。

**设置**

由于 nodeJs 是单线程的，所以我使用“pm2”模块来运行 8 个 node-js 进程，以充分利用所有的内核。

为了公平起见，我添加了“NIO”配置，以便连接器是非阻塞的。

**测试**

测试由“Apache 基准测试”工具“ab”执行。在 http 服务器上进行大量请求的最简单的工具。目标是使用 100 个并发连接发出 50 万个请求。命令如下所示，

**雄猫**

```
ab -n 500000 -c 100 [http://localhost:8080/sample/hello](http://localhost:8080/sample/hello)
```

**节点**

```
ab -n 500000 -c 100 [http://localhost:3000/](http://localhost:3000/)
```

好了，让我们看看结果吧！！

**速度**

我在 Tomcat 和 NodeJs 上运行了 5 次 500K 的请求。

PM2 集群的 8 个 Nodejs 进程用了 32 秒来处理 50 万个请求。

1 NodeJs 进程用了 28 秒来处理 50 万个请求。(无 pm2)

Tomcat 用了 20 秒来处理 50 万个请求。

**定论:NodeJs 比 Tomcat 慢 50%左右。**

完整的结果可以在谷歌文档中找到

8 NodeJs processes run with PM2 cluster module

Single NodeJs process without PM2 cluster module

Tomcat8 with “nio” connector and 200 threads running on JDK8

**CPU 使用率**

这是一个快速测试，所以我没有进行详细的 CPU 测量。我刚刚运行了“htop ”,观察了 CPU 的使用情况。看 htop 很有趣。我录下了 Node 和 Tomcat 的其中一段视频，它们不到一分钟，请欣赏！

Screencast of 8-nodejs processes with pm2 cluster.

Screencast of single NodeJs process

Tomcat with “nio” connector and 200 threads running on JDK8

如果您观看了这两个视频，您会注意到，在测试 NodeJs 时，内核利用率约为 70%。

另一方面，在测试 tomcat 时，内核的利用率约为 45%。

**结论:NodeJS 多消耗 50%的 CPU**

*如果你是 NodeJs 开发者，请不要把这个当回事，辞职吧。这纯粹是为了好玩。然而结果是真实的。*

*请让我知道你的想法。欢迎您的评论！*

**更新**

我添加了没有集群的 1 nodejs 进程的结果。

> 感谢 Ramesh 对 NodeJs 的帮助。

感谢[Vigneshkumar Chinnachamy](https://medium.com/u/5eb45e25873e?source=post_page-----f74463ed54ee--------------------------------)的建议，我也添加了 1 nodejs 流程的结果。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)