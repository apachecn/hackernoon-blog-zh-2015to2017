# 唯一一个不带 jar 的 Java 工具

> 原文：<https://medium.com/hackernoon/the-one-and-only-java-tool-shipped-without-a-jar-21594ebe366f>

![](img/178e8e3aae8bc4a6ae374ba4f4e7dbbd.png)

[nudge4j 发货时没有罐子](https://github.com/lorenzoongithub/nudge4j)

> 复制/粘贴这段代码，我们将为您的 Java 程序添加一些非常棒的功能。

你有没有碰到过做类似声明的网站？

不，我不这么认为。

我们，Java 开发人员，不复制/粘贴代码。

当然除了 [nudge4j](https://github.com/lorenzoongithub/nudge4j) 之外。但这是个特例。

相反，web 开发人员对此非常习惯。

Web 开发人员知道，他们可以通过将一个微小的脚本复制/粘贴到他们的 HTML 页面中来集成第三方的功能。

一些例子是:

*   disqus ( [链接](https://disqus.com/))
*   谷歌分析([链接](https://www.google.com/analytics/))
*   addthis ( [链接](http://www.addthis.com/))

这是一项伟大的工程:通过添加几行代码，你最终会得到带有对话(disqus)、网络分析(google analytics)和社交按钮(addthis)的页面。

不仅如此:禁用/启用添加的功能就像注释/取消注释代码片段一样简单。

在 Java 中，我们不这样做。

而且，老实说，显然有一些很好的理由。

*   Java 应用程序不能在沙盒浏览器中运行
*   Java 是一种强类型语言，当 jar 在编译时已知时，API 定制更容易
*   有这么多成熟的 Java 构建工具(Ant、Maven、Graddle ),第三方库创建者忙于与它们集成，甚至没有时间考虑不同的方法
*   Java 很啰嗦。如果你不能假设你的代码片段会在哪里，情况会变得更糟。例如，您应该完全限定您打算使用的每个类，或者将代码包装在匿名类/函数中，以避免变量冲突。

> 复制/粘贴这段代码，我们将为您的 Java 程序添加一些非常棒的功能。

我们真的可以试试吗？

我们如何才能创建**最小的 Java 代码片段**来为 JVM 添加任何功能？

下面是我试图写的最小的，最少的，幽闭恐怖的无空间片段。

> 复制/粘贴这一小段代码(*少于 200 个字符)，并从 http://localhost:5050/* 上的浏览器与您的 JVM 对话

```
try{new javax.script.ScriptEngineManager().getEngineByName("js").eval("load('http://nudge4j.appspot.com/n/n4j.js')");}catch(Exception e){throw new RuntimeException(e);}
```

*少于 200 个字符的代码*是

*   懒洋洋地将所有东西包装在一个 try-catch-exception 中
*   创建 JavaScript 引擎
*   使用 [*加载*函数](https://wiki.openjdk.java.net/display/Nashorn/Nashorn+extensions)运行具有所需功能的外部 JavaScript

坦率地说，我不认为你能得到比那更短的。

但我希望被证明是错的。如果你能想到更短的方法，请告诉我。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)