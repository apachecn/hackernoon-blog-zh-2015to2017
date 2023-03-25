# 用 JS 编程:插入排序

> 原文：<https://medium.com/hackernoon/programming-with-js-insertion-sort-1316df8354f5>

理解数据结构、算法和基本编程概念对于成为一名优秀的开发人员至关重要。现在，这些问题中的大部分都通过使用现代工具和库得到了解决，但是在该领域拥有更深入的知识肯定会拓宽你的软件开发视野。

就个人而言，对我来说很难理解其中的一些概念，因为我在日常工作中没有用到它们。我写这个系列是为了提高我自己对这些话题的理解，并帮助像我一样的其他人。

# 什么是插入排序？

插入排序是另一种流行的排序算法，尽管它不如快速排序或合并排序那样高效。它的工作方式是将数组分成两部分——已排序的和未排序的。我们不知道是否有任何条目已经就位，所以我们将从第一个条目开始排序列表(对单个条目的数组进行排序)。

然后我们开始遍历数组中的其他项。对于每一个，我们必须找到它在排序数组中的适当位置。我们通过找到第一个较小的项目或者直到我们到达排序列表的开始来实现。如果我给你看一个实际情况的例子可能会更好。排序后的数组将被加粗。

```
**5**, 9, 13, 4, 1, 6 // the only sorted part is the first item**5**, **9,** 13, 4, 1, 6 // 9 > 5 so we don't move it**5**, **9, 13**, 4, 1, 6 // 13 > 9 we don't move it**4, 5**, **9, 13**, 1, 6 // compare all to 4, until we reach the head **1, 4, 5**, **9, 13**, 6 // compare all to 1, until we reach the head**1, 4, 5**, **6**, **9, 13 //** first smaller item is 5, place 6 before it
```

# 它是如何工作的？

插入排序的特殊之处在于我们不交换项目。尽管看起来我们是在交换和移动它们，但实际发生的是我们遍历排序后的部分，找到第一个更小的项(或数组的头)并将我们的项放在那里。在那之前我们怎么处理这些东西？因为我们正在遍历它们中的每一个，所以每一个更高的数字实际上都向前移动了一个索引(嗯，不是移动，而是复制)，以便为当前项让路。

最好给出实际算法的代码表示，这样您可以更好地理解发生了什么:

一旦你理解了这个概念，把它转换成代码就一点也不难了。我认为这是最容易实现的算法之一。花一些时间在上面，把它写在一张纸上，抓住这个想法，编码它就不是问题了。

感谢您的阅读，如果您正在寻找更多的计算机科学 JS 资料，我目前正在就它们写一个完整的系列。如果想要一般的 JS 内容，可以看看我的简介。

> **用 JS 编程:**
> 
> **递归**:[https://hacker noon . com/programming-with-js-Recursion-31371 e2bf 808](https://hackernoon.com/programming-with-js-recursion-31371e2bf808)
> 归并排序:[https://medium . com/@ KondovAlexander/programming-with-js-Merge-Sort-deb 677 b 777 c 0](/@KondovAlexander/programming-with-js-merge-sort-deb677b777c0)
> **二分搜索法**:[https://medium . com/@ KondovAlexander/的](/@KondovAlexander/programming-with-js-binary-search-aaf86cef9cb3)