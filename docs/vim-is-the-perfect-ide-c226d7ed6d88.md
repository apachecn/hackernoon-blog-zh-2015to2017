# Vim 是完美的 IDE

> 原文：<https://medium.com/hackernoon/vim-is-the-perfect-ide-c226d7ed6d88>

这些年来，我在许多代码编辑器、**ide**和**工具**之间来回切换；但似乎不知何故，我总是最终回到 VIM，不仅仅是为了编程——猜猜我用哪个 markdown 编辑器写这篇文章。

我已经尝试了 Atom、SublimeText、TextMate、Eclipse、Visual Studio 和大多数 Jetbrains 产品，我不断地**调整和寻找更好的设置**，然而 Vim 对我来说总是感觉像家一样；我现在很少使用 ide——除了那些杂乱和复杂的项目，ide 可以做很多繁重的工作(是的，Magento，我说的就是你。)

但除此之外，Vim 是我默认的 Ruby、Elixir、Python、PHP IDE，也是我用来写草稿和书籍的主要工具。

## 它看起来像什么

VIM Awesomeness!

# **设置**

那么这个神奇的工具是如何工作的呢？都是开箱即用的对吗？对吗？嗯，不，就像生活中所有有价值的事情一样，需要付出一点努力来实现我想要的 Vim 设置。幸运的是，它远不是定制的，而是插件的正确组合。

你可以在相应的 [Github 库](https://github.com/amacgregor/dot-files)中找到我当前的 Vim 配置和 dot 文件，你可以随意使用它。

我们对 vimrc 文件特别感兴趣，让我们来分解一下:

此设置中的每个插件分为以下几类:

## 效用

这是一个杂七杂八的类别，由用于增强或改变核心 vim 行为的插件组成；最有用最重要的是:

*   **Nerdtree** :它以目录树的形式在屏幕左侧让您轻松访问文件系统，并为文件系统操作(创建、删除、移动文件和目录)提供快捷方式
*   **标签栏**:当前文件的快速标签浏览器，如果你正在使用任何种类的**ctag**比如 exverant-tags，这是必须的。
*   **FZF** :模糊查找器，另一个查找文件和命令的便利工具。
*   **Neocomplete** : Vim 自动完成。

## 通用编程支持

这些插件直接属于编程范畴，在我目前安装的所有或大部分**编程语言**中使用:

*   **Exuberant-Ctags** :标签是类、函数、抽象类型等等的命名定义；添加对 Vim 的支持会给您带来一些“神奇的”IDE 代码导航功能。
*   Syntastic:Vim 的语法检查插件，如果你熟悉 Jetbrains 和类似 ide 上代码检查的工作方式，syn tastic 会让你感觉很舒服。
*   **Vim-autoclose** :自动关闭一个字符，这个字符可能/应该有一个匹配的结束对应字符，比如()" "[] {}等等。

## 降价/写作

正如我提到的，Vim 是我起草新帖子的编辑，无论是书籍、博客还是随机的愤怒信件。从这一部分来看，只有语言工具值得一提，因为它直接从 Vim 内部提供了一个很好的语法检查器。

## Erlang/Elixir/PHP/Elm 支持

当谈到**个人语言支持**时，除了我尝试过(有点失败)将插件保持在最低限度并只专注于**基本语言支持**之外，没有什么值得强调的。

到目前为止，就插件而言，elixir 正在赢得这场战斗，因为我已经添加了额外的功能，如在 Vim 中运行测试和生成内容的能力，我还没有决定是否为它保留所有的插件。

## Git 支持

恐怕我很少使用的标准 git 支持，我发现自己直接回到了 **shell** 并在 Vim 之外做 **git 工作流**，所以我乐于接受建议并听取其他人在设置方面的使用。

## 主题和界面

好吧，这是一个大问题，但主要是因为我总是忘记删除不用的主题和配色方案，让我们突出重要的:

*   **vimar line**:Vim 的精益和平均状态/表格线；它看起来也很酷。
*   Vim-Devicons :因为不仅 atom 拥有侧边栏上所有漂亮的图标，如果你使用 nerdtree 的话，强烈建议你这么做。

配置文件的其余部分要么是插件配置，要么是我用来保证质量的个人密钥重映射；我已经尽了最大努力来记录和分割每个部分，所以应该很容易理解每个设置在做什么。

对于任何开始使用 Vim 的人来说，我希望特别注意以下几点:

```
" Disable arrow movement, resize splits instead.
if get(g:, 'elite_mode')
    nnoremap <Up>    :resize +2<CR>
    nnoremap <Down>  :resize -2<CR>
    nnoremap <Left>  :vertical resize +2<CR>
    nnoremap <Right> :vertical resize -2<CR>
endif
```

因为没有最快的方法来强迫一个人使用主行进行导航。

# 结束语

我希望这篇文章对你们中的一些人有所帮助，我现在的设置还远远没有完成，从这个意义上说，这是一个我几乎每天都在玩的玩具。话虽如此，但为了回到帖子最初的前提；它确实是一个非常强大的玩具，特别是当与其他工具如 **Tmux** 结合使用时，这里是我的**仙丹**IDE 由 **Vim** 和 **Tmux** 驱动的一个偷偷的高峰:

VIM + TMUX = Super IDE

如果你想知道更多关于我的设置，或者想分享你的设置，请在下面留言。

*本文原帖* [*在我自己的网站*](http://coderoncode.com/tools/2017/04/16/vim-the-perfect-ide.html) *。*

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！