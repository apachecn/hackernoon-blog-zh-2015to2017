# 在 Python 中重塑数据

> 原文：<https://medium.com/hackernoon/reshaping-data-in-python-fa27dda2ff77>

我非常喜欢 Jean-Nicholas Hould 关于 Python 中整洁数据的文章，这篇文章是基于 Hadley Wickham 关于整洁数据的文章。从某种意义上说，当你思考这些结论时，它们是直观而明显的。但是数据分析可以是抽象的。为你正在做的事情找到合适的词汇并不容易。

在这篇文章中，我想专门讨论重塑数据的过程，即把数据从一种格式转换成另一种格式。Hould 的文章中会有一些重复，但目标是概述我们经常遇到的各种数据格式，命名它们并命名我们用来转换数据的操作。

# **长格式**

让我们从一个数据整洁的表格开始。我们将称之为*长*格式数据(尽管存在其他命名约定，见下文)。借用 Wickham 的定义，在这种格式中 a)每个变量形成一列，b)每个观测值形成一行，c)每种类型的观测单元形成一个表格。

长格式数据的一个例子是这个由三个人在特定日期的现金余额组成的表。

```
In  : df
Out :
  **   date        person   dollars**
**0**    2000-01-03  Michael  200
**1**    2000-01-03  George   500
**2**    2000-01-03  Lisa     450
**3**    2000-01-04  Michael  180.5
**4**    2000-01-04  George   450
**5**    2000-01-04  Lisa     448
**6**    2000-01-05  Michael  177
**7**    2000-01-05  George   420
**8**    2000-01-05  Lisa     447
**9**    2000-01-06  Michael  150
**10**   2000-01-06  George   300
**11**   2000-01-06  Lisa     344.6
```

该表的格式可以称为:

*   *堆叠格式*，因为单个观察值相互堆叠。
*   *记录格式*，因为每一行都是单个记录，即单次观测。
*   *长格式*，因为这种格式在垂直方向是长的，而在水平方向是宽的。

# 透视数据

如果这张桌子已经很整洁了，我们为什么还要把它重新塑造成另一种格式呢？嗯，我们可能会对按日期直观地比较个人的平衡感兴趣。或者，我们可能对将数据绘制成时间序列感兴趣，其中每条水平线代表一个人。

在 pandas 中，我们可以通过使用 dataframe 的 *pivot* 方法来实现这一点。这就产生了一个“数据透视表”，这是 Excel 用户所熟悉的。

```
In  : df.pivot(index='date', columns='name', values='dollars')
Out :
**name        George  Lisa   Michael**
**date**
**2000-01-03**  500.0   450.0  200.0
**2000-01-04**  450.0   448.0  180.5
**2000-01-05**  420.0   447.0  177.0
**2000-01-06**  300.0   344.6  150.0
```

您指定为*列*参数的任何列都将用于创建新列(每个唯一的条目将形成一个新列)。您指定为*值*参数的列将构成这些列的值，索引将由……您猜对了，您指定为*索引*参数的列组成。

该表的格式可以称为:

*   *宽格式*，因为表格现在变宽了而不是变长了。
*   *未堆叠格式*，因为个人观察(一个人/一个日期)不再彼此堆叠。

有点令人困惑的是，pandas dataframes 还附带了一个 *pivot_table* 方法，这是对 *pivot* 方法的一个概括。每当一个索引/列对有重复值时，就需要使用 *pivot_table* 。让我们看一个例子。

假设我们有乔治、丽莎和迈克尔售出的饼干数量的数据。表中的每一行都代表一个销售机会，这意味着在给定的日期可能有同一销售者的多行。

```
In  : df_cookies
Out :
   **cookies_sold  date        name**
**0**  1             2000–01–01  George
**1**  3             2000–01–01  Michael
**2**  3             2000–01–01  Lisa
**3**  2             2000–01–01  George
**4**  4             2000–01–01  Lisa
```

如果我们试图*旋转*这个数据帧，我们会得到一个 ValueError。

```
In  : df_cookies.pivot(index='date',
                       columns='name',
                       values='cookies_sold')
Out :
...
ValueError: Index contains duplicate entries, cannot reshape
```

与我们之前的 balance 数据框架不同，cookies 数据框架中的值必须聚合才能进行透视，因为 George 和 Lisa 在一天内多次出售 cookies。换句话说，当我们让 pandas 透视我们的数据时，它找不到一个值来返回重复的日期/名称对。然而，Pandas 可以为我们提供每个日期/姓名对的总和、平均值或任何其他聚合值。

