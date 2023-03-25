# 分析器如何工作

> 原文：<https://medium.com/hackernoon/how-profilers-work-1826163e1bbc>

分析器是用来检查程序如何使用各种计算资源的工具。它们测量各种程序特性(内存使用、函数调用的持续时间等)。)并汇总收集的数据以创建有助于程序优化的报告。

在这篇文章中，我们将构建两个简单的评测器来测量函数调用时间(以下简称为“评测器”)，但是解决这个问题的方式略有不同。这篇文章来自我在做 [***vprof***](https://github.com/nvdv/vprof) 的时候做的各种笔记，希望对你有用。

Python(更准确地说是 CPython)用于本文中的所有代码，因为它为编写开箱即用的分析器提供了很好的工具，但是这里讨论的概念可以很容易地应用于其他主流的高级编程语言。

我们将考虑两种类型的分析器——确定性的和统计性的。
确定性分析器记录所有函数调用和返回的准确时间，然后处理收集的数据，以确定函数调用的持续时间和其他重要参数。虽然这种方法具有更高的准确性，但它会增加大量的开销(尤其是对于小函数)，并且程序在分析时运行速度较慢。
统计分析器定期对正在运行的程序进行快照，并推断出时间花在了哪里。这种方法比确定性分析器的开销小，程序几乎以全速运行，但准确性通常较低，因为收集的分析仅提供了花费时间的统计近似值。

下面是我们将使用分析器检查的代码

该代码生成 N 个素数，其中位数之和为偶数。虽然代码在算法上远非完美，但它足够简单，很好地满足了我们的需求。

## 确定性分析器

也许测量代码执行时间最简单的方法是使用 Python 内置的[***time . time()***](https://docs.python.org/3/library/time.html#time.time)函数

如果我们运行它，我们会得到这样的东西

```
Total time is 2.436735 s
```

看起来不错。现在我们知道时间召唤到****get _ prime*s**takes。但是有一个问题—我们想看看***sum _ of _ digits***和 ***is_prime*** 调用所用的时间，以便确定瓶颈。*

*让我们改进我们的方法*

*这里我们使用众所周知的 Python 技术——剖析装饰者。*

*对于那些不熟悉 Python decorator 的人来说——***@ decorator***只是***func = decorator(func)***的一个快捷方式，它允许修改 Python 函数([这里](http://simeonfranklin.com/blog/2012/jul/1/python-decorators-in-12-steps/)你可以找到关于 Python decorator 的很好的介绍)。*

*然后我们需要用新的*概要文件*装饰器包装我们的函数*

*再次运行我们的代码*

*输出是*

```
*Function is_prime run time is 0.000003 s
Function sum_of_digits run time is 0.000002 s
Function is_prime run time is 0.000001 s
Function sum_of_digits run time is 0.000001 s
Function is_prime run time is 0.000001 s
Function is_prime run time is 0.000001 s
…
(many lines here)
…
Total time is 2.567238 s*
```

*不完全是预期的那样。我们可以看到单个调用的时间，但是，由于函数调用太多，我们看不到全局。
此外，您可能会注意到现在总时间更长了，因为我们引入了分析器开销——每个包装的函数调用 ***time.time()*** 两次，执行减法、字符串格式化并将结果字符串打印到 ***stdout*** 。*

*我们的剖析装饰器还可以改进。如果我们想查看概览，就没有必要打印每个函数调用的调用时间。让我们存储函数名，即每次函数调用后的调用者名和累计调用时间，而不是打印调用次数。*

*我们在[***default dict***](https://docs.python.org/3/library/collections.html#collections.defaultdict)中用累计函数调用时间替换了 ***print*** 调用，允许我们存储每个调用方的累计调用时间。*

*具有改进装饰器的包装功能*

*并再次运行代码*

```
*Total time is 2.637101 s*
```

*统计数据存储在 ***统计数据*** 变量中。让我们打印它们*

```
*{‘get_n_primes’: defaultdict(<type ‘float’>, {‘<module>’: 2.7848520278930664}),
 ‘is_prime’: defaultdict(<type ‘float’>, {‘get_n_primes’: 2.7565882205963135}),
 ‘sum_of_digits’: defaultdict(<type ‘float’>, {‘get_n_primes’:
0.005716800689697266})}*
```

*即使从原始统计数据中，我们也可以看到调用**花费了大部分时间。***

*最后，让我们总结收集的统计数据并打印漂亮的报告*

```
*Total time: 2.637101 s
Function: is_prime, caller: get_n_primes, function run time: 2.611894 s, percentage: 99.044156 %
Function: get_n_primes, caller: <module>, function run time: 2.637097 s, percentage: 99.999855 %
Function: sum_of_digits, caller: get_n_primes, function run time: 0.005741 s, percentage: 0.217715 %*
```

*现在我们看到大部分时间都花在了 ***is_prime*** 函数上，如果我们想提高 ***get_primes*** 性能，***is***_***prime***是优化的首选。*

*我们确定性分析器的准确性由以下因素决定:
1。定时器精度。没有任何测量能比基础计时器(约 1 毫秒)更精确。
2。时间的测量不是即时的，并且在用户代码退出的时间和测量时间然后再次执行用户代码的时间之间存在延迟。不进行大量计算的小函数特别容易出现这种错误，因为它会累积。这种误差可以通过校准剖面仪来减小。*

*注意:Python 提供了***sys . settrace()***hook 用于编写剖析器，所以我推荐在所有严肃的用例中使用。*

## *统计剖析器*

*如上所述，统计分析器通过定期对调用堆栈进行采样来运行。为了做到这一点，我们需要定义一个函数来进行调用堆栈采样，然后安排这个函数定期执行。Python [信号](https://docs.python.org/3/library/signal.html)模块将帮助我们做到这一点。*

*下面是这种方法的代码*

****sample_stack*** 回调是信号处理程序。它存储当前调用堆栈名称以及调用堆栈被采样的次数，并安排一个发出新信号的计时器。我们使用 ITIMER_PROF，因为它在进程执行时和系统代表进程执行时都递减底层计时器。ITIMER_PROF 在到期时发出 SIGPROF。其他代码只是设置简单的统计分析器，并测量总运行时间。*

*在我们运行代码并打印出 ***stats*** 变量后，我们会看到这个*

```
*{‘<module>-get_primes’: 312,
 ‘<module>-get_primes-is_prime’: 876,
 ‘<module>-get_primes-sum_of_digits’: 1}*
```

*我们可以看到， **get_primes** 已经捕获了 312 个样本，***is _ prime***—876 个样本，*位数之和—只有 1 个样本。由于大多数样本属于 ***is_prime*** 函数，我们可以假设那里是花费时间最多的地方。我们将使用样本总数(312 + 876 + 1 = 1189)进行以下计算。**

**让我们总结一下统计概要分析器统计信息，并将其与确定性概要分析器统计信息进行比较**

```
**Total time: 2.493037 s
Total samples: 1189
Function: <module>-get_primes-sum_of_digits, sample count: 1, percentage: 0.084104 %
Function: <module>-get_primes-is_prime, sample count: 876, percentage: 73.675357 %
Function: <module>-get_primes, sample count: 1189, percentage: 100.000000 %**
```

**首先，我们可以观察到总运行时间比确定性分析器少，这是因为统计分析器的开销更少。此外， ***is_prime*** 的百分比(~73.7%)低于使用确定性探查器时的百分比(~99%)。这是因为统计分析器产生的数据是近似值。此外，如果我们多次运行 profiler，我们可以得到非常不同的结果(70-99%)。这意味着在做出优化决策时，应考虑较低的精度。然而，这些结果可以帮助我们找到代码中的瓶颈。**

## **摘要**

**我们已经为 Python 构建了简单的确定性和统计性的分析器。分析器测量函数调用时间，但是列出的方法也可以用于测量其他程序特性，比如内存使用。这里讨论的概念也适用于其他主流高级编程语言。**

**我希望这篇文章能帮助你更好地理解分析结果，如果有必要的话，还可以构建你自己的分析器。**

**这个帖子的代码是[这里](https://gist.github.com/nvdv/a210dc5d1a3f797cdb4be806ba507f1a)和[这里](https://gist.github.com/nvdv/e7aa07272d928273bca811895e21f5ba)。**

> **[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。**
> 
> **要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)**
> 
> **如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！**