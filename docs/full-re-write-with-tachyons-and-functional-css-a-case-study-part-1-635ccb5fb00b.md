# 用超光速粒子和功能性 CSS 在 10 天内完全重写:一个案例研究(第一部分)

> 原文：<https://medium.com/hackernoon/full-re-write-with-tachyons-and-functional-css-a-case-study-part-1-635ccb5fb00b>

## 这是一个 4 篇文章系列的开始，带你经历一个使用 [tachyons](http://tachyons.io/) 的网站重构过程，这是一个功能性的 CSS 工具包，保证会引发非常强烈的意见【插入戴夫·鲁伯特的**热门剧**共鸣板效果】。

有问题的网站是 [SocietyOne](https://societyone.com.au) ，这是一家澳大利亚市场贷款公司，我在那里担任前端主管。

欢迎来到第一章！

# 在菜单上

1.  **当初为什么要重写？(你在这里)**
2.  什么是函数式 CSS？
3.  [一步一步的重构过程](/@simonswiss/full-re-write-in-10-days-with-tachyons-and-functional-css-a-case-study-part-2-426c4dda13c2#.d0goawrpx)
4.  [后果:主要经验和建议](/@simonswiss/full-re-write-in-10-days-with-tachyons-and-functional-css-a-case-study-part-4-b565745ca1e5#.qpsjkhrvc)

我们开始吧！

# 第一章:为什么要重写？

市场贷款行业相当新，发展很快。

我们是一个非常小的团队。大约两年前，我们设计和开发了我们的“新”网站，并且一直在一个非常迭代的过程中维护和发展[代码库](https://hackernoon.com/tagged/codebase)。

我们仔细地计划事情。采用 BEM 方法来组织我们的风格。我们一切都很顺利。

我们正在编写漂亮、干净的模块。我们对自己的代码库有很好的理解。在一定程度上。

我爱本。我认为这是组织你的 [CSS](https://hackernoon.com/tagged/css) 的好方法，并且在保持相对低的特异性方面做得很好。BEM(和大多数其他 CSS 架构方法)真正没有帮助的是处理**积累的技术债务**。

我们成长中的公司经历了很多变动。新人，新观点，新策略。这产生了许多小的和大的变更请求。这种现象通常被称为 BAU，是代码库中技术债务的最大驱动因素之一。

# 结束的开始

BAU 请求通常带有一个“我们今天能完成吗”的截止日期，作为一个开发人员，你在某个时候接触到有问题的技术的风险很高。

它可能是样式表底部的一个额外的类，或者是一个模块的复制/粘贴，在这里或那里做一些调整，而不是精心制作的“BEM 修饰符”。

不知不觉中，您正在考虑在 repo 中引入一个 [shame.css](https://csswizardry.com/2013/04/shame-css/) 文件。我敢说，你甚至可以达到一个*内联式黑客*。😱

然后，事情就失控了。

在你漂亮的 CSS 轮子上扔一个巨大的扳手并不需要很多修补程序。随着现实生活中 BAU 请求的继续，你只能眼睁睁地看着你对代码库的掌控越来越差。😓

# 当事情发展到临界点时

在 2016 年圣诞节前的几周，我感觉事情正在达到一个临界点。科技债务正成为一个严重的问题，而且 BAU 没有放缓的迹象。事实上，恰恰相反。

对于这种越来越令人担忧的情况，我失眠了很多。我记得当时已经接近“倦怠悬崖”的边缘了。危险的接近。

# 现在怎么办？

有些事情需要发生。*一些*的事情。

一天，完全出乎意料的，我的同事给我看了他偶然发现的有趣的东西，叫做超光速粒子。

> 回过头来看，这个时刻可能对保持我的心理健康起到了巨大的作用。

我以前从未听说过超光速粒子。我看了一下。我最初的想法是:“嗯，看看 HTML 中的所有这些类。唉”。我向前看了。

很多人——如果不是所有人——都有完全相同的最初想法。

他们**继续前进**。

几天后，我读了亚当·莫尔斯(超光速粒子的创造者)的这篇精彩博文。那篇文章让我产生了很大的共鸣，所以我决定再看一眼那个超光速粒子。

我越想这种函数式 CSS 方法，就越觉得它有意义。我的脑袋开始充满兴奋和希望。

在[下一章](/@simonswiss/full-re-write-in-10-days-with-tachyons-and-functional-css-a-case-study-part-2-dd60256b35b2#.blmmln1rz)中，我会告诉你为什么！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！3