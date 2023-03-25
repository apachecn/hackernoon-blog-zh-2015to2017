# YAC-为什么一年前我选择了 Kotlin 和 API 21+

> 原文：<https://medium.com/hackernoon/yac-why-i-chose-kotlin-and-api-21-a-year-ago-38742dca9a9a>

[第一部分](/@prashhtrivedi/yac-why-the-app-50b2daeb5400)

如果你有一个 Kitkat 设备并试图安装 [YAC](https://play.google.com/store/apps/details?id=com.celites.yac) 。您可以看到该应用程序与设备不兼容。这是因为我已经为该应用程序设置了最低 SDK 为 21，这意味着你只能在运行 lolipop 或更新版本的设备上使用该应用程序。

在这篇文章的前半部分，我将分享一些关于我为什么决定在我的应用中使用`MinSdk=21`的观点。这个应用是一个文件/目录处理应用。为了清理你的文件，YAC 需要知道你的文件，以及它们在哪里。你知道吗？YAC 做得很好，它甚至不需要许可，事实上，这个应用程序需要的唯一许可是互联网和引导接收，这是无害的，在安装时自动授予。扫描文件，并保持扫描，YAC 无缝地工作，无需请求许可。棒棒糖或以上真的帮助了我。

Lollipop 有一个 API 处理文件甚至不需要请求权限 [](#b151) [](#831b)。你可以向`Storage Access Framework`发送一个意图来读取**任何**文件树，它可以在任何地方读取你的文件(甚至在 SD 卡中)。旧的 API 则不是这样。对于 Kitkat，你所能拥有的只是一个文件，而不是一整棵树。对于较老的 API，我们所拥有的都是 hacks，它可能有效，也可能无效。至于扫描，我使用的是`JobScheduer`API。作业调度器是非常强大的 API，我们可以根据网络类型、设备充电与否等运行周期性任务，甚至不会影响我们的电池。这就是为什么 YAC 不会出现在你的顶级电池使用中。

但是等等…反向端口的存在是有原因的，对吗？我只需要支持我的存储和旧文件 API 并使用`GCMNetworkManager`或`FirebaseJobDispatcher`API？为什么要疏远 20%的用户？这是我的回答，当我开始的时候，21+已经在大约 50%的设备上运行了(如果我没记错的话)。这个数字——出于各种原因[，我在 reddit](https://www.reddit.com/r/androiddev/comments/3fv0at/beginner_dev_here_start_with_backwards/cts750w/) 上说——不会再下降了。作为一名独立开发者，在有限的资源和时间下，我必须向前看。这种 API 的用户水平不会降低。对于那些仍然使用 Kitkat 设备的用户来说，如果我努力，他们可以使用我的应用程序，但他们能够购买这个应用程序吗？如果他们有这样的购买力，为什么他们不能或者不买一部 6000-8000 印度卢比(90-100 美元)的手机，而现在这款手机的价格是 5.1 或 6.0。想到这一点，我决定坚持用棒棒糖作为我的最低支持水平。即使对于一些简单且经过验证的反向移植，我也不希望我的代码一团糟。

但是为什么要乱呢？只有几个`if else`街区，对吗？为什么像我这样的开发者不应该写它？这个决定是和其他决定一起做出的，其中最大的决定是如何处理我的代码库？当我想开始的时候，对 Java 6 的厌恶更强烈了，我已经看到了当你想使用 RxJava 时代码所造成的混乱。人们已经在使用 RetroLambda 来摆脱它，使用 lambdas， [kotlin](https://hackernoon.com/tagged/kotlin) 已经在拐角处(比 lambdas 更有前途)，正在加速，甚至没有进入稳定阶段。我想用一种新的语言开发一个应用程序，并想看看我能走多远。

经过一年的编码，以及将近 10-11 个版本的发布，我只能说我对 kotlin 印象深刻。它已经有了从一开始就困扰我们——Android[开发者——的问题的答案。它的空处理、数据类、开箱即用的功能 API(如 map、filter……)和 lambdas 等都是我们所需要的。回答这些问题不仅使我们变得强大，而且高阶函数、扩展函数、惰性初始化等特性也使语言变得更加强大。](https://hackernoon.com/tagged/android)

有多少次我们忘记了`show()`调用一个警告对话框并调试为什么我们的对话框不显示？有多少次我们忘记了调用`notify___Changed()`方法，并且想知道为什么屏幕上的数据没有更新？或者有多少次是我们忘记调用`commit`或`close`导致我们出现异常？正如杰克·沃顿在他的一次演讲中所说。当我们在 Android 中工作时，我们不得不处理这么多“仪式”的事情。从上下文开始，做 x，做 y，做 z，如果你在做 z，不要做 4…一旦你完成了，调用一个 finishing 方法告诉 SDK*我完成了你所要求的，现在只要用我提供的数据做你的工作*。这个 IMO 要做的事情太多，要记的东西太多，还有很多容易出错的样板文件。科特林只是帮助我们减少这种`ceremonial boilerplate`。

如何创建通知？用上下文创建一个`NotificationManagerCompat.Builder`对象。传递标题，内容文本，风格，小图标，大图标等…然后只通知一个唯一的 Id。在所有这些事情中，什么更重要？是专注于创建对象并调用 notify，还是专注于让通知对我们的用户更有用、更独特、更少打扰？正因为这些`ceremonial boilerplate`,我们更关注创建通知，而不是正确地创建通知。kotlin 使我们能够创建正确的通知，而不仅仅是创建通知。在我的例子中，我有一个`notify`方法，它接受一个`context`和一个`Int`以及一些`NotificationCompat.Builder`方法(是的，像`setTitle()` `setStyle()`等方法)，它将基于这些方法创建一个适当的通知，并自动发送一个通知。只要我通过了`context`，一个合适的`Int`和合适的方法(这真的很重要)，我就不用担心通知是否被触发..

你如何处理一些回调？从你的活动中，你如何倾听——并对——你的视图模型中发生的一些变化做出反应？在 java 中，您可能有一个接口，使您活动实现该接口，在您的 viewmodel 中，您有该接口的实例，并在有事发生时调用它的方法。如果您想使它成为可选的，您可能希望您的 viewmodel 的接口可为空，并添加一个空检查。

听起来太多了吧？但在科特林却不是。我不知道 Java 8，但是在 kotlin 中，你所要做的就是将 lambda 作为参数，并在需要时传递它。Kotlin 让我们有默认的参数，所以如果我不想传递参数，我就不必传递参数，在 Kotlin 中，我所做的就是定义一个默认的无操作方法 [](#033d) 作为 lambda 的默认参数，无论何时我想听到变化，我都可以传递我自己的 lambda，而不用太担心。

到目前为止，我已经陈述了 kotlin 对我们帮助很大的好的使用案例。但是我理解**没有代码，闲聊**毫无意义。但是这篇文章太长了，无法包含那么多代码。在下一篇文章[，](/@prashhtrivedi/kotlin-and-ceremonial-boilerplate-some-real-examples-from-my-app-4199b5c58869)中，我会讲更多关于科特林的例子。

原刊[此处](http://www.prashamhtrivedi.in/yac_why_kotlin)。

[](https://play.google.com/store/apps/details?id=com.celites.yac) [## YAC-Google Play 上的 Android 应用

### 另一种(智能)清洁器

play.google.com](https://play.google.com/store/apps/details?id=com.celites.yac) 

# 脚注

1.  事实上，当用户界面呈现给用户时，权限会被虚拟地询问，如果用户继续使用用户界面，应用程序得到肯定的响应，权限会被授予，我们只需要添加一些代码来保持这一点。如果用户取消它，我们没有得到许可。
2.  Ian Lake 在 2015 年的大型 Android BBQ 上发布的这个视频确实在文件和权限方面表现得很好，请看这个视频。依我看，你不需要一个文件权限，如果你有 10 个文件，用上下文的文件方法，用存储访问框架。

3.无操作:什么都不做，空方法

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请阅读我们的“关于”页面、[在脸书](http://bit.ly/HackernoonFB)上给我们点赞/发消息，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！