# 用 Python 模拟 API 的 3 种新方法

> 原文：<https://medium.com/hackernoon/3-new-ways-to-mock-out-apis-in-python-c12510c30d79>

![](img/22e92c20d7b720ee6566e125085357bf.png)

Getting your tests right can be crucial

在我关于主题的上一篇博客[中，我谈到了如何使用`requests_mock`模拟对 REST APIs 的快速而简单的响应。](/@anthonypjshaw/python-requests-deep-dive-a0a5c5c1e093#.f17oeryy0)

*   返回大量响应，如 CSV 报告
*   返回二进制文件，如图像
*   通过回调动态响应

我放在一起的是一个新的(beta)包，用于在单元测试和 Python 请求适配器 API 之间创建一个简单的桥梁。

这个包叫做 requests-staticmock，你可以在 [GitHub](https://github.com/tonybaloney/requests-staticmock) 和 [PyPi](https://pypi.python.org/pypi/requests-staticmock) 找到代码。

它提供了 3 种模拟 API 响应的新方法。

## 模式 1:将一个 API 模仿成一组文件

考虑这个场景，您想要测试您开发的一个类，它

最简单的形式是，您可以使用上下文管理器将静态资产适配器注入到请求会话中。

如果该方法不存在，它将生成 404 响应。

这里有一个更详细的例子来说明如何使用它。

## 模式 2:模仿一个特定的 URL，但是也使用一个真实的实例

在这个例子中，您可能想要模拟一个特定的 API，但是保持对另一个 API 的访问。适配器注入器(和上下文管理器模式)支持会话中的特定 URL，对不匹配模式的 URL 保持默认的 HTTP 行为。

## 模式 3:让测试类生成响应

这种模式允许您创建一个共享类，该共享类为您可能想要测试的所有 URL 提供回调

如果该方法不存在，它将生成 404 响应。

## 摘要

这个库的目标是使用 Python 模拟给定 API 客户机的 HTTP APIs 变得非常简单。

我正在寻找反馈，因为这是一个早期的测试版，请在 GitHub 上提出任何问题或功能想法。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)