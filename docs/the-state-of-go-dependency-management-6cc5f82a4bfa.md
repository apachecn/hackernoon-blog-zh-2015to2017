# Go 依赖管理的状态

> 原文：<https://medium.com/hackernoon/the-state-of-go-dependency-management-6cc5f82a4bfa>

自从 Go 在 Google 之外引起了极大的关注，依赖管理就成了一个热门话题。

最初，除了`go get`之外没有依赖[管理](https://hackernoon.com/tagged/management)，它将源文件从`origin/master`加载到您的系统级`[$GOPATH](https://github.com/golang/go/wiki/GOPATH)`。对于内部依赖，这通常是可行的，因为每个人都应该使用最新的版本。然而，对于依赖第三方的团队来说，这是不可接受的，因为存在不匹配、冲突、突破性的变更，因此出现了许多*特别的*方法，例如

*   提交特定于项目的$GOPATH
*   git [子模块](https://git-scm.com/book/en/v2/Git-Tools-Submodules) ( [和](https://github.com/ingydotnet/git-subrepo) [变体](https://git-scm.com/book/en/v1/Git-Tools-Subtree-Merging))
*   在特定项目的`$GOPATH`之间自动切换的工具，如`[gvm](https://github.com/moovweb/gvm)`和`[gb](https://github.com/constabulary/gb)`

最终，人们意识到我们需要一些更好的东西…一些一致的、实际上跟踪代码提交的实际版本(提交 sha、标签)的东西…一些简单的东西。为了“解决”这种需求，最流行的解决方案——`[godep](https://github.com/tools/godep)`——上升到了流行。Godep 允许你“轻松地”

*   将`$GOPATH`中的依赖项固定到项目中
*   将依赖项从项目复制到您的$GOPATH
*   浏览通过 Godeps.json 固定的依赖项

尽管 Godep 是对现有方法的巨大改进，但它肯定不能解决所有问题。它有许多问题。

首先，Godep 有许多让用户感到沮丧的缺陷、怪癖和矛盾之处。许多人认为这扼杀了这个项目。我不同意。

这些“怪癖”大多是因为 Godep 还是围绕`$GOPATH`转。要更新项目的依赖关系，您必须通过`go get -u github.com/some/dep`在您的`$GOPATH`中更新它，然后，通过`godep save github.com/some/dep`将它从您的`$GOPATH`复制到您项目的`Godeps/`。由于大多数开发人员在`$GOPATH`之外工作，当你在`$GOPATH`中修改一个依赖项时，这是很烦人的。此外，由于大多数开发人员只有一个`$GOPATH`，同时使用不同版本的依赖项来更新多个项目的依赖项是压倒性的。

到 Go 1.5(2015 年年中)，已经有了很多不同的做法，但没有标准。所以 Go 团队意识到是时候通过起草“[供应商实验](https://docs.google.com/document/d/1Bz5-UB7g2uPBdOx-rw5t9MxJwkfpx90cqG9AFL0JAYo/edit)”来介入了。启用实验后，Go 的`import`更喜欢`vendor`文件夹而不是`$GOPATH/src`。在 Go 1.6 中，默认情况下启用供应商实验。立刻，许多工具爆炸了，例如

*   glide ，一个固执己见的、笨重的、现代的包管理系统…非常类似于 ruby 的 gems
*   govendor ，一个快乐的中间人……我个人的最爱，尽管它可能有奇怪的命名/语法约定
*   gvt ，一个简单的售货工具……卖主的神坛
*   ……还有[多](https://github.com/govend/govend)，[多](https://github.com/skelterjohn/wgo)多[多](https://github.com/dpw/vendetta)

不要破坏将这样的东西集成到核心工具链中所需要的东西，但是除了 4 页的提议和宣传之外，“供应商”没有引入太多已经不存在的东西。`godep build`已经做了它提议的一切，甚至更多。

> 请注意，当“go get”获取一个新的依赖项时，它不会将它放在供应商目录中。一般来说，**将代码移入或移出供应商目录是供应商工具的工作，而不是 go 命令。**
> *—来自* [*Go 1.5 厂商实验*](https://docs.google.com/document/d/1Bz5-UB7g2uPBdOx-rw5t9MxJwkfpx90cqG9AFL0JAYo/edit) *建议*

事实上，供应商甚至没有提供一个工具来管理依赖关系，或者将它们放在第一位。然而，它所做的是为社区提供一个标准，告诉他们应该如何以及在哪里管理依赖关系。而且，很明显，这通常足以让软件社区开始运转。

围棋中的复仇是[还是](https://github.com/kardianos/govendor/issues) [非常](https://github.com/mattfarina/golang-broken-vendor) [破](https://github.com/golang/go/issues?utf8=%E2%9C%93&q=is%3Aissue%20is%3Aopen%20vendor)。幸运的是，Go 团队正在开发一种新工具——`[dep](https://github.com/golang/dep)`——这应该会大大改善这种情况。到目前为止，我遇到的所有关于 Go 依赖的问题都在他们的[特性](https://docs.google.com/document/d/1JNP6DgSK-c6KqveIhQk-n_HAw3hsZkL-okoleM43NgA/edit#)和[用户故事](https://docs.google.com/document/d/1wT8e8wBHMrSRHY4UF_60GCgyWGqvYye4THvaDARPySs/edit#heading=h.50hvk96hc71m)文档中解决了。当然，不能保证这些特性会在最初的版本中得到解决，但是很高兴知道团队认识到并有兴趣解决它们。从支持分叉到可传递依赖，到同一依赖的多个版本，再到支持私有代理，我真的为`dep`感到兴奋。

围棋是为了简单。简单是为了生产力。不必对抗依赖性将是一个巨大的优势！🎉

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！