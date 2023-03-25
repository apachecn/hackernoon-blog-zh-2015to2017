# 理解 Git(第 1 部分)——像我五岁时一样解释它

> 原文：<https://medium.com/hackernoon/understanding-git-fcffd87c15a3>

![](img/a288c3317185d1ff793dbd21f1585170.png)

A confusing mess of branches. Photo by [Brandon Green](https://unsplash.com/photos/GEyXGTY2e9w?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText).

> → [了解 Git(第一部分)——解释得像我五个](https://hackernoon.com/understanding-git-fcffd87c15a3)
> [了解 Git(第二部分)——贡献给一个团队](https://hackernoon.com/understanding-git-2-81feb12b8b26)
> 了解 Git(第三部分)——解决冲突(敬请期待！)

Git 是一个强大的工具，但是它有着迷惑新手的名声。大多数人只是被扔在深水区，并期望游泳，这并没有什么帮助。

有了正确的知识，任何人都可以掌握 git。一旦你开始理解它，术语会变得更有意义，你会(最终)学会爱上它。保持坚强🙏

# 为什么是另一个向导？

已经有很多“git 教程”了，但是大多数只是告诉你复制/粘贴特定的东西来完成一次性的任务。任何有键盘的人都可以复制/粘贴；要真正理解 git 是如何工作的，它能为您做什么，您需要稍微深入一点的理解。

他们也倾向于向你抛出词汇，而不解释这些词汇的真正含义。

本指南旨在让您对经常使用的基本短语和命令有一个可行的理解。一次就学会如此强大而复杂的工具是不可能的，所以我鼓励你慢慢来，享受这个旅程。

# git 是什么？

首先， [GitHub](https://hackernoon.com/tagged/github) 是*不是* git。可以理解，许多人混淆了这两者。 [GitHub](https://github.com/) 是一个托管*使用* git 的项目的网站。

Git 是一种 [**版本控制**](https://hackernoon.com/tagged/version-control) **系统** (VCS)的类型，使得跟踪文件的变更变得更加容易。例如，当你编辑一个文件时，git 可以帮助你准确地确定修改了什么*，谁*修改了什么*，以及*为什么*。*

它有助于协调多人在一个项目中的工作，并通过保存“检查点”来跟踪进度。你可以在写文章的时候使用它，或者跟踪对艺术作品和设计文件的修改。

Git 不是唯一的版本控制系统，但它是目前最流行的。许多软件开发人员每天都在使用 git，了解如何使用它可以大大提升你的简历。

在复杂的项目中，多个人可能同时对同一个文件进行更改，很容易进入一种奇怪的状态。任何处理过“合并冲突”和那些令人困惑的符号的人都可以证明这一点。

如果您开始理解 git 是如何工作的，您就会明白为什么会发生冲突，以及如何轻松地从这些情况中恢复过来。

# 如何获得 git

Git 默认安装在许多系统上。如果您还没有它:

*   你可以在这里下载 git 的[命令行界面(CLI)](https://git-scm.com/downloads)。我向初学者和高级用户都推荐了这个。
*   如果你更喜欢使用花哨的图形用户界面(GUI)，试试 [GitHub 桌面](https://desktop.github.com/)(用于 Windows 和 Mac)。这将更容易使用，但会使它更难真正看到发生了什么。

下面的例子都假设您正在使用 CLI。

ℹ️:你可以通过在终端输入**来使用命令行界面。如果你不熟悉终端，没关系——先试试[这个](https://askubuntu.com/q/38162/259547)(或者搜索谷歌寻求帮助)。**

# 常见命令

下面是一系列基本命令，并描述了它们各自的功能。本部分旨在进行互动。在阅读时，在继续阅读之前，请随意尝试这些命令。最后还会有一个“真实生活”的例子，有一个命令列表，你可以一次全部尝试。

注意事项:

*   任何**行话**在第一次被描述时都会以粗体突出显示。请随意在官方的 [git 词汇表](https://git-scm.com/docs/gitglossary)或[参考指南](https://git-scm.com/docs)中查找这些术语，了解更多详细信息。
*   这是一个简化的指南。它试图尽可能准确，同时避免一些混乱的细节。结尾有更多深入的链接。
*   ✨在描述中会有一些新的见解，所以一定要继续读下去。

以下是一些最常见的命令，大致按照您会遇到的顺序排列:

> 从头开始创建您自己的**库**(在您计算机上的任何现有文件夹中):

```
git init
```

这将在当前文件夹中创建一个隐藏的`.git`文件夹——这是 git 存储所有内部跟踪数据的“仓库”(或 **repo** )。您对原始文件夹中的任何文件所做的任何更改现在都可以被跟踪。

✨:原来的文件夹现在被称为您的**工作目录**，而不是跟踪您的更改的存储库(`.git`文件夹)。你*在工作目录中工作*。简单！

> **克隆**现有的回购:

```
git clone [https://github.com/cooperka/emoji-commit-messages.git](https://github.com/cooperka/emoji-commit-messages)
```

这将从互联网(GitHub)下载一个`.git`存储库到您的计算机，并将回购的最新**快照**(所有文件)提取到您的工作目录。默认情况下，所有文件都将保存在与回购文件同名的文件夹中(在本例中为`emoji-commit-messages`)。

✨你在这里指定的 URL 叫做**远程源**(文件被*源*盟友下载的地方)。这个术语以后会用到。

> 查看项目的当前**状态**:

```
git status
```

这将打印一些基本信息，例如最近修改了哪些文件。

你应该随时检查你的状态。Git 将根据当前发生的情况打印附加信息来帮助您。

> 创建新的**分公司**名称:

```
git branch <new-branch-name>
```

你可以认为这就像创建一个本地“检查点”(技术上称为**引用**)并给它一个名字。类似于在文本编辑器中做**文件>另存为…**；创建的新分支是对您的回购当前状态的引用。您很快就会看到，这个分支名称可以用在其他各种命令中。

与分支类似，更常见的是，在进行过程中，以**提交**的形式保存每个检查点(稍后见下面的`git commit`)。

提交是一种特殊类型的检查点，称为 [**修订**](https://git-scm.com/docs/gitrevisions) 。这个名字将是由数字和字母组成的随机组合**，比如`e093542`。这个散列可以像分支名称一样用于各种其他命令。**

> ✨:这确实是 git 的核心功能:保存检查点(修订版)并与其他人共享。一切都围绕着这个概念。

如果你曾经创建过某个东西的检查点，只要你的`.git`文件夹完好无损，你就可以在以后恢复它。太神奇了。如果您有兴趣了解更多信息，请参见`[git reflog](https://git-scm.com/docs/git-reflog)`。

分支是一个巨大而复杂的话题。我很快会写更多关于它的内容；现在，如果你愿意，你可以在这里阅读更多的。

> **查看**某个特定的分支:

```
git checkout <existing-branch-name>
```

您可以将此视为从现有检查点“恢复”。您的所有文件都将被重置为它们在该特定分支上所处的状态。

⚠️请记住，你的工作目录中的任何变化都会被保留下来。如果你对避免头痛的简单方法感兴趣，请参见。

😎你可以使用`-b`标志作为快捷方式*创建*一个新的分支，然后*检出*所有这一切都在一个步骤中完成。这很常见:

```
git checkout -b <new-branch-name>
```

> 查看检查点之间的**差异**:

```
git diff <branch-name> <other-branch-name>
```

编辑完一些文件后，你可以简单地输入`git diff`来查看你所做的修改列表。这是在提交工作前仔细检查的好方法。

对于每一组更改，您将看到文件*以前是*的样子(前缀为`-`，颜色为红色)，然后是现在的样子(前缀为`+`，颜色为绿色)。

有关该命令的更多高级示例，请参见下面的内容。

> 准备实施你的改变:

```
git add <files>
```

编辑一些文件后，该命令会将您所做的任何更改标记为“暂存”(或“准备提交”)。

⚠️:如果你接着进行更多的更改，这些新的更改将会*而不是*自动进行，即使你已经像以前一样更改了相同的文件。这有助于准确控制您提交的内容，但也是新人困惑的主要来源。

如果你不确定，只需再次输入`git status`看看发生了什么。您将看到“要提交的更改:”后面跟着绿色的文件名。在它下面，您会看到“未提交的更改:”后面跟着红色的文件名。这些还没有上演。

😎作为一种快捷方式，您可以像使用任何其他终端命令一样使用通配符。例如:

```
git add README.md app/*.txt
```

这将添加文件`README.md`，以及文件夹`app`中以`.txt`结尾的所有文件。通常你只需输入`git add --all`就可以添加所有改变的内容。

> 提交你的阶段性变化:

```
git commit
```

这将打开您的默认命令行文本编辑器，并要求您键入一条**提交消息**。一旦您保存并退出，您的提交将保存在本地。

提交消息对于帮助其他人理解更改了什么以及您为什么更改它非常重要。这里有一个简短的指南[解释如何编写有用的提交消息。](http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)

😎您可以使用`-m`标志作为编写消息的快捷方式。例如:

```
git commit -m “Add a new feature”
```

> **推送**贵分公司上传到其他地方:

```
git push origin <branch-name>
```

这将把你的分支上传到名为`**origin**`的**远程**(记住，那是在`clone`期间最初定义的 URL)。

在成功的`push`之后，你的队友将能够`pull`你的分支来查看你的提交(见下面的`git pull`)。

😎作为一种快捷方式，您可以键入单词`HEAD`而不是`branch-name`来自动使用您当前所在的分支。`HEAD`总是指你最近的检查点，也就是你当前分支上最近的提交。

✨如前所述，git 中的所有东西都可以看作是一个检查点。下面是您现在知道的检查点类型的列表(同样，这些在技术上称为“引用”和“修订”):

*   `HEAD`
*   `<branch-name>`，例如`master`
*   `<commit-hash>`，如`e093542d01d11c917c316bfaffd6c4e5633aba58`(或简称`e093542`)

还有:

*   `<tag-name>`，例如`v1.0.0`
*   `stash`

最后，像`^`、`~`和`@{}`这样的特殊字符可以用来修改引用。它们非常有用；点击了解更多[。](https://git-scm.com/book/en/v2/Git-Tools-Revision-Selection)

> **获取**关于回购的最新信息:

```
git fetch
```

这将从`origin`下载关于回购的最新信息(比如存储在 GitHub 上的所有不同分支)。

它不会改变任何本地文件，只是更新存储在`.git`文件夹中的跟踪数据。

> **合并**来自别人的修改:

```
git merge <other-branch-name>
```

这将获取`other-branch-name`分支上存在的所有提交，并将它们集成到您自己的当前分支中。

⚠️这使用本地存储的任何分支数据，所以确保你已经运行了`git fetch`来下载最新的信息。

例如，如果其他人向`origin`的`master`分支添加了一些提交，您可以执行以下操作来下载他们的更改并更新您自己的本地`master`分支:

```
git checkout master      # Make sure you're on the right branch.
git fetch                # Download any new info from origin.
git merge origin/master  # Merge the 'origin/master' branch
                           into your current branch.
```

✨这里的名字`origin/master`字面意思是你电脑上的`origin/master`检查点。Git 使用这种符号来区分位于不同地方的同名分支(例如`master`)(例如您自己的分支与`origin`的分支)。

😎作为一种快捷方式，您可以使用 **pull** 命令在一个步骤中获取和合并。这比上面的手动合并更常见:

```
git pull origin master
```

这里我们把`origin`和`master`这两个词分开(没有上面那样的斜线)。我们不想在自己的计算机上使用`origin/master`检查点，因为它是离线存储的，可能已经过时了。相反，我们希望直接从名为`origin`的远程端点的`master`分支获取数据。保持警惕；区别很重要！

要更深入地了解合并是如何工作的，以及**冲突**是如何解决的(附有有趣的图片和图表)，请参见官方[合并文档](https://git-scm.com/docs/git-merge)。这也将在本系列的第 3 部分中广泛讨论。

# 现实生活中的例子

这里有一系列的命令，可以假设在开发一个真正的功能时执行。看看你是否能弄清楚他们每个人会做什么，然后自己尝试并检查。

```
git clone https://github.com/cooperka/emoji-commit-messages.git
cd emoji-commit-messages
git status
git checkout -b my-new-feature
echo “This is a cool new file” > my-file.txt
git status
git add --all
git status
git diff HEAD
git commit -m “Add my-file.txt”
git status
git log
git push origin HEAD
git checkout master
git pull
```

这些命令中的大多数都有额外的参数，您可以通过传递这些参数来定制它们，并且为了简洁起见，这些命令都有简化版本，所有这些都使一切变得更加有趣。通常有不止一种方法来完成给定的任务。

# 现在怎么办？

现在你知道了关于 git 的一切！

…开玩笑，这只是冰山一角。在你声称自己是专家之前，你还需要了解很多东西，但是现在，你至少有了能够与团队合作和理解行话的基础。

一旦你理解了这些基本命令，你可以并且应该继续从 [git 的网站](https://git-scm.com/book/en/v2)或者这个[更高级的指南](http://think-like-a-git.net/)中学习更多。

如果你发现这很有用，请点击**来帮助你👏想按多少次**就按多少次，这样其他人也能找到。

谢谢，敬请期待[第二部](https://hackernoon.com/understanding-git-2-81feb12b8b26)。如果有你感兴趣的特定话题，请在评论中告诉我。