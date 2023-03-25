# YAC-Kotlin 总结和一些提示

> 原文：<https://medium.com/hackernoon/yac-kotlin-wrapup-some-tips-707c5e26f901>

我的天啊。！！ [Kotlin](https://hackernoon.com/tagged/kotlin) 现已获得 Android 官方支持。当我在看[谷歌](https://hackernoon.com/tagged/google) I/O 直播的时候，正是印度的睡眠时间，当我看到那个锁的图案成为我们最喜欢的 Kotlin 标志时，我欣喜若狂，差点把我的长辈们吵醒…

但这是值得的，毫无疑问，这一宣布受到了广泛的欢呼和热情。和它一起工作了一年，这对我们来说都是一种接受和解脱。非常感谢谷歌。

最初，我打算写这篇文章来强调 Kotlin 如何对我们有益的一些小细节。但是从 5 月 17 日到今天，很多关于科特林的报道被强调和书写。我不会在这里重复所有的内容，也不会写第一百万篇关于 kotlin 对空指针有多棒的文章…我会稍微改变一下路线。

首先，我有一些经验来分享一些小技巧和诀窍，可能会帮助你与 Kotlin 一起工作，在稍后(保持最初的想法)我将宣布一些开源作品。

# **一些提示和陷阱。**

**入门**

*   如果你是从 kotlin 探索开始，我建议从 kotlin koans 开始。它们是掌握这门语言的很好的教程。
*   不要从浏览器入手，诱惑摆在那里，马上就可以入手。但是我认为开始 koans 的最好方法是在你的 IDE 中。使用 IDE，你会比使用浏览器对语言和环境有更好的了解。
*   如果你无法在 Android Studio 中启动 koans，请使用 Intellij Idea 和 Kotlin Education 插件。使用 Android studio preview(3.0 preview 2)，我们无法运行测试。我们安装了几个 intellij 社区版来测试 flutter，发现 Kotlin Education 插件与在 android 中运行 koans 的方式几乎相同

**转换你的代码。**

*   有一个快捷方式来转换你的文件(我有` Alt+Ctrl+Shift+K `)，尝试有一个提交之前，你转换你的文件，所以你可以比较和恢复，如果需要的话。
*   首先从大多数独立的文件开始，这样可以减少需要提交的更改。我们试图转换 utils 文件(有我们所有的静态工具方法)，它改变了我们应用程序中的几乎每个类。我们最终恢复了这些变化。
*   与上述观点一致，MVP 中的演示者可以被认为是独立的类。对于每个演示者，我们只需要转换 3 个类-演示者接口，演示者实现和视图接口。(旁注:仅仅通过转换表示器，我们就减少了 1000 行，有效地减少了所有与空指针和索引相关的崩溃)
*   当您的 java 类调用 kotlin 演示者时，可以添加可空符号(？)在所有参数中。java 很有可能在 Kotlin 不希望它为 null 的地方传递 null。编译器也不会抱怨。在 java 中，当 kotlin 不返回任何空值时，会强制使用`@NotNull '注释，但是当传递给 kotlin 时，不会强制使用任何类型的注释。(不能说 Lint 或推断注释，我没有尝试过它们)
*   我们可以在没有插件的情况下将我们的数据类转换成 parcelables(它在最新的 Android Studio 3.0 Preview 5 中有效)。只要有基本类型作为属性，它就能完美地工作。如果您的类有其他类型或数组列表，parcelable 不会为它们生成读或写实现。它生成一个“TODO()”。你可以通过使用[这个插件](https://github.com/nekocode/android-parcelable-intellij-plugin-kotlin)来克服这个问题。

**图书馆。**

正如我在这篇文章开始时所说的。Android 主持 Kotlin 愉快地改变了很多事情，包括这篇文章的过程。我将讨论更多来自 YAC 的 kotlin 代码，它试图指出这种语言的力量。由于 I/O，这些问题在很多帖子中被讨论了很多次。

但这并不改变分享一些源于 YAC 的开源作品，或者 YAC 对其进行了升级。

第一个是 Kotlin 的老库的端口 [Android 外部文件编写器](https://github.com/PrashamTrivedi/AndroidExternalFileWriter),[kot Lin 端口](https://github.com/PrashamTrivedi/AndroidExternalFileWriter/tree/master/androidexternalfilewriter-kotlin)是几乎相同的代码，在 YAC 应用中打开文件浏览器并从中读取响应。你可以把它放在你的 gradle 文件的下面

```
compile**/**implementation `com**.**creativeelites**:**androidexternalfilewriter**-**kotlin:0.3`
```

第二个是[库蒂尔斯](https://github.com/CreativeElites/UtilsLibrary/tree/master/kutils)。这个库是我在项目中使用的扩展函数的集合。这个库开始是作为 Kaffeine 的分支，自过去两年没有更新。这个项目的保留结构，我开发了 KUtils 添加方法，并解决了 Kotlin 和 YAC 发展过程中的一些错误。

我还添加了一些来自 Anko 的扩展和方法。当我开始的时候，Anko 是一个巨大的用于 layuot、扩展和 sqlite 的库。从那天到现在，为了安心，anko 被拆分成多个库。让我加入安科不是很好吗？为什么要为了复制代码而开发自己的库呢？

答案是我觉得 anko 要么是停滞不前，要么对使用它的人来说不是很有用。除了扩展功能之外，我从来没有感觉到使用 anko 的冲动。社区中有些成员使用 anko 进行布局，但对我来说，Anko 是很好的(和官方的)扩展函数集合，用于常见的东西、Sqlite 和布局。构建 Anko 的指令(对我来说)对我来说不清楚是用于插件还是库。我有一些问题，直到这方面的困惑被清除，我已经在库蒂尔斯库托管这个代码。您可以通过以下方式使用 KUtils

```
compile**/**implementation `com**.**creativeelites**:**kutils:0.7**.**1@aar`
```

这就是科特林和我的图书馆。在下一个关于 YAC 的子系列中，我将讨论我是如何使用 firebase 来处理 YAC 的一些琐碎用例的。

原发表[此处](https://www.prashamhtrivedi.in/yac_kotlin_wrapup)。

[](https://play.google.com/store/apps/details?id=com.celites.yac) [## YAC-Google Play 上的 Android 应用

### 另一种(智能)清洁器

play.google.com](https://play.google.com/store/apps/details?id=com.celites.yac) 

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)，[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！