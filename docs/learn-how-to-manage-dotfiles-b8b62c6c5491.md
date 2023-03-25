# 了解如何管理点文件

> 原文：<https://medium.com/hackernoon/learn-how-to-manage-dotfiles-b8b62c6c5491>

![](img/e9c16bd35fc8eff985956e4f9f6c4bc0.png)

我最近买了一台新的笔记本电脑，我的一个同事建议我开始自动化我的设置配置。目标是允许我几乎立即在不同的机器上引导和复制我的配置偏好。待办事项列表中最重要的一项是点文件的[管理](https://hackernoon.com/tagged/management)。今天，我将与您分享如何利用 Unix shell 的强大功能来创建一个脚本，您可以用它来管理不同机器之间的点文件。

[点文件](https://hackernoon.com/tagged/dotfiles)用于定制类 Unix 系统。它们的巧妙之处在于它们可以根据你自己的喜好进行修改。例子包括。gitconfig，。bash_profile 和。仅举几个例子。按照惯例，点文件通常存储在主目录中。你可以在你的 shell 上输入`echo $HOME`来找到你的。在我的机器上，显示的是`/Users/haali`。

![](img/3d94c9c8d6750f9881bcac7446af50e4.png)

入门非常简单。我们需要在一个目录中组织我们的点文件。这是你只需要做一次的事情，因为稍后包含我们所有点文件的目录将被存储在 Git 上。为了组织我们的文件，我们需要将我们的点文件从`$HOME`目录移动/复制到一个新的目录。我们将这个新目录称为`$dir`。

![](img/9a20ab8529deac266388b35d50b30e34.png)

Original copies are to be stored in $dir

在这篇文章中，让我们假设我们想要跟踪`.vimrc`、`.gitconfig`和`.zshrc`，但是可以随意添加任意数量的点文件。在您的终端上，运行以下两条语句:

```
❯ mkdir $HOME/Documents/dotfiles
❯ cp $HOME/{.vimrc, .gitconfig, .zshrc} $HOME/Documents/dotfiles
```

如果您以前从未编写过 bash，第一条语句在给定的路径中创建一个目录 dotfiles，第二条语句将选定的文件复制到 dotfiles 文件夹中。仅供参考，这是您唯一一次为这个特定的自动化输入这些语句。将来，您只需从 git 中检索您的点文件并运行一个脚本。

# 创建脚本

打开您选择的编辑器，创建一个新文件`dotfiles_setup.sh`,在顶部显示下面一行:

```
#!/bin/bash
```

这告诉进程这个脚本应该总是用 bash 运行，而不是另一个 shell。

接下来，让我们将我们关心的点文件存储在一个变量中。我们还需要一个变量来存储包含原始点文件的已创建目录的路径。

```
dotfiles=(".vimrc" ".zshrc" ".gitconfig")
dir="${HOME}/Documents/dotfiles"
```

按照惯例，大多数点文件都是从`$HOME`目录中读取的。将我们的点文件从`$HOME`目录中移走后，我们将需要一种方法来创建一个链接，将每个被移动的文件从`$HOME`连接到`$dir`。幸运的是，这很容易通过使用`ln`命令来完成。link 命令基本上创建了一个指向原始文件的副本。

![](img/0964e672560603bb4683c769c4f3c636.png)

Original copies are stored in $dir. $HOME contains pointer dotfiles

继续该脚本，添加以下几行:

```
for dotfile in "${dotfiles[@]}";do
 ln -sf "${HOME}/${dotfile}" "${dir}"
done
```

该代码段在开头有一个 for 循环，该循环遍历先前定义的`$dotfiles`的每个元素，并使用`ln`命令创建一个指向移动到`$dir`的原始点文件的软链接。[软链接](https://en.wikipedia.org/wiki/Symbolic_link)是一种特殊的文件，它指向文件系统中的另一个文件，并且不包含目标文件中的数据。

就是这样！前往您的终端，让神奇的事情发生:

```
❯ ./dotfiles_setup.sh
```

如果您转向`$HOME`并运行`ls -la $HOME`，您将能够直观地看到所有创建的对`$dir`的引用。在$HOME 目录中，点文件引用将显示一个指向原始副本的箭头。在我的机器上，例如`.gitconfig`被视为:

```
.gitconfig -> /Users/haali/Documents/dotfiles/.gitconfig
```

# 做出改变

对`$dir`所做的任何更改都会立即被参考文件看到，所以你不需要做任何特别的事情。我强烈建议您将 dotfile 与 git 保持同步，这样您就可以在任何机器上复制最新的机器配置。

# 整个剧本

```
#!/bin/bashdotfiles=(".vimrc" ".zshrc" ".gitconfig")dir="${HOME}/Documents/dotfiles"for dotfile in "${dotfiles[@]}";do
 ln -sf "${HOME}/${dotfile}" "${dir}"
done
```

我希望这篇文章能让你开始自动化所有的事情。功劳归功劳，我要感谢我的同事爱德华激励我开始自动化所有的事情。