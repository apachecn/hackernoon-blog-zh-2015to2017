# 如何使用 Django 的缓存模板加载器显著提高 Django 站点的速度

> 原文：<https://medium.com/hackernoon/are-you-using-djangos-cached-template-loader-9e1145a7d610>

我在剖析一个 Django 1.8 站点时，注意到了一些奇怪的事情。

函数`django.template.engine.get_template`被调用了 50 次，总共花费了 229 毫秒，几乎是页面加载时间的一半。

该网站使用大量的[模板](https://hackernoon.com/tagged/template)标签来呈现页面上的不同元素，比如广告位置和相关的故事框。

Django 的模板系统会在每次渲染时读取并编译一个模板。这种情况发生得很快，但是当你在每个页面上呈现多个模板时，它会累加起来，就像我的情况一样。

在四处寻找解决方案后，我发现了`django.template.loaders.cached.Loader`，当它被启用时，将编译好的模板存储在内存中，减少了检索模板的时间。

一旦在网站上启用了缓存模板加载器，`django.template.engine.get_template`的累积时间就下降到了 1 毫秒，减少了大约 200 毫秒的页面加载时间

Django 1.2 中引入了缓存模板加载器，但默认情况下并未启用，这很容易被忽略。

在启用缓存模板加载器之前，您需要确保所有自定义的[模板标签都是线程安全的](https://docs.djangoproject.com/en/1.11/howto/custom-template-tags/#template-tag-thread-safety)。

从[开始，当`DEBUG`设置为假时，Django](https://hackernoon.com/tagged/django) 1.11 缓存模板加载器默认启用。

如果你还在使用老版本的 Django，并且使用大量的模板标签，启用缓存模板加载器，它可以极大地提高你的站点速度。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！