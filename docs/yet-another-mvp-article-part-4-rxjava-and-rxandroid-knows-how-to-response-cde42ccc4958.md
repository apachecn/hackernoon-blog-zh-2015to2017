# 另一篇 MVP 文章——第 4 部分:RxJava 和 RxAndroid 知道如何响应

> 原文：<https://medium.com/hackernoon/yet-another-mvp-article-part-4-rxjava-and-rxandroid-knows-how-to-response-cde42ccc4958>

*使用* ***MVP*** *，****rx Java****，****Dagger 2****，****retro fit 2****，****Test****，以及所有全新的现代方法和库*

# 本文前情提要…

[](https://hackernoon.com/yet-another-mvp-article-part-3-calling-apis-using-retrofit-23757f4eee05) [## 另一篇 MVP 文章——第 3 部分:使用改型调用 API

### 使用 MVP、RxJava、Dagger 2、Retrofit2、Test 和所有全新的现代技术实现一个示例 Android 应用程序…

hackernoon.com](https://hackernoon.com/yet-another-mvp-article-part-3-calling-apis-using-retrofit-23757f4eee05) ![](img/a515d82a58411a8153f93f243e4dd3ac.png)

假设您已经学习了本文的 [part1](/@mohsenoid/yet-another-mvp-article-part-1-lets-get-to-know-the-project-d3fd553b3e21#.6y9ze7e55) 、 [part2](/@mohsenoid/yet-another-mvp-article-part-2-how-dagger-helps-with-the-project-90d049a45e00#.62mdwkqwj) 和 [part3](/@mohsenoid/yet-another-mvp-article-part-3-calling-apis-using-retrofit-23757f4eee05#.anv029kvw) ，这意味着您对[示例项目的](https://github.com/mohsenoid/marvel)模块和结构了如指掌，也更加熟悉 **Dagger** 在连接 **MVP** 层中的用法，并且知道**改型**用于连接到[网络](https://hackernoon.com/tagged/network)API 的用法。

现在让我们继续…

# RxJava 是一场革命，真实还是虚构！

在我作为软件开发人员的整个职业生涯中，我可以说 RxJava 是 Java 8 中继表达式之后最好的变化之一。通过遵循混合这两者的配方，你可以像一个[魔术师](https://hackernoon.com/tagged/magician)一样开发软件。

据 **RxJava** 官网[react vex . io](http://reactivex.io)介绍，是*“一个异步编程的 API”*在 Android 开发中总是需要的。

幸运的是，这个图书馆网站上的文档非常完美，你可以通过大量的图表找到你想知道的东西。

# …，所以让我们回到我们的示例项目:

在这个项目中，RxJava 主要用于从**模型**层检索数据，而**表示**层调用**视图**层的方法来处理响应。

**可观察对象**由于调用 API 方法而被提供改造库，并被 **MVP** 的**表示层**观察。

RxJava 的神奇之处在于它可以在不同的线程中被观察和订阅。因为我们总是将结果传递给 Android UI 线程(即 **MainThread)** ，所以我们需要一个从 *app* 模块到 *core* 模块中该线程的链接。

# 如何从 Android 到内核指定调度器！

![](img/15efe5950a342fc298578f4d2cdc83ee.png)

RxJava 中的 [**调度器**](http://reactivex.io/documentation/scheduler.html) 是 Java 中的线程。为了将 API 的响应传递给视图层，你必须将它推入 Android 主线程。

为了说明这一点，我们使用 **RxJava** 的**观察**方法。

RxJava 有一个名为 **RxAndroid** 的 Android 扩展，它提供了与 RxJava 一起使用的 Android 线程。为了使*内核*和 *app* 模块之间的这种连接成为可能， *app* 模块从[**AppSchedulerProvider**](https://github.com/mohsenoid/marvel/blob/master/app/src/main/java/com/mirhoseini/marvel/util/AppSchedulerProvider.java)类中的*内核*实现了[**scheduler provider**](https://github.com/mohsenoid/marvel/blob/master/core-lib/src/main/java/com/mirhoseini/marvel/util/SchedulerProvider.java)*接口*，该类实现了两种方法(mainThread/backgroundThread):

![](img/0ab1d80d1f1d8e4fb3e1296a073ccdde.png)

“Take care of the wrong abstraction”, — [Novoda](https://www.novoda.com/blog/designing-something-solid/)

，最后通过一些 **Dagger** 注入，我们可以将 **AppSchedulerProvider** 作为 presenter 类中的父类( **SchedulerProvider)** ，这是 **MVP** 中 [Liskov 替换](https://en.wikipedia.org/wiki/Liskov_substitution_principle)的另一个例子。

# RxJava 地图操作来求助…

RxJava 有许多有用的操作符，但我认为 **MAP** 是使用最多的操作符。

看看[**search present erimpl**](https://github.com/mohsenoid/marvel/blob/master/core-lib/src/main/java/com/mirhoseini/marvel/character/search/SearchPresenterImpl.java)里面的这部分代码，做字符搜索:

来自 API 的响应并不总是我们想要交给**视图**层的，甚至可能需要一些**数据库**缓存，因此，使用 [**映射**](http://reactivex.io/documentation/operators/map.html) 操作符我们可以在 UI 知道之前做一些手脚。

在[**search presenterimpl**](https://github.com/mohsenoid/marvel/blob/master/core-lib/src/main/java/com/mirhoseini/marvel/character/search/SearchPresenterImpl.java)**:**中有四个应用于 API 响应的映射操作符

*   第一个映射，检查 API 的响应代码是否正确，如果不正确，稍后将抛出一个异常。确保您使用了 *'Exceptions.propagate'* 方法，否则您必须使用 try/catch 语句立即处理异常！！
*   第二个地图，检查是否有任何搜索字符的结果，如果没有，稍后将抛出一个异常…
*   第三个映射使用 Mapper 类将响应模型映射到数据库模型。
*   最后，最后一个映射，将响应插入到数据库中进行进一步的缓存。

![](img/bcf04a110511c484afd6df774d5c72ec.png)

使用 **RxJava** ，所有这些对 API 响应的操作都可以在一行代码中实现！

…记住，所有的映射都发生在后台线程中，因为我们在 [**类中使用了 subscribe*来指定它，这就是它永远不会中断**主线程**来导致任何延迟甚至***](https://github.com/mohsenoid/marvel/blob/master/core-lib/src/main/java/com/mirhoseini/marvel/character/search/SearchInteractorImpl.java) ***[ANRs](https://developer.android.com/training/articles/perf-anr.html) 的原因。***

## 还有哪里？

RxJava 在 [**适配器**](https://github.com/mohsenoid/marvel/blob/master/app/src/main/java/com/mirhoseini/marvel/character/cache/adapter/CharactersRecyclerViewAdapter.java) 中也在使用，用于通知列表点击中的项目，我觉得很好理解，大家可以看看。

# 暂时就这样了…

![](img/779d029dc96d0218ee0044d44579f5d8.png)

请从 GitHub 中克隆[项目回购，并更加熟悉它，因为在下一部分，我将解释更多关于使用我们所做的所有 dagger 东西编写测试的内容。](https://github.com/mohsenoid/marvel)

我期待您的评论，并帮助我对这篇文章进行更多的改进。

如果你觉得这篇文章有用就分享吧，关注我更多文章 [Mohsen Mirhoseini](https://medium.com/u/6a4ed0c4dd2c?source=post_page-----cde42ccc4958--------------------------------) 。

> 未完待续…

[](https://hackernoon.com/yet-another-mvp-article-part-5-writing-test-using-a-mixture-of-dagger-and-espresso-15c638182706) [## 另一篇 MVP 文章——第 5 部分:混合使用匕首和浓缩咖啡的写作测试

### 使用 MVP、RxJava、Dagger 2、Retrofit2、Test 和所有全新的现代技术实现一个示例 Android 应用程序…

hackernoon.com](https://hackernoon.com/yet-another-mvp-article-part-5-writing-test-using-a-mixture-of-dagger-and-espresso-15c638182706) [![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)