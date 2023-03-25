# Go 回购结构

> 原文：<https://medium.com/hackernoon/go-repo-structure-49bceabee492>

![](img/1da7f5b7d03f84992256b2b79547b7de.png)

Photo by [Sanwal Deen](http://unsplash.com/photos/GJCWj-n3h4E?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

在过去的几年里，我已经改变了我构建 Go repos 的方式几次，所以这里是我目前的观点以及与现在相比我过去做的事情。

## 重要的事情先来

像大多数人一样，当我在 2013 年开始查看 Go back 时，我的第一个回购非常简单。没有考虑任何形式的有用的布局。我发现的问题是

*   单个包/应用命名空间。这说明了一切。
*   如果它是一个可运行的程序，而不是一个包，那么任何有用的代码都不能在其他应用程序中使用，并且都在 *main* 下。
*   文件的数量会很快变大。

## 包是有用的

我发现几乎总是从一个包开始是非常有用的。第一，这迫使你至少要考虑从包的外部看代码应该是什么样子，第二，暴露代码的有用部分用于其他事情真的很容易。

至于如何做到这一点，我尝试了几种方式，并选定了这一点。

*   回购根中的主包站点。
*   任何其他包都在 pkg 文件夹下

就是这样。

比如说…

```
github.com/matzhouse/newPackage
                    /newPackage/ 
                    /newPackage/pkg/otherPackageOne
                    /newPackage/pkg/PackageTwo
```

## 二进制文件在哪里？

从一个包开始，我们还必须分离出二进制文件。使用 *cmd* 目录已经成为围棋的惯用方式。这有一些微妙的优势。

*   我们可以为不同的东西准备多个二进制文件——只需添加一个新的文件夹并编码即可。
*   我们可以把这个包作为一个包来使用，给我们一个不同的代码视角。

```
github.com/matzhouse/newPackage
                    /newPackage/cmd
                    /newPackage/cmd/commandOne
                    /newPackage/cmd/commandTwo
```

## 还有别的吗？

随着时间的推移，还有其他一些东西可能会变得更加普遍。

首先是插件。这些是预构建的包，可以在运行时导入到流程中。我不确定是否有任何习惯用法出现，但如果有特定的子包是插件，我会在包的根目录下创建一个插件目录。

第二个是内部文件夹——这是值得了解的。老实说，这非常简单，它将代码从包中分离出来，因此只允许从包范围调用它——即包中已经存在的代码。

## 不要相信我的话

这种布局可能不适用于所有人。可能有更好的方法。让我知道你的想法——要么在这里让每个人都看到你的想法，要么在推特上通过 [matzhouse](https://twitter.com/matzhouse) 告诉我

如果这是有用的，那么为什么不击中心脏:)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)