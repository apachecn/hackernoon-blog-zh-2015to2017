# Doc-Diff (Python 库)

> 原文：<https://medium.com/hackernoon/doc-diff-python-library-9db21ebc9022>

Python 通常是需要在工作中应用数据分析的开发人员或者主要是数据科学家/数据工程师的选择，他们的任务更多的是从数据中获得洞察力。

Python 最大的资产之一是其丰富的库。最近，我正在研究非常流行的数据挖掘算法(即:FP-Growth 和 Custom A-Priori)。有一种情况，我想得到这些算法产生的结果的综合分析报告。

作为[数据科学](https://hackernoon.com/tagged/data-science)工作的支持库引入[**“doc-dff—生成两个文件之间的差异数据”**](https://github.com/Renien/doc-diff)

![](img/8a077fa42e0f3ba14f9e55985a97e9b0.png)

[doc-diff](https://github.com/Renien/doc-diff)

doc-diff 支持以下特性:

> *生成以下比较报告*

*   common _ in _ doc 1-and-doc 2-% Y-% m-% d . CSV
*   common _ key _ with _ diff _ values-% Y-% m-% d . CSV
*   exclusive_in_doc1-%Y-%m-%d.csv
*   exclusive_in_doc2-%Y-%m-%d.csv

> *比较两个文件并返回以下内容* ***'dicts(prodCode，recommendation)'***

*   common _ in _ doc 1 _ and _ doc 2 _ list = dicts()
*   common _ key _ with _ diff _ values _ list = dicts()
*   exclusive_in_doc1_list = dicts()
*   exclusive_in_doc2_list = dicts()

**安装**

> $ pip 安装文档-差异

**实施**

```
**from** doc_diff **import** Diff
**from** doc_diff **import** gen_comp_report**if** __name__ == **'__main__'**:

    *# Data file location* a_priori_csv_location = **"./data/a-priori.csv"** pfp_csv_location = **"./data/pfp.csv"** *# Process a-priori.csv data file* a_priori_diff = Diff(a_priori_csv_location)
    a_priori_diff.process_file() *# Process pfp.csv data file* pfp_diff = Diff(pfp_csv_location)
    pfp_diff.process_file()
    gen_comp_report(a_priori_diff, pfp_diff)
```

我期待为数据科学/数据工程工作开源我所有的支持库。请在下面的评论中告诉我你对‘doc-diff’的看法，并分享你的想法。如果你想分享任何新的特性/问题，[可以在 GitHub 库](https://github.com/Renien/doc-diff/issues)中随意打开一个问题。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)