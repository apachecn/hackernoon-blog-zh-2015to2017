# 硒测试:新的希望

> 原文：<https://medium.com/hackernoon/selenium-testing-a-new-hope-7fa87a501ee9>

# 第一部分.问题和初步解决办法。

2004 年启动的 Selenium 项目现在已经成为浏览器自动化的行业标准。然而如果你的 QA 部门相对较大，你迟早会面临到推荐[硒](https://hackernoon.com/tagged/selenium)架构的局限。在本文中，我将告诉您如何轻松创建一个可伸缩且容错的 Selenium 解决方案。

# 问题

自 2004 年创建第一个原型以来，Selenium 架构发生了几次根本性的变化。当前在 2.0 分支中引入的 Selenium 架构被称为 Selenium Grid。它的工作原理如下:

![](img/68e21802964fed714417c2a3282e1555.png)

通常一个集群由两个守护程序应用程序组成:Selenium Hub 和 Selenium Node。一个 **hub** 是一个 API，它处理用户请求并将它们重定向到各自的节点。一个**节点**是一个实际的请求执行器，它启动浏览器进程并向它们请求所需的测试步骤。理论上，一个 Selenium Hub 可以连接无限数量的 Selenium 节点，每个节点都可以启动任何已安装的浏览器。但是实际上呢？

1.  这样的架构有一个薄弱的部分。Selenium Hub 是一个单一的浏览器访问点。如果它关闭或没有响应，所有浏览器都不可用。如果带有集线器的数据中心断电或网络出现故障，也会发生同样的情况。
2.  Selenium Grid 的伸缩性不好。我们 5 年多的 Selenium 集群专业经验表明，即使在中等负载下，一个集线器也可以与有限数量的连接节点一起工作。根据硬件的不同，即使有几十个连接的节点也会显著增加集线器的响应时间。
3.  没有报价功能。您不能创建用户和指定浏览器消费限制。

# 解决方法

最简单的可扩展方法是使用分布在多个数据中心的多个 Selenium Hubs。然而，标准的 Selenium 库只能与一个 Selenium hub 一起工作。我们需要教会他们使用这样的分布式系统。

# 客户端负载平衡

几年前，我们成功使用的一个初始方法是客户端负载平衡的客户端库。它是这样工作的:

1.  我们在多个数据中心推出了多个 Selenium Hubs 和相应的节点。
2.  支持浏览器的集线器主机名列表保存到文件中。
3.  Selenium 用户将一个小型客户端库作为其测试的依赖项，并使用该库请求一个 selenium 会话。
4.  库通过集线器读取文件，并随机选择其中一个具有所需浏览器的文件。然后，它请求使用标准 Selenium 客户端的浏览器。
5.  如果会话创建成功，那么测试步骤开始执行。否则，库会尝试另一个集线器主机，直到创建了会话。不同的集线器可以包含不同数量的浏览器。为了实现均匀的负载分布，我们需要为集线器主机分配不同的权重，然后根据权重选择这些主机。
6.  如果客户机无法在列表中的每个集线器上创建会话，它应该会抛出一个错误。

![](img/78351861444a0932c6ddf3e850d77129.png)

应该更改一行测试代码(新的会话请求)来支持该库。例如，在 Java 测试中，一个新的会话请求可能如下所示:

```
WebDriver driver = 
 new RemoteWebDriver("http://my-hub.example.com:4444/wd/hub", caps);
```

这段代码中的所有类都来自一个标准的 Selenium Java 客户端。例如，如果客户端库被称为 SeleniumHubFinder，则新的会话请求将如下所示:

```
WebDriver driver = SeleniumHubFinder.find(caps);
```

更新后的代码中没有使用 Selenium hub URL 该信息存储在客户端库中。就是这样！这种方法多年来一直有效。我们公司的数百名软件测试人员都很满意。使用客户端库有什么弊端？

1.  应该向每个测试项目添加一个补充库。没有这个库，您无法启动您的测试。
2.  应该为每种语言实现单独的客户端库。例如，您的公司中可能存在 Javascript、Java 或 Python Selenium 测试。在这种情况下，您需要支持多个客户端库，并确保中心列表同步。这就是为什么服务器端解决方案是必要的。

# 服务器端负载平衡

根据我们在客户端解决方案方面的经验，我们向服务器端解决方案引入了以下自然需求:

1.  对于客户端库来说，服务器应该看起来像 Selenium hub。为此，它应该实现 [Selenium JsonWire 协议](https://w3c.github.io/webdriver/webdriver-spec.html)。
2.  可以在任何数据中心安装任意数量的服务器节点。它们可以安装在任何软件或硬件负载平衡器的后面。
3.  服务器实例是无状态的。它们既不使用数据库服务器也不使用队列服务器来共享状态。
4.  服务器应支持多用户和报价。

我们称这个服务器为 GridRouter，因为它唯一做的事情就是将用户请求路由到正确的 Selenium 网格中心。这是新的架构:

![](img/39d766276b74b45a19ad2c1cde02abd6.png)

*   负载平衡器将用户请求分布在多个 GridRouter 实例中。
*   每个 GridRouter 实例都像客户端库一样存储所有可用 Selenium Hubs 的信息。
*   为了处理新的会话请求，GridRouter 使用相同的随机分配算法。
*   您可能知道，Selenium 中的每个新浏览器会话都会自动获得一个名为会话 ID 的 ID。根据 Selenium JSONWire 协议，这个 ID 总是被传递给请求。GridRouter 将有关所选 Selenium Hub 的信息附加到该会话中，并将丰富的会话 ID 返回给用户。
*   在获得会话之后，GridRouter 在每个后续请求上从丰富的会话 ID 中提取所使用的 Selenium Hub 信息，并简单地将其代理给相应的 Hub。因为所有会话信息都存储在它的 ID 中，所以不需要同步 GridRouter 实例。这就是为什么 GridRouter 是无状态的。

# 栅格路由器

最初我们使用 Java、 [Jetty](https://eclipse.org/jetty/) 和 [Spring Framework](http://spring.io/) 实现了 GridRouter。它的源代码可以在 [Github](https://github.com/seleniumkit/gridrouter) 上找到。这个实现使用一个纯文本属性文件来存储用户列表，使用一个 XML 文件来保存每个用户的 Selenium hubs 列表。典型的用户列表(默认为**/etc/grid-router/users . properties**)如下所示:

```
user:password, user
user2:password2, user
```

每行对应一个用户。当前实现中的密码是在没有任何加密的情况下存储的。这是因为我们考虑到用户主要需要考虑不同团队的浏览器消耗。Selenium hub 列表存储在以下格式的 XML 文件中(默认为 **/etc/grid-router/quota/*)。xml** ):

```
<qa:browsers xmlns:qa="urn:config.gridrouter.qatools.ru">
  <browser name="firefox" defaultVersion="33.0">
    <version number="33.0">
      <region name="us-west">
        <host name="ff33-hub-1.example.com" port="4444" count="5"/>
      </region>
      <region name="us-east">
        <host name="ff33-hub-2.example.com" port="4444" count="5"/>
      </region>
    </version>
    <version number="37.0">
      <region name="us-west">
        <host name="ff37-hub-1.example.com" port="4444" count="3"/>
        <host name="ff37-hub-2.example.com" port="4444" count="4"/>
      </region>
      <region name="us-east">
        <host name="ff37-hub-3.example.com" port="4444" count="2"/>
      </region>
    </version>
  </browser>
  <browser name="chrome" defaultVersion="42.0">
    <version number="42.0">
      <region name="us-west">
        <host name="ch42-hub-1.example.com" port="4444" count="10"/>
      </region>
      <region name="us-east">
        <host name="ch42-hub-2.example.com" port="4444" count="10"/>
      </region>
    </version>
  </browser>
</qa:browsers>
```

您可以看到，我们定义了可用的浏览器名称、它们的版本以及分布在多个地区的一组主机。在我们看来，一个区域就是一个数据中心。如果一个数据中心出现故障，主要需要有关数据中心的信息。如果第一次会话尝试失败，我们将从另一个数据中心选择主机。这种方法增加了更快创建 Selenium 会话的可能性。

# 在测试中使用 GridRouter

正如我前面说过的，GridRouter 实现了标准的 Selenium 协议，并且与所有现有的客户端库完全兼容。我们剩下的主题是如何在 GridRouter 中进行身份验证，即指定我们想要使用的配额。所有 Selenium 客户端库都只支持一种认证方法— [基本 HTTP 认证](https://en.wikipedia.org/wiki/Basic_access_authentication)。这就是为什么 GridRouter 也只支持这种方法。通常 Selenium hub 的 url 如下:

```
http://example.com:4444/wd/hub
```

您可能知道，基本的 HTTP 验证用户名和密码可以编码为 URL，如下所示:

```
http://username:password@example.com:4444/wd/hub
```

这是您在代码中使用 GridRouter 代替 Selenium Hub 所需要做的唯一更改。大多数 Selenium 客户端库(包括 Java 和 Python 实现)都使用这种符号。然而，一些基于 Selenium 的 Javascript 工具要求您将用户名和密码指定为单独的配置选项。

# 月球表面图

GridRouter 允许我们停止使用客户端库。它让使用不同语言的用户可以访问可伸缩的 Selenium 安装。要扩展 GridRouter 的安装，只需向它的 XML 配置中添加更多 Selenium hubs 所有的更改都会自动应用，无需重启服务。为了每秒处理更多的请求，您还需要在负载均衡器后面添加 GridRouter 主机。我们的经验表明，当任何版本的浏览器的总使用百分比低于 80%时，GridRouter 都可以很好地工作。当峰值负载到来，浏览器消耗增长到总容量的 90–100%时，问题就出现了。在这种情况下，随机统一会话尝试分布变得低效。

![](img/bc9432bdfffc122162ff53ce89bf21ff.png)

我们太频繁地试图在完全占用的集线器上获取 Selenium 会话，并在将会话返回给用户之前尝试几个集线器。这增加了会话开始时间并降低了测试速度。我们在 Selenium cluster 开发中的下一个阶段旨在解决上述问题，这是一个名为[selenio graph](https://github.com/seleniumkit/selenograph)的新产品。Selenograph 是一个基于 GridRouter 源代码的 Java 服务器，与它的配置文件完全兼容。主要区别是:

1.  它是有状态的。为了在高负载下更高效，Selenograph 使用了更复杂的算法来选择集线器主机。主要思想是通过考虑已经运行的会话总数来动态调整集线器主机权重。这个数字应该保存到月图节点之间共享的存储器中。我们使用 [MongoDB](https://www.mongodb.com/) 作为这样的存储。
2.  它提供了更多的统计数据和用户友好的界面。例如，Selenograph API 可以返回每个时刻并发运行的会话总数。虽然 Selenograph 是一个有状态的解决方案，但它被证实在高负载下可以正确工作，允许每秒钟为每个实例服务数百个请求。

# 结论

在这一部分中，我向您介绍了标准的 Selenium 可伸缩性问题，以及如何通过对集群架构进行少量更改来解决这些问题。在下一部分，我们将讨论以下主题:

1.  如何为大型集群准备工作节点，使其能够很好地扩展
2.  关于最近硒未来的一些思考
3.  如何在 Docker 容器中运行 Selenium
4.  有哪些新的开源工具可以帮助您部署一个高效的低资源消耗的 Selenium 集群

敬请关注…

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)