```
In  : df_cookies.pivot_table(index='date',
                             columns='name',
                             values='cookies_sold')
Out :
**name        George  Lisa  Michael**
**date**
**2000-01-01**  1.5     3.5   3.0
```

pandas 使用的默认聚合函数是平均值，但是我们可以使用 *aggfunc* 参数轻松地更改它。

```
In  : df_cookies.pivot_table(index='date',
                             columns='name',
                             values='cookies_sold'
                             aggfunc='sum')
Out :
**name        George  Lisa  Michael**
**date**
**2000-01-01**  3       7     3
```

# 堆叠和拆分数据

除了旋转方法之外，pandas 还有堆叠和拆分数据这两个相关的概念。这些主要是为操作多索引数据帧而设计的。

让我们创建一个原始余额数据框架的多索引数据框架。

```
In  : df_multi = df.set_index(['date', 'name'])
In  : df_multi
Out :
                     **dollars**
**date        name**
**2000–01–03  Michael**  200.0
          **  George**   500.0
          **  Lisa**     450.0
**2000–01–04  Michael**  180.5
          **  George**   450.0
          **  Lisa**     448.0
**2000–01–05  Michael**  177.0
          **  George**   420.0
           ** Lisa**     447.0
**2000–01–06  Michael**  150.0
           ** George**   300.0
           ** Lisa**     344.6
```

记住，这是堆叠的数据。每行对应一行。使用 *DataFrame.stack* 和 *DataFrame.unstack* ，我们可以在层次索引和层次列之间切换。在这种情况下，我们有一个层次索引，所以让我们看看 *unstack* 是做什么的。

```
In  : df_multi.unstack()
Out :
            **dollars**
**name**        **George  Lisa   Michael**
**date**   
**2000-01-03**  500.0   450.0  200.0
**2000-01-04**  450.0   448.0  180.5
**2000-01-05**  420.0   447.0  177.0
**2000-01-06**  300.0   344.6  150.0
```

如您所见，该操作将我们的分层索引移动了一级，从而在数据帧中形成了一个新的列级。要回到堆栈格式，我们只需使用*堆栈*。

```
In  : df_multi.unstack().stack()
Out :
                     **dollars**
**date        name**
**2000–01–03  Michael**  200.0
          **  George**   500.0
          **  Lisa**     450.0
**2000–01–04  Michael**  180.5
          **  George**   450.0
           ** Lisa**     448.0
**2000–01–05  Michael**  177.0
           ** George**   420.0
           ** Lisa**     447.0
**2000–01–06  Michael**  150.0
           ** George**   300.0
           ** Lisa**     344.6
```

# 转置数据

Pandas 有一种旋转数据帧的简单方法，即切换列和索引的位置。只需使用 *T* 属性来旋转数据。

```
In  : df = pd.DataFrame({'a': [1, 2], 
                         'b': [3, 4]}, index=[‘one’, ‘two’])
In  : df
Out :
    **a** **b**
**one** 1 3
**two** 2 4In  : df.T
Out :
  **one two**
**a** 1   2
**b** 3   4
```

# 使用 melt 取消数据透视

对我来说，发现熊猫的“融化 T21”功能改变了游戏规则。它基本上允许您按照自己的意愿取消数据透视。换句话说，我们使用 *melt* 将宽数据转换为长数据。

假设我们有一个像这样的宽格式数据帧。

```
 **country 2010  2011  2012**
**0** Canada  55    55    86
**1** Iraq    56    32    22
**2** Italy   3     56    11
```

为了将这些数据转换成长格式，其中每一行代表一个国家/年份对，我们使用了 *melt* (这不是 dataframe 方法，而是从 pandas 的顶级导入)。

```
In  : pd.melt(df, id_vars='country', value_vars=[2010, 2011, 2012])
Out :
 **country  year  value**
**0**  Canada   2010  55
**1**  Iraq     2010  56
**2**  Italy    2010  3
**3**  Canada   2011  55
**4**  Iraq     2011  32
**5**  Italy    2011  56
**6**  Canada   2012  86
**7**  Iraq     2012  22
**8**  Italy    2012  11
```

希望这个概述能让 pandas 重塑数据的工具更加清晰。快乐的数据争论！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！