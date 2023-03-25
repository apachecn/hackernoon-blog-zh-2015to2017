# 我们如何测试 20 万行 Ember 应用在<10 minutes. Again!

> 原文：<https://medium.com/hackernoon/how-we-got-tests-for-200k-lines-ember-application-running-10-minutes-again-1fa7a4c5af2f>

![](img/a5bbffbd2695268a0983aeedf79bf900.png)

# Shipping as a heartbeat

The ability to ship the new version of your application fast has many advantages. Building a new feature is enjoyable — you can split it in more pieces and make sure each works and scales properly. When you’re refactoring, you can refactor a really small piece and test it out. Oops, you made a mistake?! No problem — you can apply a fix and ship it fast. Putting your code into production frequently changes how you work. Every mistake has a shorter (and smaller) impact so fear of mistakes become smaller. The easier it is to fix a mistake, the more eager developers are to change the app. It’s very simple — like drawing with a pen versus a pencil and an eraser! Which one is more suitable for [学习](https://hackernoon.com/tagged/learning)怎么画？

# 大约 40 分钟后发货内部通信前端应用

我们在对讲机中建立的流程看起来像是 PR ->快速回顾->测试**->合并成 master - > **测试** - >部署！由于编写测试比每次更改后点击应用程序更便宜，所以随着时间的推移，我们的应用程序会得到越来越多的测试。更多的测试，更长的测试时间。测试时间更长，从我的电脑到客户的时间更长。事实上，测试阶段执行两次，一次在特性分支上，一次在主分支上，以确保主分支在合并后是健康的。这意味着每一项新的测试都会增加`2 x test_duration`的生产时间。在某些时候，我们的测试运行了 15 分钟。当我们加倍的时候，我们得到 30 分钟的等待时间仅仅是为了测试。除此之外，我们还需要几分钟的时间来创建一个版本，压缩资产，缩小 javascript 等等，现在我们的更改需要 40 分钟才能投入生产！那真的很慢！如果我犯了一个错误，我必须回滚，而不是向前滚动，并确保我的修复是正确的。**

# ****测试阶段分解****

**我很少在我们的前端应用上工作，所以我知道 [codebas](https://hackernoon.com/tagged/codebase) e 远远少于后端服务，这意味着我在那里犯更多的错误。我犯的每一个错误的成本都要高得多(时间方面)，对此我真的很恼火。我抱着“我会让它更快”的无知态度，开始钻研我们的测试设置。这很常见:**

*   **准备软件依赖关系(yarn、npm、bower 等)**
*   **检查代码**
*   **构建应用程序**
*   **运行测试**

**我开始计算不同阶段的时间，发现大多数阶段都被大量缓存，用时不到一分钟，但构建应用程序却花了 4 分钟！4 分钟？！当我在本地更改一个文件时，我可以在几秒钟内看到更改。为什么我在测试环境中不能有相同的构建时间？**

# ****西兰花缓存来拯救！****

**因为我不知道 ember 是如何构建工作的，所以我开始深入研究它(1)。原来 ember 增量构建背后的组件是 [Broccoli.js](http://broccolijs.com/) 。它有一些魔力(它实际上不是魔力，而是一点点 CS 和软件工程)来重建只是改变文件和依赖于它们的文件，这通常是一个或两个文件，并且花费远远少于四分钟。还发现有一个环境变量`BROCCOLI_PERSISTENT_FILTER_CACHE_ROOT`定义了缓存的位置。Tada！确保目录在您的构建之间共享，这样它们可以是增量的，就像在开发环境中经历的那样。许多配置项支持构建之间的某种缓存。**

**例如，我们的 CircleCI 配置如下所示:**

```
machine:
...
  environment:
    BROCCOLI_PERSISTENT_FILTER_CACHE_ROOT: "/home/ubuntu/embercom/persisted-cache"
...
dependencies:
    cache_directories:
      - "/home/ubuntu/embercom/persisted-cache"
```

**幸运的是，CircleCI 缓存每个分支的[,所以当依赖项升级时缓存不能被使用。在所有其他情况下，它将我们的测试时间减少了三分半钟——总共七分钟！对于一个投入了一天时间的新人来说，这绝对不是一个坏结果！](https://circleci.com/docs/how-cache-works/#per-branch-cache)**

# ****进一步改进****

**我们的基础设施团队开始使用 [BuildKite](https://buildkite.com) (到目前为止我们很喜欢它！)并构建了 Docker 映像，只需要为新提交重新构建很少的*层*。通常只是复制新文件，平均节省 2 分钟。我相信他们很快会写一篇关于这个的博文，所以请保持关注(我会让你保持更新；)).**

**因为 BuildKite 不像 CircleCI 那样提供开箱即用的缓存，所以我们必须自己实现它。我们对 S3 使用非常简单的实现。可以查看一下 [buildite-s3-cache](https://github.com/mariokostelac/buildkite-s3-cache) (声明:这不是 Intercom 在生产中用的)。示例用法:**

```
export BUCKET_PATH="<bucket_name>/<path>"
source set_unique_cache_dir.sh
./download_cache.sh
./run_my_tests.sh ${CACHE_DIR}
./upload_cache.sh
```

**并获得缓存构建的所有好处！**

**关于改进 ember 测试运行时间，你有其他的建议吗？！告诉我！**

**(1)其实，“挖掘”是由我和[加文·乔伊斯](https://twitter.com/gavinjoyce)在我们公司酒吧喝酒开始的。**

**[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)****[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)****[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)**

> **[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。**
> 
> **如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！**

**![](img/be0ca55ba73a573dce11effb2ee80d56.png)**