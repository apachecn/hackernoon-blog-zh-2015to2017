# 另一篇 MVP 文章——第 3 部分:使用改型调用 API

> 原文：<https://medium.com/hackernoon/yet-another-mvp-article-part-3-calling-apis-using-retrofit-23757f4eee05>

*使用* ***MVP*** *，****rx Java****，****Dagger 2****，****retro fit 2****，****Test****，以及所有全新的现代方法和库*

# 本文前情提要…

[](https://hackernoon.com/yet-another-mvp-article-part-2-how-dagger-helps-with-the-project-90d049a45e00) [## 另一篇 MVP 文章——第 2 部分:Dagger 如何帮助项目

### 使用 MVP、RxJava、Dagger 2、Retrofit2、Test 和所有全新的现代技术实现一个示例 Android 应用程序…

hackernoon.com](https://hackernoon.com/yet-another-mvp-article-part-2-how-dagger-helps-with-the-project-90d049a45e00) ![](img/966a4e88bb1648ef069519eb7daa3621.png)

让我们假设你已经学习了 [part1](/@mohsenoid/yet-another-mvp-article-part-1-lets-get-to-know-the-project-d3fd553b3e21#.6y9ze7e55) 和 [part2](/@mohsenoid/yet-another-mvp-article-part-2-how-dagger-helps-with-the-project-90d049a45e00#.62mdwkqwj) ，这意味着你知道关于[示例项目的](https://github.com/mohsenoid/marvel)模块和结构的一切，并且也更加熟悉 Dagger 在连接 **MVP** 层中的用法。

现在让我们继续…

# 让我们进入网络和 API:

几乎所有的移动应用程序都使用互联网与它们的服务进行通信。**改型**是由[杰克·沃顿](https://medium.com/u/8ddd94878165?source=post_page-----23757f4eee05--------------------------------)贡献的一个完美的库，它总是能派上用场，帮助你为你的 [Android](https://hackernoon.com/tagged/android) 应用开发一个网络层。

长话短说，在这个项目**改造中，**使用了一个名为 [**MarvelApi**](https://github.com/mohsenoid/marvel/blob/master/core-lib/src/main/java/com/mirhoseini/marvel/domain/client/MarvelApi.java) 的接口，它显示了 Api 的方式:

使用**改进**真的很简单，正如你看到的，在[http://gateway.marvel.com:80/v1/public/characters](http://gateway.marvel.com:80/v1/public/characters)有一个 **RESTful API** 方法，它需要一些参数来检索你想要的东西，也就是使用其名称的字符数据。

一个好的[开发者](https://hackernoon.com/tagged/developer)总是阅读 API 文档，你可以阅读更多关于漫威 API 的信息:

 [## 漫威开发者门户-交互式文档

### 漫威开发者门户网站为漫威的粉丝、合作伙伴和其他技术人员提供了一系列强大的 APIs

developer.marvel.com](https://developer.marvel.com/docs) 

## ，以及 [**MarvelApi**](https://github.com/mohsenoid/marvel/blob/master/core-lib/src/main/java/com/mirhoseini/marvel/domain/client/MarvelApi.java) **位于哪个 MVP 层？**

![](img/f58d426492b11cefe3ee7768541f1952.png)

所有联网和数据库的东西都发生在**模型**层。

这一层只处理数据和业务逻辑，演示者知道如何调用或如何为视图层准备数据。

**请停止在模型层操纵数据，将数据安全可靠地传递给表示层，他知道该怎么做……**

## 和更坚实的原则…

根据固本原则的[单一责任](https://en.wikipedia.org/wiki/Single_responsibility_principle)[一个厨师不能经营整个餐厅](https://www.novoda.com/blog/designing-something-solid/)，那么**模特**和**展示**有完全分离的任务要做。

![](img/d9f67cf0ddb369025b1a4a2b7448ed07.png)

“One chef can’t run the whole restaurant”, — [novoda](https://www.novoda.com/blog/designing-something-solid/)

# 我们可以有一个很好的日志来记录我们在 API 响应中调用的内容和收到的内容吗？为什么不呢！

**改造**图书馆使用 OkHttp 库，里面是一个强大的网络库。所有的魔法都发生在拦截器上。我将逐一解释，并最终向您展示如何将它们应用到您的 OkHttpClient 中。

通过一点配置，您可以让这个库在 logcat 中向您展示一个体面的日志，这总是很方便的:

，但是不要忘记在发布版本中禁用这个日志记录功能！您可以通过检查 **BuildConfig 来实现这一点。调试在应用程序构建期间生成的**变量。

# 缓存 API 结果如何导致数据库消失！！！

我第一次在新闻阅读器应用程序中这样做时，所有的数据库记录更新和查询几乎要了所有团队成员的命。

使用 OkHttp 缓存功能，您可以处理标题缓存，也可以进行离线文件缓存，这有时可能是数据库痛苦的完美替代:

在这个示例中有两个缓存拦截器，

*   一个缓存拦截器在 2 分钟内改变 API 响应和可用缓存的头( **maxAgeMin** )，这意味着以某种方式去抖动来自 API 的用户调用 2 分钟。
*   另一个是离线拦截器，使用缓存目录(从 *app* 模块提供使用 **Dagger** )将 API 响应保存到文件中 7 天( **maxStaleDay** )。

如果一切都是新的，不要惊慌，你会在一些样本项目后学会如何使用它……；)

![](img/a05c279a601f4b2bea80e28271fbe90d.png)

# API 调用失败后，您信任网络状况吗？否，请重试！

网络连接总是有损耗，甚至服务可能会中断几秒钟，这可能会给应用程序带来麻烦，所以请在通知用户这种情况之前重试！

我使用 **RxJava** 寻找重试策略，但是没有一个能与**改进**一起工作，但是这个拦截器工作:

如果你需要更多的重试次数，甚至更多的条件来决定重试与否，你可以自定义它，但我认为它是好的。:)

# ，以及如何将所有拦截器附加到我的 OkHttp 客户端？

所有拦截器都在 [**ClientModule**](https://github.com/mohsenoid/marvel/blob/master/core-lib/src/main/java/com/mirhoseini/marvel/domain/ClientModule.java) 中提供，这也将它们全部添加到 OkHttpClient:

注意作为 **BuildConfig 结果的 *if(isDebug)* 语句。调试**变量。

还有网络超时，它告诉 OkHttp 等待响应的时间。

# …那么改造在哪里呢！

好了，所有这些编码都是为了最后一步…在 [**ApiModule**](https://github.com/mohsenoid/marvel/blob/master/core-lib/src/main/java/com/mirhoseini/marvel/domain/ApiModule.java) 中，我们使用那个 **OkHttpClient** 来创建**改型**实例，并使用它来创建 **MarvelApi** :

您必须提供 **BaseUrl** ，这通常是所有服务 API URLs 的第一个静态部分，在本例中为“http://gateway.marvel.com:80/”。(不要忘记最后一个'/'，它是**改型**库所必需的，可能会导致异常)

以这种方式提供的 API 将被与服务通信的[**searchinteractoriimpl**](https://github.com/mohsenoid/marvel/blob/master/core-lib/src/main/java/com/mirhoseini/marvel/character/search/SearchInteractorImpl.java)使用。**改型**知道如何使用 **RxJava** 响应，你可以很容易地捕获结果，使用 Gson 解析它，并与其他 **RxJava** [操作符](http://reactivex.io/documentation/operators.html)(即映射、平面映射、过滤器等)一起使用。)来操作和准备响应，并将其提交给**视图**层以呈现给用户。

# 暂时就这样了…

![](img/779d029dc96d0218ee0044d44579f5d8.png)

请从 GitHub 中克隆[项目 repo 并更加熟悉它，因为在下一部分中，我将解释更多关于 RxJava 和 RxAndroid 以及它如何帮助将响应从*核心*传播到**视图**层。](https://github.com/mohsenoid/marvel)

我期待您的评论，并帮助我对这篇文章进行更多的改进。

如果你认为这篇文章有用，请分享，并关注我的更多文章[莫森·米罗西尼](https://medium.com/u/6a4ed0c4dd2c?source=post_page-----23757f4eee05--------------------------------)。

> 未完待续…

[](https://hackernoon.com/yet-another-mvp-article-part-4-rxjava-and-rxandroid-knows-how-to-response-cde42ccc4958) [## 另一篇 MVP 文章——第 4 部分:RxJava 和 RxAndroid 知道如何响应

### 使用 MVP、RxJava、Dagger 2、Retrofit2、Test 和所有全新的现代技术实现一个示例 Android 应用程序…

hackernoon.com](https://hackernoon.com/yet-another-mvp-article-part-4-rxjava-and-rxandroid-knows-how-to-response-cde42ccc4958) [![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)