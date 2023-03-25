# Spring Boot:用 REST + JPA 保持数据

> 原文：<https://medium.com/hackernoon/spring-boot-persisting-data-with-rest-jpa-33a063e8b147>

![](img/ec20ab8cd0e47fb7a5f9f025abe149e5.png)

> 本文是《Spring Boot:从零开始的 REST+TDD》的续篇，所以，如果你还没有这样做，请首先回顾一下。

在本文中，我们将添加 [Spring Data JPA](http://projects.spring.io/spring-data-jpa/) 来帮助我们实现基于 JPA 的存储库。我们的主要目标将是通过 RESTful 端点持久化/检索我们的用户[数据](https://hackernoon.com/tagged/data)，并且，就像在前一篇文章中一样，我们将尝试跟上 [TDD](https://hackernoon.com/tagged/tdd) 实践。

因此，让我们从创建一个简单的测试开始:

Add this file to $SOURCE_HOME/src/test/java/br/com/brunokrebs/

这个类包含一个单独的测试，它使用[REST freeze API](https://github.com/jayway/rest-assured)将序列化为 JSON 的用户发布到我们将要创建的端点。这个代码，就像现在一样，甚至不会编译，因为我们还没有 User 类。现在让我们创建它，以便能够运行我们的测试。

Add this file to $SOURCE_HOME/src/main/java/br/com/brunokrebs/model/

目前，一个简单的 [POJO](https://en.wikipedia.org/wiki/Plain_Old_Java_Object) 就足够了，因为我们将只使用它来序列化/反序列化到/到我们将要创建的端点。有了这个新类，我们现在就可以执行测试了。发出“mvn 清除验证”将为我们执行此任务。

Ooops，我们的测试中有一个危险信号。看来我们的终结点没有响应，状态代码等于 HttpStatus。好的，正如我们测试的[23 号线所料。嗯，有道理，因为我们还没有创建自己的端点。让我们现在就开始:](https://gist.github.com/brunokrebs/93b1e492ff585d064591#file-userrestit-java-L23)

Add this file to $SOURCE_HOME/src/main/java/br/com/brunokrebs/rest/

有了这个小类，我们现在将能够再次运行我们的测试，并(希望)看到它通过。让我们再次发出“mvn 清除验证”。很好，它现在通过了我们的第一次测试。

但等等，我们的 REST 服务只是返回我们传递给它的用户。让我们改进测试，看看我们的端点是否以某种方式神奇地持久化了我们的用户。

Take a look at @FixMethodOrder annotation. The NAME_ASCENDING configuration makes JUnit run our tests in alphabetical order.

发出“mvn clean verify”现在不能证明我们的用户是否被持久化，它只能证明我们没有一个端点来应答“/user/”路径上的 GET 请求。因此，为了获得一些速度并使事情更简单，让我们先将 Spring Data JPA 添加到我们的项目中，然后创建一个方法来响应 GET 请求。

> TDD 通常希望我们先创建一个小的测试，用最简单的实现来完成这个测试，改进测试来添加新的条件，重构我们的实现，然后一直这样下去。尽管一些 TDD 实践非常好，但是这个微周期并不是非常有效率，这就是为什么我们在本文中跳过了一些周期。

## 添加 Spring 数据 JPA 和 H2 数据库

添加 [Spring 数据 JPA](http://projects.spring.io/spring-data-jpa/) 作为依赖项，并通过我们的 RESTful 端点持久化数据，只需要四个简单的步骤。首先，我们需要向 pom.xml 添加两个依赖项:

第一个将 JPA 2 和 Hibernate 添加到我们的项目中。然后，我们必须注释我们的用户类，以获得持久化，并向其实例添加一个 id。

User class now annotated to be persisted and with an id field that is auto generated.

在那之后，我们的第三步，也是证明 Spring 如何令人难以置信地使事情变得简单的一步，是创建一个存储库接口，Spring 数据将使用它来自动创建存储库实现。这个实现将拥有我们现在需要的一切(比如保存、按 id 查找和查找所有方法)。

Create this interface at $SOURCE_FOLDER/src/main/java/br/com/brunokrebs/repository

作为第四步…等等，等等？用户存储库是否正确？只是一个扩展另一个的接口？是啊！！所以面对现实吧，让我们继续前进。在我们的下一篇文章中，我们将看到更多关于它的精彩内容。正如我所说的，第四步也是最后一步是重构我们的 UserRest 类，使用之前创建的 UserRepository 来保存和列出用户。

$SOURCE_HOME/src/main/java/br/com/brunokrebs/rest/UserRest.java

现在，所有这四个步骤完成后，让我们发出' mvn 干净验证'。太好了！两个测试都通过了，我们的用户得到了持久化。这太神奇了。嗯，没什么好惊讶的，H2 是一个嵌入式数据库，有了这种配置，每次执行时所有的表都会被重新创建。所以没有什么是真正持久的。当我们的应用程序启动并运行时，我们的用户会一直保留在数据库中。当它关闭时，我们的数据就会丢失。

我们将在下一篇文章中讨论这个问题。实际上，我们将用 PostgreSQL 替换 H2 数据库，这里使用它只是为了展示在我们的 Spring Boot 面向 REST 的应用程序中使用 JPA/Hibernate 是多么容易。

哦，只是一个附录，如果你想让我们的系统能够手动发出请求，只需发出以下命令' mvn spring-boot:run '。该系统还没有图形界面，但我们也将在这方面努力。这个接口也将基于我们的 RESTful 端点。

那么，你觉得 Spring Boot 和我们正在建造的这个建筑怎么样？在我看来，这是基于 web 技术的企业应用程序所能拥有的最佳架构之一。我们在一个坚实的基础上构建我们的应用程序，Spring 带有自动化测试，并且采用一种无状态的方法，就像 HTTP 被设计为工作一样。留下评论:)

> 我叫布鲁诺·克雷布斯，你可以通过我的 [Twitter](https://twitter.com/brunoskrebs/) 、我的 [LinkedIn](https://www.linkedin.com/in/brunokrebs) 和我的 [Github](https://github.com/brunokrebs) 账户找到我。此外，请随意在这里或我的任何文章中添加任何评论。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)