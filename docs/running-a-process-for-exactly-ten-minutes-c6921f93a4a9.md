# 运行一个进程正好十分钟

> 原文：<https://medium.com/hackernoon/running-a-process-for-exactly-ten-minutes-c6921f93a4a9>

我想快速了解一下我的[计算机](https://hackernoon.com/tagged/computerr)上的[进程](https://hackernoon.com/tagged/processes)在后台做些什么，所以我转向了 [DTrace](https://en.wikipedia.org/wiki/DTrace) (向 Julia Evans 大喊[让我兴奋起来](https://jvns.ca/blog/2016/07/03/debugging-tools-i-love/)，它可以告诉我一些方便的事情，比如“谁在打开哪些文件？”以及“正在进行什么系统调用？”

首先，我决定运行这个[一行程序](http://www.brendangregg.com/DTrace/dtrace_oneliners.txt)来看看哪些程序进行了最多的系统调用:

```
sudo dtrace -n 'syscall:::entry { @num[execname] = count(); }'
```

在运行了几分钟以上内容(并在后台随意浏览)后，我得到了以下输出(为简洁起见被截断):

现在，我想重新启动到一个相对干净的状态，并运行该命令十分钟，而不必手动停止它。这是我想到的:

Note the ampersand at the end of line 1

与号使 DTrace 作为后台作业运行。运行`jobs -p` 会返回作业的进程 id 列表:

```
bash-3.2$ jobs -p
10038
```

我通过`tail -1`将它管道化，只获取最后一个(以防在同一个 shell 实例中有其他作业正在运行或暂停)。然后因为`kill`不从 stdin 获取输入，我们将 PID 通过管道传递给`xargs,` ，它将把它的 stdin 作为参数传递给`kill`。我们确保用`sudo`运行`kill`，因为 DTrace 进程就是这样开始的。

顺便说一下，因为`jobs`是 bash 内置的，如果你想得到帮助，你需要使用`help jobs`，而不是`man jobs`。

如果有更好的方法，请告诉我！我绝不是狂欢专家。

> 黑客中午是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！