# React World 可以向 Android 学习的 5 件事

> 原文：<https://medium.com/hackernoon/5-things-react-world-can-learn-from-android-56e36b7aa071>

![](img/ef35f5af4436d67714b4d33996d1e5b7.png)

在我的[上一篇](https://hackernoon.com/5-things-i-learned-as-an-android-developer-after-doing-some-web-development-35268cd47c38)文章中，我谈到了我从我通常的 Android 开发人员的工作中脱离出来并做了一些 web 开发(尤其是 React)后注意到的一些事情。

正如我所承诺的，在列举了 Android 可以从 web 开发世界学习的一些东西之后，我现在将重点放在另一方面——web 开发人员可以从 Android 世界借鉴什么。

所以让我们开始*(戴上我的头盔)*:

![](img/ac0ef4ec505bfd95ac37e085dfa97d02.png)

# 1.一个像样的 IDE

哦，是的，我马上就触动了神经。但是对不起，这是没有办法的。

> Web 开发人员没有像样的 IDE，他们应该使用一个。

我们有优秀的文字编辑，但这是不一样的。

我为什么要手动编写我的导入？当我不确定我是否重命名了我的方法的所有实例时，我如何和平地重构我的代码？为什么我不能在源代码中轻松导航？

作为一个来自一个有着伟大 IDEs 的世界(Java 有 Eclipse 和 IntlliJ)的人，这是我开发时最困扰我的事情。

谷歌把它带到了一个新的高度，他们有一个团队正在开发 Android 的开发工具。我不是说我们必须使用像 Android Studio 这样的专用 IDE，但至少给我一些像样的东西来工作。

# 2.停止那些该死的待办事项应用程序

没有真的，WTF！？一个男人需要多少个待办事项应用？为什么每个人都在反复写同样的东西？

是的，我们知道-你写你的任务，它在 Redux 上保存，你可以完成一些任务，然后可以看到正在进行的和已经完成的。我发誓我在学习 React 的时候看到过 5 次类似应用程序的东西。

那么下一步呢——看看如何与服务器同步数据，放置多种类型的数据，处理用户输入，加载或任何其他边缘情况。

谷歌建立真正的应用程序并发布源代码。比如——[专注于设计和动画的格子 app](https://github.com/nickbutcher/plaid) ，或者[处理图像的木子 app](https://github.com/nickbutcher/plaid) ，甚至[官方的 Google I/O app](https://github.com/google/iosched) 。

这些都是有真实用户和真实用例的真实应用。有趣的是，有经验的开发人员如何处理超过几百行代码的应用程序。

# 3.社区专家

[谷歌开发者专家(GDEs)](https://developers.google.com/experts/about) 是一个由经验丰富的开发者(和其他人)组成的全球网络，积极支持开发者、初创公司和公司通过网络和移动应用改变世界。

这意味着谷歌鼓励(也补偿)个人帮助并教育社区关于 Android 的知识。

想想你自己——如果你有一个问题，或者一些不工作，或者需要某种建议。你问谁(或哪里)？

帮助社区使用该平台是开发新平台不可或缺的一部分，我觉得脸书在这方面可以做得更好。

# 4.谈论的不仅仅是代码。

React 相对简单。你可以在几个小时内轻松安装并运行一个应用程序。然而，一个真正的应用程序不仅仅是几个屏幕和组件——它远不止如此。它处理奇怪的用户输入和连接错误。它让用户理解流程，让操作变得简单直观。

我希望脸书团队能给我一些建议，告诉我在这么长时间为这么多用户和用例构建 React 应用后所学到的经验教训。

就像谷歌分享类似[材料设计](https://developer.android.com/design/index.html)指南的东西一样，我很乐意在构建我的单页应用时听到更多关于最佳实践的信息。

![](img/aa0b75040b570d3d2ea730388f3b1d99.png)

# 5.处理架构组件(或将社区解决方案作为最佳实践)

因此，我们有了基本的东西，应用程序的骨架，很快我们就会编写高阶组件来处理更高级的东西。

这些组件可能会和我们一起(作为共享库或者更糟——作为复制和粘贴)转移到我们的下一个项目中。

我们并不孤单——所以我假设其他人也在他们自己的项目中编写这些代码。

因为我不是唯一一个需要编写一个组件来处理当前组件数据加载时的状态的人。我可能不是唯一一个有时想在存储和本地存储之间同步一些值的人。或动画组件。

在 Android 中，众所周知，翻新是 HTTP 调用的库，谷歌甚至在他们的[官方示例](https://developer.android.com/topic/libraries/architecture/guide.html#fetching_data)中使用它。对于其他事情，他们想出了自己的解决方案，如[架构组件](https://developer.android.com/topic/libraries/architecture/index.html)。

在 react 中有 [React 路由器](https://github.com/reacttraining/react-router)，被广泛使用。但是除此之外，我真的不知道这种事情。

我真的很喜欢 web 开发世界，而且我觉得这个社区的参与度要高得多(老实说，规模也更大)。但是我觉得这是一个应该站起来带领开发人员进入更有组织和结构化的环境的地方。

如果你喜欢这篇文章，请点击下面的**推荐**按钮，分享这个故事。我也喜欢听你的评论。谢谢！

*原载于 2017 年 7 月 2 日*[*she M8 . github . io*](http://shem8.github.io/blog/2017/07/02/5-things-react-world-can-learn-from-android/)*。*