# 关于好的区域分解

> 原文：<https://medium.com/hackernoon/on-good-domain-decomposition-385ee8ce5a3>

## 或者如何制作可以工作的物品

我用一些特征来检查我的软件。大概最主要的是[复用抽象原则](http://www.codemanship.co.uk/parlezuml/blog/?postid=934)。它说只有当有多个实现时，你的抽象才是好的。换句话说，它认为抽象应该是抽象的。

马克·西曼有一篇关于解决方案空间方法的很好的[文章](http://blog.ploeh.dk/2010/12/03/Towardsbetterabstractions/)，可以带来更好的抽象。

但是 OOP 比你开始写代码要早得多。当你和你的领域专家交谈时，它就开始了。

## 了解你的问题

柏拉图在他的[菲德鲁斯](https://en.wikipedia.org/wiki/Phaedrus_(dialogue))对话中[说](http://www.perseus.tufts.edu/hopper/text?doc=Perseus%3Atext%3A1999.01.0174%3Atext%3DPhaedrus%3Apage%3D265)我们的设计应该是领域驱动的，应该从问题空间开始。他对这一原则描述如下:

> 感知并把分散的细节集中在一个想法中的原则，这样一个人就可以通过定义来明确他想要解释的特定事物；就像现在，在谈到爱*【我想这里指的是厄洛斯】*，**我们说了他是什么，并定义了它，无论是好是坏**。当然，通过这种方式，话语获得了清晰和一致性。

这与帕纳斯将系统分解成模块的方法产生了共鸣。

## 分解你的问题空间

当我弄清楚我的领域后，我开始分解。[柏拉图描述了](http://www.perseus.tufts.edu/hopper/text?doc=Perseus%3Atext%3A1999.01.0174%3Atext%3DPhaedrus%3Asection%3D265e)分解必须具备的主要特征:

> [原则]再次将事物分类，在自然连接处，而不是试图打破任何部分，就像一个糟糕的雕刻师那样。

事实证明，过程化编程对于良好的分解来说并不是一个好的指南。数据和程序都不适合这个目的。但是什么会呢？好吧，既然我们在这里做 OOP，答案是行为就不足为奇了。上面提到的自然关节是物体的边界。
顺便说一下，同样的原理也适用于[识别服务边界](/@wrong.about/how-to-define-service-boundaries-251c4fc0f205)。

## 实际后果

第一个是右分解产生的元素是可组合的，而抽象的数量相对较少。这不是一个科学的说法，更像是经验性的。看化学:元素周期表不到 130 个元素。[欧几里德几何](https://en.wikipedia.org/wiki/Euclidean_geometry) : 13 条公理。代数:10 个数和一堆运算。重用抽象原则自然得到满足。

可组合性意味着实体共享相同的接口。意味着我们身边有很多可替代的东西。我不在乎哪辆公共汽车带我去办公室，我只在乎它的号码。我不在乎穿什么牛仔裤——尺码可能是唯一的标准。或者我用什么椅子，桌子，笔。

## 隐喻引导你寻找物品

[大卫·韦斯特](http://davewest.us)在他的书[对象思维](https://www.amazon.com/Object-Thinking-Developer-Reference-David/dp/0735619654)中描述了一些有用的隐喻来描述权利分解。它们都适用于问题空间，即领域。

第一个是乐高。乐高里有很多零件，但是零件的种类却少得多。所有的部分都是高度可组合的。
第二个是人称隐喻。我在读这本书之前用过它，所以它对我来说是最自然的。更具体地说，我想象的不仅仅是一个人，而是一个成年人，他能做决定，分辨是非，决定如何做他或她应该做的任何事情。
第三个是剧场。每个演员都知道如何扮演自己的角色，而且每个演员都不介意替换一些同事。我喜欢的对象协作的比喻是蚂蚁。科学证明(甚至有一本书里有链接！)认为绝对没有蚂蚁的编排。他们都是平等的。物体也应该如此。

## 例子

我试图在设计 [DateTime](https://github.com/wrong-about-everything/ooDateTime) 库时应用这些原则。
我基本上需要两件事:从任意格式创建 datetime 对象，并以任意格式输出它们。因此有两个接口— [格式化日期时间](https://github.com/wrong-about-everything/ooDateTime/blob/master/src/ISO8601DateTime.php)和[格式化日期时间](https://github.com/wrong-about-everything/ooDateTime/blob/master/src/FormattedDateTime.php)。此外，我需要一个界面与间隔工作。三个目录对应于这些接口。
我还需要一种相对指定约会时间的可能性。所以我有一个时间轴目录与三个类:过去，现在和未来。
最后，我想要一个比较能力，我用两个类来表达它: [Min](https://github.com/wrong-about-everything/ooDateTime/blob/master/src/comparison/Min.php) 和 [Max](https://github.com/wrong-about-everything/ooDateTime/blob/master/src/comparison/Max.php) 。可以实现与它们的任何比较。

下面是一个可以用此库表示的内容示例:

```
// outputs true

var_dump(
    (new Max(
        new Future(
            new Past(
                new FromMilliseconds(
                    (new ToMilliseconds(
                        new FromISO8601('2017-08-18T15:08:13+04:00')
                    ))
                        ->value()
                ),
                new ISO8601Interval('P1Y2M21DT24H56M26S')
            ),
            new ISO8601Interval('PT23H')
        ),
        new Now()
    ))
        ->equalsTo(new Now())
);
```

对象是可组合的，类比接口多得多。所以我的目标实现了。

这个库目前还只是一个实验，它缺乏一些功能，并且已经存在一些问题(我不喜欢`equalsTo()`方法中的复制粘贴)，但是整个分解过程似乎很好。

## 总结一下

所以从问题空间开始——你的领域。使用行为作为分解标准。记住这些比喻。让可组合性成为你的目标。