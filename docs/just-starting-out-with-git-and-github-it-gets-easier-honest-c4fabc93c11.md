# 刚开始用 Git 和 GitHub？越来越简单了，真的！

> 原文：<https://medium.com/hackernoon/just-starting-out-with-git-and-github-it-gets-easier-honest-c4fabc93c11>

![](img/387ae57bc90facd4035b04e50e446193.png)

Image credit: [https://gist.github.com/pierreinglebert/11209464](https://gist.github.com/pierreinglebert/11209464)

A familiar sentiment for anyone starting out with Git

毫无疑问你听说过 Git 或者 GitHub 的源码控制，但是**什么是源码控制？**

这是对源代码控制的基本概述，以及在团队环境中使用它或自己使用它的优势，如果你在谷歌上搜索[源代码控制](https://en.wikipedia.org/wiki/Version_control)，你可以看到维基百科是如何定义源代码控制的，如下所示:

> 版本控制(也称为版本控制、源代码控制或(源代码)代码管理(SCM))是对同一信息单元的多个版本的管理

酷故事 br0！换句话说:源代码控制允许任何规模的团队在不同的位置进行分布式工作，同时避免源代码变更中的冲突。

真的！？谢谢你澄清了这件事！

让我们这样来看…

> 用最简单的术语来说，它就像一个**【另存为】**。您想要新文件，但不删除旧文件中的更改。这是一种日常情况，除了在一个软件项目中有很多变化的可能性。

## 概念

这里有一些关于版本控制的基本概念，我将很快介绍一下，这些术语在许多 SCM 系统中使用，有些与 Git 和 GitHub 相关，有些与其他系统相关。

**Repository/repo:** *存储文件的数据库。*

**分支:** *创建一个单独的回购副本，以便在您的计算机上使用。*

**恢复/回滚:** *返回到先前保存的代码库/回购版本。*

**Push:** *Push 是回购上的一个访问级别，如果您没有 Push 访问权限，则需要提出 pull 请求。*

**Pull:** *如果您没有推送权限，您可以发出一个 Pull 请求，通知回购所有者您希望将您的更改合并到他们的代码中。*

如果您刚刚开始，那么您最常用的命令可能是:

```
git add .
git commit -m 'some informative message'
git push origin master
```

在我学习如何将代码备份到 GitHub 的早期，这些命令对我很有帮助。

> Git 和 GitHub 是两个不同的东西，Git 是一个自由和开源的版本控制系统，而 GitHub 使用 Git 技术在 GitHub.com 的服务器上托管你的库。

当我第一次开始使用 Git 和 GitHub 时，我对它们有点困惑，我以前对 VCS 很熟悉，但那是以微软的 Visual SourceSafe 和 Team Foundation Server 的形式出现的，在那里你有一个很好的 GUI 来指导你完成签入和签出过程，对于那些过程是:

*   **签出:**在你的机器上复制你想要修改的库，一旦你完成了你的修改，签入你的修改。
*   **签入:**将您的更改添加回存储库，并附带一条消息，详细说明您所做的更改。

使用 Git 就没那么花哨了，都是通过命令行，但与 VSS 和 TFS 几乎一样。

*   克隆存储库:在您的机器上制作存储库的副本。
*   做出改变。
*   一旦进行了更改，就用伴随的提交消息将它们添加回去。
*   将更改推回到 Github 上的 repo。

GitHub 上的文档对于你想要实现的任何事情都是很棒的，在这篇文章中，我引用了一些文档。

不过，这可能会有点让人不知所措，尤其是如果您不同步，也就是说，忘记在远程版本上进行更改，然后在将更改加载到本地版本之前尝试签入您的更改。如果事情出了一点差错，我一点也不自信，但是我已经开发了一个[“稍后提交通常是完美的”](https://gist.github.com/SethRobertson/1540906/68feeabfe906ec1eb893e4fa45f402795ed6e62c#commit)方法，所以如果你真的打破了某些东西，你不会损失太多宝贵的时间去找出哪里出了差错。

如果你看一看你从 GitHub 克隆的一个 repo，你会看到有一个名为`.git`的文件，这就像一个你在你的机器上所做的所有更改的小数据库，它包含了连接到 GitHub 并对主 repo[或任何你正在将更改推送到的分支]进行更改所需的所有信息。

我做了一个备忘单[要点](https://gist.github.com/spences10/5c492e197e95158809a83650ff97fc3a#useful-git-commands)，我每次接近 Git 时都会用到它，现在它已经变成了我日常使用的其他备忘单的一个[回购](https://github.com/spences10/cheat-sheets)。

在这本书里，我讲述了这些情况:

*   在你的机器上创建一个你想添加到 GitHub 的新项目？看这里的。
*   从别人的 GitHub 克隆一个 repo，推送到我的 GitHub 上的一个 repo，或者[“我没有做叉，现在怎么办！”](https://github.com/spences10/cheat-sheets/blob/master/git.md#cloning-a-repo-from-someone-elses-github-and-pushing-it-to-a-repo-on-my-github)
*   您有一个[分支，您需要在进行任何更改之前更新](https://github.com/spences10/cheat-sheets/blob/master/git.md#sync-a-remote-fork-on-your-machine)。

不同的情况下工作流程会有所不同，对我来说，作为一名 noob 开发人员，我试图记录我遇到的任何 Git，以便我可以在需要的时候引用它，但不记得我在周三 21:45 做了什么，只是为了将代码签入 GitHub。

有许多与 Git 集成的 GUI 可以与 GitHub 一起使用，官方的 GitHub one 非常好，但我很快就陷入了 GUI 无法让我摆脱的情况，所以我一直坚持使用终端，虽然有 VSCode，它有一个我每天使用的漂亮的 Git UI，但我仍然使用终端做一些事情:

```
git status
git checkout <branch-name>
git [push] tags
```

所有这些都很方便，可能有一些扩展可以帮助解决这些问题，但是现在我很乐意通过命令行来完成。

你有什么在 Git 中使用的技巧或诀窍想要分享吗？请随时留下您的评论，或者通过拉取请求添加到[回购中。](https://github.com/spences10/cheat-sheets/compare)

如果这些对你有所帮助，请随意喜欢这篇文章，并在社交媒体上分享。

非常感谢。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)