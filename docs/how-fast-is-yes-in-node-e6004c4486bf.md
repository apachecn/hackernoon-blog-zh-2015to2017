# Node 里的 yes 有多快？

> 原文：<https://medium.com/hackernoon/how-fast-is-yes-in-node-e6004c4486bf>

![](img/623a80bf39d3cb50fa3f8a4bf6a541a0.png)

当开发人员厌倦了在终端中对用户协议、安装或确认之类的事情说 Y(是)时，他们会编写诸如 [**yes**](https://en.wikipedia.org/wiki/Yes_(Unix)) 之类的程序。这是一个在 Node 中实现“y\n”的高吞吐量的故事。

## 灵感和方法

这个故事的灵感来自于[“yes in Go 有多快”](https://hackernoon.com/how-fast-is-yes-in-go-1acc57b98815)的一篇文章，这篇文章本身就受到了[这次讨论](https://www.reddit.com/r/unix/comments/6gxduc/how_is_gnu_yes_so_fast/?st=j3v3iw3c&sh=5651ea3c)的启发。在我们的例子中，我们还将依靠 [**pv**](https://linux.die.net/man/1/pv) 来测量吞吐量并记录峰值结果，通过输出字符“y”后跟适当的行尾字符来复制默认行为 **yes** 。我们将使用来自`yes | pv > /dev/null`的 **8.01 GiB/s** 作为基线进行测量。*硬件设置如下。*

# 使用 console.log 和 process.stdout.write

也许首先想到的是使用我们的老朋友`console.log`，它几乎没有让我们失望，并且使用一个无限的 while 循环来保持应用程序运行...此外，由于`console.log`会在每个日志后自动添加一个 EOL，因此可以节省一个步骤:

如您所见，性能非常糟糕，几乎比我们的基线慢了 10，000 倍。如果我们抛开`console`抽象，直接写`stdout`会怎么样？我们现在只需要添加从操作系统导入的 EOL，并测量:

这相对不错，但我们可以做得更好。让我们尝试根据上一篇文章来缓冲我们的输出，我们将利用最小的页面对象大小`4096`，以便在从内存写入底层文件描述符(fd)时获得一些效率:

在短时间内，我们获得了一些吞吐量，然后什么也没有。是一个隐藏的流，每当它可以推送数据时，它就写入文件描述符，否则它会将数据缓冲回内存，直到可以再次推送数据。不幸的是，我们在这里用我们的同步循环淹没了异步流[(有些例外)](https://nodejs.org/api/process.html#process_a_note_on_process_i_o)，特别是有那么多数据被缓冲回到流中，我们可以通过编写一个半异步循环来解决这个问题:

那就好多了！这使我们处于 GiB 领域，但我认为我们可以进一步推动 Node。

*注意* `*Buffer.alloc*` *中的第二个参数用给定的字符串填充整个缓冲区。*

# 使用流和其他优化

我之前提到过`process.stdout`是[一种奇特的河流](https://nodejs.org/api/process.html#process_a_note_on_process_i_o)，那么为什么不通过管道把一条河流输送到那里呢？本质上，流的工作方式是根据可用的吞吐量读写数据，并缓冲其余的数据以备后用。我们可以聪明地使用我们的 **yes.js** 程序，只发送需要的内容。

我们将从创建一个[可读流](https://nodejs.org/api/stream.html#stream_readable_streams)开始，该流将推送一个大小相等的缓冲区，因为总是适合整个字符集并且不丢失一个换行符是很重要的:

我们的流中的`_read`方法将被调用，其大小取决于要读取或在我们的例子中产生的数据量。然后，我们可以创建这个流的一个新实例，并将其通过管道传输到`process.stdout`，这将产生 **3.25GiB/s** 的吞吐量，这一点都不差！但是我们正在分配一个全新的缓冲区，并在每次调用`_read`方法时填充它。让我们缓存我们的缓冲区并创建一个最终版本:

最终峰值吞吐量**为 4.67 GiB/s** ，比我们开始时快了 5 万倍，比我们基线的一半还要好。在同一台机器上，Golang 能够使用 Mat Evans 的 source 实现 **7.75 GiB/s** 。

作为旁注，`console.log`不是一个[高性能日志解决方案](https://www.npmjs.com/package/lorg),如果你完全依赖它，但是它有一大堆简单日志记录器所没有的内置特性。

## 运行测量的设置是什么？

为了测量峰值吞吐量，所有测试都在同一台远程机器上单独运行，该机器装有运行 Ubuntu 17.0 的英特尔至强 E3 1240 v3(3.4 GHz)和 32GB DDR 3 内存。*在应用端 Node.js 版本 8.3.0 和 7.10.0 和 Golang 1.7.4 被使用。*

## 最后的想法

你可以从 npm 安装并运行[最终代码。有许多展示语言/框架能力的好方法，但是**是的**开始成为我个人最喜欢的方法之一，它展示了原始的单线程吞吐量，而没有牺牲简单性。](https://www.npmjs.com/package/fast-yes)