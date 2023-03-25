# Laravel 和连续输送的第一步

> 原文：<https://medium.com/hackernoon/first-steps-with-laravel-and-continuous-delivery-591acf200c9a>

![](img/2bc46a0642191fbf05ac2ef1da6e5455.png)

看来 [Laravel](https://hackernoon.com/tagged/laravel) 是现今使用最频繁的 [PHP](https://hackernoon.com/tagged/php) 框架之一。这些数字证实了这一点:Twitter 上有超过 65000 名粉丝，GitHub 上有 30000 名明星和 10000 名粉丝。通过这篇文章，你将学会如何为一个由 Laravel 构建的应用程序创建一个连续的交付管道。

# 基于 Laravel 的应用程序

本文的第一部分将指导您创建一个演示项目(一个简单的计算器将作为例子)的过程，单元和特性测试将被创建。

## 安装 Laravel

首先，确保在本地安装以下内容:

1.  PHP v. 5.6.3 或最新的 PHP 版本来自:[http://php.net/manual/en/install.php](http://php.net/manual/en/install.php)
2.  PHP 作曲:[https://getcomposer.org/doc/00-intro.md](https://getcomposer.org/doc/00-intro.md#using-the-installer)

完成所有工作后，我们可以开始安装 Laravel:

```
$ composer global require "laravel/installer"
```

最后需要做的是将 Laravel 添加到`PATH`环境变量中，这样我们的应用程序就可以用`laravel`命令执行了:

```
$ export PATH="$PATH:$HOME/.config/composer/vendor/bin"
```

# 想了解更多？[此处跟随全文](https://buddy.works/guides/first-steps-with-laravel-and-continuous-delivery)。

![](img/237fecb458d889482966108f09dae68f.png)[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)