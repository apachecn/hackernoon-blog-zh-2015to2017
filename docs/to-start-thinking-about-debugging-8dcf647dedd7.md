# 开始考虑调试

> 原文：<https://medium.com/hackernoon/to-start-thinking-about-debugging-8dcf647dedd7>

[调试](https://hackernoon.com/tagged/debugging)任何一段[代码](https://hackernoon.com/tagged/code)都是一个程序员的核心技能。有时候你在调试自己的代码，有时候是别人写的。

# 在您的机器上调试

当您在计算机上编写代码，并且想要调试某个问题时，请尝试以下所有方法:

## 检查日志

不要跳过日志，直接得出结论“它不起作用”。日志在 80%的情况下会包含关键信息。确保从实际错误行之前的几行开始检查。

## 谷歌错误

这一点非常重要，90%的情况下，其他人已经面临这个问题，并且可能已经解决了它。尝试谷歌主要错误行，并读出至少 4-5 个不同的页面。有时，一个问题可能是由多种原因造成的，而第一个解决方案可能不是您正在寻找的解决方案。

执行此操作时，请确保从错误行中删除不可搜索的文本。这可以包括:

*   时间戳
*   目录路径
*   名称

如果没有结果出现，试着通过删除不重要的单词来减少你正在搜索的错误行。

## 检查堆栈跟踪

如果以上两种尝试都失败了，那么尝试找到堆栈跟踪并到达错误发生的实际代码行。这将帮助您缩小可能的错误问题的数量。

## 现场调试

如果这不是一个致命的错误(或者即使它是)，意味着错误发生在运行时，只是结果是错误的，应用程序不会崩溃，接下来你应该尝试在调试模式下运行应用程序。这可能不是所有语言都能做到的，但是大多数语言都有调试的方法。调试功能还取决于您使用的 IDE。

将断点放在发生错误的行内和周围，并检查参数和自变量(所有相关变量)。如果你发现哪个变量有错误的变量，就追踪到它的起源。

这需要你遵循这个循环

*   在一行处断开
*   找到变量
*   找出这个变量是如何形成的(params/args)
*   找到这个函数的调用者，转到步骤 1

# 不在您的计算机上调试

很多时候，问题并不出现在您的本地开发环境中，而只是出现在沙箱或生产机器中。要调试此类问题

## 远程调试

一些语言提供了远程调试这一很好的特性，您可以连接到远程机器上的进程。如果可能的话，试着这样做。我只在 Java 上试过，效果很好。您将需要使用一些特殊的命令行参数来启动 jvm，因此只有在您控制了主机的情况下这才是可能的。

## 印刷

如果以上方法都失败了，这是最后的选择之一。您仍然需要缩小到可能发生错误的行，并在它们之前添加 print 语句。添加尽可能多的 print 语句，以便跟踪单个线程的执行。

## 最后一个选择:试一试

这很少会起作用，尽管它可能会给你很好的提示，告诉你应该在哪里查找错误。这种方法包括随机改变初始变量，并尝试观察故障模式。如果没有别的，这将有助于缩小到根本原因。

# 还是没有解决

这是给你同事打电话或打电话的好时机。确保你包含了从前面所有步骤中获得的尽可能多的信息。

调试是一项后天习得的技能，你会越来越擅长。