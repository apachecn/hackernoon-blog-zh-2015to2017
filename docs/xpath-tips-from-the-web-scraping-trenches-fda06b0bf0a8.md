# 从网上搜集的 XPath 技巧

> 原文：<https://medium.com/hackernoon/xpath-tips-from-the-web-scraping-trenches-fda06b0bf0a8>

在 web 抓取的上下文中， [XPath](http://en.wikipedia.org/wiki/XPath) 是一个很好的随身工具，因为它允许您比 CSS 选择器更灵活地编写文档位置的规范。如果你正在寻找一个教程，[这里有一个 XPath 教程，有很好的例子](http://www.zvon.org/comp/r/tut-XPath_1.html)。

在本帖中，我们将向您展示一些我们发现在战壕中使用 XPath 时很有价值的技巧，以 [Scrapy 选择器 API](http://doc.scrapy.org/en/latest/topics/selectors.html) 为例。

# 避免使用包含(。//text()，' search text ')。

使用包含(。，'搜索文本')。

原因如下:表达式`.//text()`产生一个文本元素集合——一个*节点集*。当一个节点集被转换成一个字符串时，当它作为参数传递给一个字符串函数如`contains()`或`starts-with()`时，只产生第**个**元素的文本。

```
>>> from scrapy import Selector
>>> sel = Selector(text='<a href="#">Click here to go to the <strong>Next Page</strong></a>')
>>> xp = lambda x: sel.xpath(x).extract() # let's type this only once
>>> xp('//a//text()') # take a peek at the node-set
[u'Click here to go to the ', u'Next Page']
>>> xp('string(//a//text())') # convert it to a string
[u'Click here to go to the ']
```

然而，转换为字符串的*节点*将自身及其所有后代的文本放在一起:

```
>>> xp('//a[1]') # selects the first a node
[u'<a href="#">Click here to go to the <strong>Next Page</strong></a>']
>>> xp('string(//a[1])') # converts it to string
[u'Click here to go to the Next Page']
```

所以，总的来说:

**好:**

```
>>> xp("//a[contains(., 'Next Page')]")
[u'<a href="#">Click here to go to the <strong>Next Page</strong></a>']
```

**坏:**

```
>>> xp("//a[contains(.//text(), 'Next Page')]")
[]
```

**好:**

```
>>> xp("substring-after(//a, 'Next ')")
[u'Page']
```

**不好:**

```
>>> xp("substring-after(//a//text(), 'Next ')")
[u'']
```

您可以阅读 XPath 规范中关于节点和节点集的字符串值的更多详细解释。

# 注意//node[1]和(//node)[1]之间的区别

`//node[1]`选择在其各自父节点下首先出现的所有节点。

`(//node)[1]`选择文档中的所有节点，然后只获取第一个节点。

```
>>> from scrapy import Selector
>>> sel=Selector(text="""
....: <ul class="list">
....: <li>1</li>
....: <li>2</li>
....: <li>3</li>
....: </ul>
....: <ul class="list">
....: <li>4</li>
....: <li>5</li>
....: <li>6</li>
....: </ul>""")
>>> xp = lambda x: sel.xpath(x).extract()
>>> xp("//li[1]") # get all first LI elements under whatever it is its parent
[u'<li>1</li>', u'<li>4</li>']
>>> xp("(//li)[1]") # get the first LI element in the whole document
[u'<li>1</li>']
>>> xp("//ul/li[1]") # get all first LI elements under an UL parent
[u'<li>1</li>', u'<li>4</li>']
>>> xp("(//ul/li)[1]") # get the first LI element under an UL parent in the document
[u'<li>1</li>']
```

还有，

`//a[starts-with(@href, '#')][1]`获取首先出现在各自父节点下的本地锚的集合。

`(//a[starts-with(@href, '#')])[1]`获取文档中的第一个本地锚点。

# 按类别选择时，尽可能具体

如果您想通过 CSS 类选择元素，XPath 的方法相当冗长:

```
*[contains(concat(' ', normalize-space([@class](http://twitter.com/class)), ' '), ' someclass ')]
```

让我们编造一些例子:

```
>>> sel = Selector(text='<p class="content-author">Someone</p><p class="content text-wrap">Some content</p>')
>>> xp = lambda x: sel.xpath(x).extract()
```

**坏:**不起作用，因为属性中有多个类

```
>>> xp("//*[[@class](http://twitter.com/class)='content']")
[]
```

坏:得到的比我们想要的多

```
>>> xp("//*[contains([@class](http://twitter.com/class),'content')]")
[u’<p class=”content-author”>Someone</p>’]
```

**好:**

```
>>> xp("//*[contains(concat(' ', normalize-space([@class](http://twitter.com/class)), ' '), ' content ')]")
[u'<p class="content text-wrap">Some content</p>']
```

很多时候，您可以只使用 CSS 选择器，如果需要，甚至可以将两者结合使用:

**还好:**

```
>>> sel.css(".content”).extract()
[u'<p class="content text-wrap">Some content</p>']
>>> sel.css('.content').xpath('[@class](http://twitter.com/class)').extract()
[u'content text-wrap']
```

在这里阅读[更多关于你能用 Scrapy 的选择器做什么的信息。](http://scrapy.readthedocs.org/en/latest/topics/selectors.html#nesting-selectors)

# 学会使用所有不同的轴

知道如何使用轴是很方便的，你可以[按照教程](http://www.zvon.org/comp/r/tut-XPath_1.html#Pages~List_of_XPaths)中给出的例子快速复习。

特别是，你应该注意到后面的[和](http://www.zvon.org/comp/r/tut-XPath_1.html#Pages~Following_axis)[后面的-兄弟](http://www.zvon.org/comp/r/tut-XPath_1.html#Pages~Following-sibling_axis)不是同一个东西，这是一个常见的混淆来源。同样适用于之前的[和](http://www.zvon.org/comp/r/tut-XPath_1.html#Pages~Preceding_axis)[之前的同级](http://www.zvon.org/comp/r/tut-XPath_1.html#Pages~Preceding-sibling_axis)，以及[的祖先](http://www.zvon.org/comp/r/tut-XPath_1.html#Pages~Ancestor_axis)和[的父](http://www.zvon.org/comp/r/tut-XPath_1.html#Pages~Parent_axis)。

# 获取文本内容的有用技巧

下面是另一个 XPath 技巧，您可以使用它来获得有趣的文本内容:

`1//*[not(self::script or self::style)]/text()[normalize-space(.)]`

这排除了来自`script`和`style`标签的内容，也跳过了只有空白的文本节点。来源:[http://stackoverflow.com/a/19350897/2572383](http://stackoverflow.com/a/19350897/2572383)

# 您还有其他 XPath 技巧吗？

请给我们留下您的建议或问题。

![](img/2ee7e6ccac9e6ea3fafa7f0b4d6d0086.png)

这篇文章由 Zyte(前身为 Scrapinghub)的软件开发人员 Elias Dorneles([@ Elias Dorneles](https://twitter.com/eliasdorneles))撰写。

请用心“推荐”并广泛分享这些技巧。

[**了解一下网页抓取和网页数据能为你做什么**](https://www.zyte.com/data-extraction/) **。**

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)