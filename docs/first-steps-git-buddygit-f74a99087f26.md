# Git 版本控制的第一步

> 原文：<https://medium.com/hackernoon/first-steps-git-buddygit-f74a99087f26>

![](img/9ae86fa663884628965b3a762dcea8a1.png)

下面的指南涵盖了 [Git](https://hackernoon.com/tagged/git) 的基础知识:在您的系统上安装 Git，初始化存储库，提交文件，添加 remote，以及与其他用户在分支上协作。这是一些非常简单的东西，在 Git 成为你一生的朋友之前，无论好坏，都要随身携带。

**开始之前的一个注意事项**理解 Git 工作原理的最佳方式是从终端运行 Git 命令。如果您是 Windows 用户，请确保在添加 git 包时运行 git bash。

# 装置

首先，您需要为您的操作系统选择并安装软件包:

*   [麦克·OS X](https://code.google.com/archive/p/git-osx-installer/downloads)
*   [视窗](https://git-for-windows.github.io/)
*   [Linux](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)

# 创建存储库

一旦安装了 Git，转到包含您的项目的文件夹并运行

```
git init
```

在这个目录中初始化 Git。该命令将在您的文件夹中添加一个`/.git`目录，该目录将存储有关回购的数据。

# 将文件添加到存储库中

现在我们需要告诉 Git 将文件置于[版本控制](https://hackernoon.com/tagged/version-control)下，这样我们就可以提交它们了。为此，请运行:

```
git add .
```

使用“git add ”,您可以定义要在下次提交时添加哪些文件:

*   `git add filename`将添加一个特定的文件
*   `git add .`将从目录中添加所有文件。

添加文件后，您可以将它们提交到存储库:

```
git commit -m ‘Added all files to the repository’
```

其中`-m`代表提交消息。总是写一些东西来描述你在提交中的活动，以避免以后浏览历史时出现混乱。

要查看添加和/或修改了哪些文件，请使用…

# 想了解更多？[点击这里查看全文](https://buddy.works/guides/first-steps-with-git?utm_source=medium&utm_medium=post&utm_campaign=first-steps-with-git-version-control&utm_content=link)。

![](img/237fecb458d889482966108f09dae68f.png)[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)