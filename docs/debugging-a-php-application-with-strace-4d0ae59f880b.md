# 用 strace 调试 PHP 应用程序

> 原文：<https://medium.com/hackernoon/debugging-a-php-application-with-strace-4d0ae59f880b>

每隔一段时间，你就会遇到一个棘手的 bug，当它出现时，你可能会损失几个小时甚至几天来修复它。

bug 应该是软件开发过程的一部分，我多年来学到的是，当你真正适应你的开发环境(语言、框架、部署类型等)时，花在解决 bug 上的时间是非常有限的，因为你在你的舒适区内工作([技术方面的](https://hackernoon.com/tagged/technology-wise))。

然而，有时有一个 bug 会让你在花了一整个下午寻找根本原因后失去理智。以我的经验来看，我引入的 bug 通常很容易被发现和修复。但是真正的挑战是在其他人的代码中发现错误，比如第三方库、PHP 扩展甚至 PHP 本身。

当奇怪的事情发生时，仪式每次都是一样的:启动 PHPStorm 上的调试器，放置几个断点，然后看看会发生什么。

不过，昨天情况有所不同。

在对代码库做了一些修改并在浏览器上重新加载页面后，我开始直接收到 HTTP 500 错误，奇怪的是 Zen 框架的错误/异常处理程序没有捕捉到这些错误，apache 错误日志也没有包含任何错误。

看起来阿帕奇可能会崩溃，让我们看看。

```
$ sudo less /var/log/syslog
...
Mar  1 15:59:15 redokunvm kernel: [593714.307281] Out of memory: Kill process 30901 (apache2) score 639 or sacrifice child
Mar  1 15:59:15 redokunvm kernel: [593714.308664] Killed process 30901 (apache2) total-vm:2707492kB, anon-rss:1852200kB, file-rss:8024kB
...
```

是的，内存不足错误，即使请求执行的操作根本不占用内存。然而，通过 `ini_set`施加的内存限制应该已经抓住了这一点。除非它发生在 PHP 核心或它的某个扩展上。

在这些情况下，您有几个选择，例如:

*   在每个步骤中添加日志条目，并尝试找出导致内存泄漏的原因
*   打开调试器，添加几个断点，看看会发生什么

查看应用程序日志可以清楚地看到，问题是在将原则实体保存到数据库时产生的。但是调试教条需要相当长的时间，所以我决定尝试另一种方法。

## 遇见斯特拉思

[strace](https://linux.die.net/man/1/strace) 是一个调试工具，分析某个进程对内核的所有系统调用。这对于我来说非常方便，因为我可以看到当出现问题时 PHP 应用程序到底在做什么。

所以让我们启动 strace 并监控所有的 apache 孩子:

`$ ps h --ppid $(cat /var/run/apache2/apache2.pid) | awk '{print"-p " $1}' | xargs sudo strace -o strace.txt`

再次加载网页后，我查看了 strace.txt，发现了这个:

```
open("/mnt/hgfs/php/redokun/vendor/doctrine/dbal/lib/Doctrine/DBAL/Driver/PDOException.php", O_RDONLY) = 25
...
open("/mnt/hgfs/php/redokun/vendor/doctrine/dbal/lib/Doctrine/DBAL/Driver/DriverException.php", O_RDONLY) = 25
...
mremap(0x7f4b8b111000, 188416, 192512, MREMAP_MAYMOVE) = 0x7f4b8b0e2000
mremap(0x7f4b8b0e2000, 192512, 196608, MREMAP_MAYMOVE) = 0x7f4b8b0e2000
mremap(0x7f4b8b0e2000, 196608, 200704, MREMAP_MAYMOVE) = 0x7f4b8b0e2000(mremap was called thousand of times without stopping)
```

很明显，mremap 系统调用分配了越来越多的内存，直到进程被内核终止。

所以查看最近的系统调用，泄漏似乎与之前自动加载的`PDOException`异常有关(还有`DriverException`，它是异常实现的一个接口)。

在 PDOException.php 内部设置了几个断点后，我意识到这个异常确实是由原则准备并抛出的，但是它从未在包含 try/catch 的`Doctrine\DBAL\Statement::execute()`方法中被捕获。

因为这显然与扩展或 PHP 本身有关，所以我尝试禁用扩展。最有可能的原因是 XDebug，我确信它有某种钩子，当一个异常被创建或抛出时就会被触发。

果然，在禁用 XDebug 之后，我终于可以看到异常并修复它了。

经过进一步挖掘，我发现这不是 XDebug 内部的一个错误，而是一个导致 XDebug 崩溃的错误配置。

下面是我不得不在我的应用程序中删除的一行代码，它显然产生了内存泄漏:`ini_set("xdebug.var_display_max_data", "-1");`

使用 strace 调试 PHP 应用程序可能看起来像是一种极端的措施，但是考虑到它的易用性，它绝对可以成为在寻找像这样的令人讨厌的错误时使用的[工具之一。](https://hackernoon.com/tagged/tools)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！