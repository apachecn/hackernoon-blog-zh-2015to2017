# 作为一名 Android 开发人员，在做了一些网络开发后，我学到了 5 件事

> 原文：<https://medium.com/hackernoon/5-things-i-learned-as-an-android-developer-after-doing-some-web-development-35268cd47c38>

![](img/6274e84d671aabfe11e6facc83df8982.png)

在过去的几个月里，我开始了我的新旅程，离开了我的 Android 位置，开始做一些网络开发。在这两个平台上工作有很多不同之处，我不能说一个比另一个好，但是这两个世界可以互相学习很多东西。

在这篇文章中，我将重点讲述我在开发我们的前端 React 应用程序时看到的事情。我了解到的关于 Android 和我们开发应用程序的方式。

# 1.我们写了太多代码

创建一个自定义视图，即使是最简单的视图，也可能会占用 1 个太长的视图子类。这个类应该包含几个构造函数、getter/setter，读取和解析属性，并扩展一个 XML，这甚至没有提到布局文件。在 react 上，你应该只实现 render 方法(也可能是定制的 CSS/SCSS 文件)。

例如，这里有一个简单的用户信息小部件，用 Android 和 React 编写:

我不谈论 JSON 解析、网络调用或保存状态之类的事情。

这主要是因为在 Android vs JavaScript 上使用 Java 这样的语言。如果你使用 Kotlin，你可以去掉一些样板文件，或者使用[数据绑定](https://developer.android.com/topic/libraries/data-binding/index.html)在布局文件中完成更多工作。

# 2.开发反馈周期太长

在 Android studio 中，需要多长时间才能在设备(虚拟或物理)上看到代码更改？如果你改变像文本或颜色这样的小事情，谷歌说应该只需要几秒钟(当即时运行启用时)，大多数情况下需要几十秒钟，甚至可能需要一两分钟。此外，只有当您单击“运行”时，更改才会生效。

使用 Webpack(一个模块捆绑器，或者“编译器”，如果你想的话)时，React 需要多长时间？立刻。像第二或第二。这就发生在你保存文件的时候。换错元素了？嘣！你马上就能看到。

不要以为 5 秒或 10 秒真的有那么大区别？发展时——每一秒都很重要(很快所有这些秒都会累积起来)。尤其是当这些变化被动地显示(当文件被保存时)和主动地显示(当你运行应用程序时)时。我真的推荐你去看这个[演讲](https://vimeo.com/36579366)，看看它会如何影响你的发展(警告:演讲者会让你大吃一惊)。

# 3.使用 Flux 架构

Flux 是脸书用来构建客户端网络应用的应用架构。它通过利用单向数据流来补充 React 的可组合视图组件。它更像是一种模式，而不是一个正式的框架，当结合 React Router 和 Redux 时，我们可以得到一些非常接近 Flux 模式的数据流。

Flux 使得编写客户端更加干净，更容易调试和预测。尽管有一些用于 Android 的[示例](https://github.com/lgvalle/android-flux-todo-app)和[库](https://github.com/frostymarvelous/Fluxxan)，但它仍然没有被广泛使用。

在 Android 应用程序上，我们仍然以各种方式传递数据(intents、DB、消息总线等)，保持数据的可变性，每个组件都可以改变它，这使得我们的生活变得非常艰难。

# 4.将内容与外观分开

当在 Android 上建立一个页面时，我也必须建立它的布局。我需要安排页面及其组件，并以某种方式将它们放在视图中。即使我只想专注于行为——我必须以某种方式玩线性布局、滚动布局之类的东西。

然后，当我需要设计视图时，我需要再次返回布局文件并添加所有的魔法。然而，当我需要添加动画时，我需要把它放在代码中。

视图类和视图布局没有那么分离，很难只关注行为而不处理外观和感觉。在安卓里，太耦合了。

但是使用 React，您可以将所有与 UI 相关的代码提取到样式表文件中。例如，如果我想创建一个简单的小部件，通过单击来改变视图状态，我会这样做( [JSFddle](https://jsfiddle.net/reactjs/69z2wepo/) ):

在 SCSS 文件中，我将决定禁用的项目看起来像什么(隐藏？灰色熄灭？我现在不在乎)。

但是如果我想在 Android 中这样做，我需要在视图类(或者它的父类，或者 OnClick 监听器)中改变它。

# 5.我们可以得到更好的开发工具

你还记得你最后一次在 Android studio 中选择“调试”吗(我打赌很可能是误选的)？你还记得你等待应用程序打开和调试器连接的那 1-2 分钟吗？或者你把调试器附加到一个正在运行的应用程序上，突然一切都慢得要命？

你还记得那次你的视图之间有一个奇怪的间距，你不得不打开开发者选项设置中的一些标志，并希望你能从屏幕上的一些随机矩形中理解每个视图的边界吗？

您是否记得试图找出您的数据库发生了什么，因此您不得不转储数据库并从设备中取出它？

您还记得您想在设备上尝试一些视图中的东西，但每次都需要更改代码并重新运行吗？

Chrome(和 Firefox)开发者工具使得调试和玩你的应用变得如此容易，这是不公平的。您可以随时更改视图，轻松检查您的元素，并在控制台中运行一些代码。

Web 开发真的很好(实际上比我想象的要困难得多)，但我心里仍然对 Android 有一个更温暖的地方。虽然比较这两者是不公平的(Android 需要在远程设备或模拟器上工作，它比 web“年轻”得多)，但我认为这两个社区可以互相学习。

至于 web 开发能从 Android 开发中吸取什么- [在下期](/@shemag8/5-things-react-world-can-learn-from-android-56e36b7aa071)的帖子里。

*如果你喜欢这篇文章，请点击下面的“推荐”按钮，分享这个故事。我也喜欢听你的评论。谢谢！*

*原载于 2017 年 3 月 31 日*[*she M8 . github . io*](http://shem8.github.io/blog/2017/03/31/5-things-i-learned-as-an-android-developer-after-doing-some-web-development/)*。*

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)