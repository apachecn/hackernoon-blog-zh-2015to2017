# 时间复杂度 O(log n)实际上是什么意思？

> 原文：<https://medium.com/hackernoon/what-does-the-time-complexity-o-log-n-actually-mean-45f94bb5bfbf>

![](img/f31590e0b507976aad67225432dc0761.png)

事先知道算法的复杂性是一件事，另一件事是知道它背后的原因。

无论你是计算机科学的毕业生还是想有效处理最优化问题的人，如果你想用你的知识解决实际问题，这是你必须了解的。

像 O(1)和 O(n)这样的复杂问题简单明了。O(1)表示直接到达一个元素的操作(如字典或哈希表)，O(n)表示首先我们必须通过检查 n 个元素来搜索它，但是 O(log n)可能意味着什么呢？

你可能第一次听说 O(log n)时间复杂度的地方是二进制搜索算法。因此，一定有某种类型的行为，算法显示，给定的复杂性为 log n，让我们看看它是如何工作的。

由于二分搜索法的最佳情况效率为 O(1)，最差情况(平均情况)效率为 O(log n)，我们将查看最差情况的示例。考虑一个由 16 个元素组成的有序数组。

对于最坏的情况，假设我们想搜索数字 13。

![](img/d4d403691bf3c9a002a409c224b52586.png)

A sorted array of 16 elements

![](img/1340de3e65a834c65ba3564ded7b4b49.png)

Selecting the middle element as pivot (length / 2)

![](img/67bf749fb90ebd527b237ac2eb10f84e.png)

Since 13 is greaterthan pivot, we remove the other half of the array

![](img/4acfae0577e04e06178c6afaa9d7fe75.png)

Repeating the process for finding the middle element for every sub-array

![](img/a71c910cd379fb29041b6ddbdfd4ef98.png)![](img/51e3b0828954ab9076592d00c5cbed88.png)

你可以看到，每次与中项比较后，我们的搜索范围被分成当前范围的一半。

因此，为了到达一组 16 个元素中的一个元素，我们必须将数组划分 4 次，

我们可以这么说，

![](img/2ce3dfdacc23dfbc1fedd89ebe823155.png)

Simplified Formula

类似地，对于 n 个元素，

![](img/405080afbbc2030082ff1100bf8fa6b7.png)

Generalization

![](img/22741f0ef53195c9416257095d995c82.png)

Separating the power for the numerator and denominator

![](img/67c058feecdeddcba6e1d165e21dc707.png)

Multiplying both sides by 2^k

![](img/939d777bb4e26152aed9a04b4f24b4d6.png)

Final result

现在，让我们看看对数的定义，它说

> 一个表示一个固定数(基数)的幂的量，它必须被提高到一定的幂才能得到一个给定的数。

这使得我们的等式变成

![](img/3f203a458e053ba4606616d4aa5f0e97.png)

Logarithmic form

所以对数 n 实际上意味着什么，不是吗？一种其他事物无法代表的行为。

好吧，我希望你已经很清楚了。当在计算机科学领域工作时，了解这些东西总是很有帮助的(也很有趣)。谁知道呢，也许你是团队中能够找到解决问题的最佳方案的人，因为你知道你在处理什么。祝你好运！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)