# 使用 Git，效率提高 125%

> 原文：<https://medium.com/hackernoon/be-125-more-efficient-with-git-60556a1ce971>

我使用 Vim 已经 12 年了，在使用它的第一天，你会学到一件重要的事情，那就是你可以非常高效地输入命令来完成你想要做的事情。

我几乎一整天都在控制台前编写代码，在某个时候，你会意识到不仅你可以弄干你的代码，你的日常控制台命令也可以。时不时地，我真的喜欢停下来一会儿，想想我输入的所有命令，看看我是否可以更快地执行它们。

几个月前，我的一个朋友也试图解决他的日常难题，他为 tmux 创建了一个名为 [tmux-fingers](https://github.com/Morantron/tmux-fingers) 的插件，但是我决定走另一条路。与此同时，我发现了 [FZF](https://github.com/junegunn/fzf) ，我认为这将是一个有趣的[工具](https://hackernoon.com/tagged/tool)来玩。

这个命令行模糊查找器做一件事，而且做得很好，就像 [UNIX](https://hackernoon.com/tagged/unix) 中最古老的命令一样。它接受一个输入，您可以对其进行过滤，以在交互式 shell 中选择您想要的内容。所以你可以这样做:

[](https://asciinema.org/a/22tnlseff7yuw62fc73nfelns) [## 查找+ fzf

asciinema.org](https://asciinema.org/a/22tnlseff7yuw62fc73nfelns) 

我建议你尝试一下，并开始思考你可以用它做什么。回顾所有可用的 FZF 选项，你会发现像多选(-m)宝石。

那么，你如何洗牌 FZF 和 Git 得到这个职位的主题回答？想想你每天都在做的常见 git 用例，你会想到这样的事情:

*   在分支之间切换
*   查看一些具体的提交
*   做互动 rebases
*   在提交中编辑更改的文件

我们将举一个例子:

```
 git log --pretty=oneline | fzf
```

在执行下面的命令之后，您意识到您可以以交互的方式在您的存储库中搜索提交。如果您按下*回车*，FZF 的输出将是您所选择的行。现在，尝试执行以下命令:

```
git show $(git log --pretty=oneline | fzf | cut -d=' ' -f1)
```

你将得到你的第一个交互式 git 展示。万岁！:p

# 用例

现在，深呼吸，仔细阅读我在日常工作流程中发现的适用于您的用例。我所有的模糊 git 命令都以 *pam* 开头。

## 在分支之间切换

这是一个经典的用例。你想换一个你最近去过的分行。我执行这个:

```
git pam
```

在内部，该命令的作用是:

```
git checkout $(git recent | fzf)
```

[](http://asciinema.org/a/15lmglaf6ojo44jo0fbv4tt6c) [## git pam

### git 分支+ fzf

asciinema.org](http://asciinema.org/a/15lmglaf6ojo44jo0fbv4tt6c) 

## 编辑未提交的文件

这是另一个经典用例。你想编辑一些你正在处理的未提交文件。我执行这个:

```
git pamvim
```

在内部，该命令的作用是:

```
vim $(git status -s | fzf -m)
```

该命令支持多选！

[](https://asciinema.org/a/7fbnjgoh41bg2ek9un5rxgn3x) [## git pamvim

### fzf + git + vim

asciinema.org](https://asciinema.org/a/7fbnjgoh41bg2ek9un5rxgn3x) 

## 交互式 rebase

这越来越有趣了…

您想要从某个特定的提交开始一个交互式的 rebase，并且您想要选择它。我执行这个:

```
git pamrebase
```

在内部，该命令的作用是:

```
git rebase -i $(git log --pretty=oneline | fzf)^
```

[](http://asciinema.org/a/b8xhvbr2rpw0cnjr2ga0dh0kh) [## git pamrebase

### git rebase + fzf

asciinema.org](http://asciinema.org/a/b8xhvbr2rpw0cnjr2ga0dh0kh) 

## 在提交中编辑文件

另一个有用的命令。您想要编辑在某个特定提交中被更改的一些文件，并且您想要选择它。我执行这个:

```
git pamvimlog
```

注意:检查我的[点文件](https://github.com/fcsonline/dotfiles/blob/master/git/gitconfig)以了解它是如何工作的。

该命令支持多选！

[](https://asciinema.org/a/c0y4ywx3wj5i8wh2epf08a51a) [## git pamvimlog

### vim + git + fzf

asciinema.org](https://asciinema.org/a/c0y4ywx3wj5i8wh2epf08a51a) 

## 交互式修正

我的最后一个例子。您希望在某个特定的提交中进行修复，并且您希望选择它。我准备了一些修改，然后执行了这个:

```
git pamfix
```

[](https://asciinema.org/a/699sr7ume88wemx9p57n0ipr4) [## git pamfix

### 修复+ fzf

asciinema.org](https://asciinema.org/a/699sr7ume88wemx9p57n0ipr4) 

# 结论

如果你对更有用的命令感兴趣，可以看看我的[点文件](https://github.com/fcsonline/dotfiles/blob/master/git/gitconfig)。

黑客快乐！

感谢阅读，【http://github.com/fcsonline/】[费兰](https://medium.com/u/7f68810f90fc?source=post_page-----60556a1ce971--------------------------------)

> 像这样？请给它一些♥，并分享它！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请阅读我们的“关于”页面、[在脸书](http://bit.ly/HackernoonFB)上给我们点赞/发消息，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！