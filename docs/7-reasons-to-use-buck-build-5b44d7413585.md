# 使用降压构建的 7 个理由

> 原文：<https://medium.com/hackernoon/7-reasons-to-use-buck-build-5b44d7413585>

Buck 是一个跨平台、跨语言的编译系统，在脸书为大规模编译而设计。所有的 [Buckaroo](https://www.buckaroo.pm) 软件包都使用 Buck 作为构建系统，所以不用说我们是它们的忠实粉丝。

然而，即使你不在脸书规模，这里有 7 个理由，我们认为你应该给巴克一个尝试。

# 1.成熟的技术

Buck 不仅仅是一个业余爱好项目，当创作者失去兴趣时，它可能会变得不受支持。仅举几个例子，它就被用于脸书、优步和 Dropbox 的生产中。最重要的是，Buck 团队中的许多人都是前谷歌人，他们曾在 Blaze 工作，Blaze 是 Buck(和 Bazel)的开源前身。Buck 是关于构建系统的大量知识的顶点。

# 2.更正增量构建

你有没有删除过你的构建文件夹*以防万一*？在编译之前，Buck 将源文件复制(或符号链接)到登台区，以便将依赖关系放入沙箱。每个构建输入(头、标志和环境)都被用作构建工件的缓存键。这种训练有素的方法意味着 Buck 每次都能给你正确的增量构建。

# 3.私有标题

对于 C/C++，Buck 允许您将导出的头(库用户可用的头)与私有头(用于构建库的头)分开。这对于 C/C++代码的模块化来说是一个巨大的胜利，因为它防止了其他模块到达你的 API 后面。

# 4.哈希，而不是时间戳

Buck 知道何时通过输入的散列来重建目标。这意味着减少了开发人员浪费在不必要的重建上的时间，因为您接触了文件，但没有更改。Buck 与分支 VCS(如 Git)配合得非常好。

# 5.一种合适的脚本语言

任何使用过 CMake 的人都会理解使用蹩脚的脚本语言的沮丧。Buck 没有重新发明轮子，而是使用 Python 来构建脚本。

然而，Buck 以一种受控的方式使用 Python。Python 脚本用于生成构建目标，而不是执行构建本身。这允许 Buck 在单线程中执行 Python 代码，但之后并行执行构建。额外的好处是，这意味着 Python 的结果可以被缓存。

# 6.更快的构建

Buck 理解你的项目的依赖图，允许它并行地构建独立的工件。然而与`make -j4`不同的是，Buck 永远是确定性的。您甚至可以使用 [Buck Server](https://github.com/uber/buck-http-cache) 在您的团队中共享增量编译结果。

# 7.容易推理

也许 Buck 最好的特性是构建文件很容易推理。每个目标只能使用它声明的依赖，依赖循环是不允许的，构建输出之间的递归是不可能的。

即使您以前从未使用过 Buck，您也可以理解这一点:

# 深信不疑？

前往 Buck 网站开始吧。

# 您可能也对…感兴趣

[](/@buckaroo.pm/even-more-reasons-to-use-buck-build-9e2f6bf451d4) [## (甚至)更多使用降压构建的理由

### 出色的预编译头文件支持

medium.com](/@buckaroo.pm/even-more-reasons-to-use-buck-build-9e2f6bf451d4) [](https://hackernoon.com/getting-started-with-buck-build-on-travis-ci-d1208d363023) [## 从基于 Travis CI 的 Buck build 开始

### Travis CI 是一个为开源项目提供免费构建服务器的服务(感谢，VCs！).它具有紧密的集成…

hackernoon.com](https://hackernoon.com/getting-started-with-buck-build-on-travis-ci-d1208d363023) 

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)，[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！