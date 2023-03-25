# 关于电子邮件头像标题的建议

> 原文：<https://medium.com/hackernoon/a-proposal-for-an-email-avatar-header-38a2879d8219>

在一大群人阅读了[谷歌关于如何加强电子邮件安全并使某些最佳实践的缺失对用户可见的文章](https://blog.google/products/gmail/making-email-safer-for-you-posted-by/)后，讨论中出现了头像是电子邮件中非常常见的东西。

对于这个用例，有一些现有的电子邮件标题的特别用法，并且有一些服务允许一个[电子邮件](https://hackernoon.com/tagged/email)地址与一个虚拟角色相关联。我们会先讨论这些。

## 以前/当前的解决方案

*   [X-Face](http://users.cecs.anu.edu.au/~jaa/) —一个旧的新闻组功能，其中编码的 48x48 位图作为消息的一部分包含在 X-Face 报头中。这非常简洁，但是它并没有像“现代网络”所希望的那样向前发展。
*   [Face](http://quimby.gnus.org/circus/face/)—X-Face 的一个新版本，Face 允许对 48x48 的 PNG 进行 base-64 编码，并作为“Face”头附加到邮件中。
*   [X-Image-URL](http://gigliwood.com/weblog//MacOSX/Putting_your_pictur.html) —与 Face 和 X-Face 类似，X-Image-URL 与其余部分分开，作为设置要发送的 URL 而不是图像编码的方式。这被 Mail.app 采用，但后来被移除。
*   Gravatar 是一项免费服务，你可以向它注册你的电子邮件，并在邮件中附上一个头像。这些化身可以具有各种年龄等级，例如，使用 Gravatar 的服务可以强制将明确的化身排除在 PG 服务之外。
*   [谷歌](https://hackernoon.com/tagged/google) + —通过 Gmail 收件箱，以及 Gmail(在较小程度上)使用

这些都很好。但事实就是这样——他们“还好”X-Face、Face 和 X-Image-URL 肯定是更好的选择。它们不依赖于第三方服务，可以在不同的电子邮件之间转换(即使来自同一个发件人)。

我的提议基本上和 X-Image-URL 一样，但是更新得稍微现代一些。

## 我的提议

我的建议最好缩写为“一个签名的 srcset 头”也就是说，您获取一个 srcset 属性的内容，将它放入一个电子邮件头中，并使用 DKIM 对其进行签名。

我同意这个决定，因为最终，srcset 支持一个虚拟角色所需要的一切(主要是:提供多种分辨率的能力)

该电子邮件标题的名称将为“**X-Image-Srcset**”—在适当的时候将更改为“Image-Srcset”。

该标题的要求:

*   实现**不能**处理报头，除非它是 DKIM 签名的
*   实现**不能**处理邮件头，除非邮件通过了 SPF
*   标题**必须**优先于第三方服务(如 Gravatar 和 Google+)
*   实现**必须**支持 PNG
*   实现**应该**支持 APNG、WEBP、AWEBP 和 JPG
*   实现**应该**允许用户禁用动画
*   实现**可能**支持 gif

这些细节中特别值得注意的是对 DKIM 和 SPF 的要求。应特别注意挫败网络钓鱼计划，因为头像图像可能会给电子邮件带来不应有的可信度。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)的机会。
> 
> 要了解更多信息，请阅读我们的“关于”页面、[喜欢/在脸书上给我们发消息](http://bit.ly/HackernoonFB)，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！