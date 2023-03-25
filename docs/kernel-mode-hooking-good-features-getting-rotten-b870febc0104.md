# 内核模式挂钩，好功能烂了

> 原文：<https://medium.com/hackernoon/kernel-mode-hooking-good-features-getting-rotten-b870febc0104>

这篇文章应该以教育为唯一目的来阅读。不得伤害或损坏任何用户或机器。伦理第一。过会儿开心。

现代 x86 操作系统，使用**保护模式**来执行指令。在保护模式下，有 4 个不同的权限级别，从 0 到 3。它们也被称为**环 0** — **环 3** ，以表示它们之间的分离程度。

最高级别(最低特权)是常规应用程序运行的**用户区** (ring3)。最低层(最高特权)是[内核](https://hackernoon.com/tagged/kernel)模式(ring0)，操作系统的内核或核心在这里运行(基本上是拥有硬件的代码)。

每当应用程序需要调用内核时，它使用一个**中断**来告诉内核执行哪个系统调用。这个中断在 Linux x86–32 中是指令 *int $0x80* ，在 Linux x86–64 中是指令 *syscall* 。

当 CPU 接受中断时，它从 ring3 切换到 ring0，并调用 *system_call* 。从这种有规律且看似无害的行为中，可以做出美好又恐怖的东西，如果你明白我的意思的话。[这里的](https://github.com/worldofpiggy/C-code/tree/master/kernel_mode_hooking)是允许[挂钩](https://hackernoon.com/tagged/hook)内核系统调用的代码，嗯，释放你的想象力。黑客快乐！

你喜欢阅读吗？请用一笔小额捐款来支持我们。我们真的很感激！

*原载于 2015 年 9 月 11 日 worldofpiggy.com**的* [*。*](http://worldofpiggy.com/2015/09/11/kernel-mode-hooking-good-features-getting-rotten/)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！