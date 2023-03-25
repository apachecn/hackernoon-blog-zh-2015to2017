# 还有多一个人的地方吗？

> 原文：<https://medium.com/hackernoon/is-there-room-for-one-more-3462fc7e4a3e>

## 关于谷歌 Android 新房间图书馆的一些想法。

在上一次 Google I/O 中，Google 宣布了新的[架构组件](https://developer.android.com/topic/libraries/architecture/index.html)。其中之一是房间。Room 是 Android 开发者的另一个 ORM 解决方案。结合谷歌发布的一些其他元素(如 LiveData、ViewModel 和 Lifecycle Listeners)，基本应用的流程应该简单得多。

的确——谷歌在这方面做了一些不错的工作。像显示 DB 中的项目并更新它们这样简单的任务是一件痛苦的事情，而且您必须一遍又一遍地编写大量代码。[现在你可以使用那些开箱即用的组件](/proandroiddev/clean-easy-new-how-to-architect-your-app-part-1-e439668a523d)。这将为开发人员节省时间:您将编写更少的代码，并且您将有更少的 bug。

当检查这些组件时，我感觉它们中的大多数都带来了新的东西。但是关于[室](https://developer.android.com/topic/libraries/architecture/room.html)——是否需要另一个 ORM 图书馆？

![](img/8dfc38d80cdfdee1a55b3a2ad767fb4f.png)

Photo credit: [http://7-themes.com/7021413-android-wallpaper-3d.html](http://7-themes.com/7021413-android-wallpaper-3d.html)

# ORM？

让我们从头开始:对于不熟悉的人，ORM 代表“对象关系映射”。这是一种更好的方式，让开发人员用持久对象来代替表或行。

这意味着如果我想在数据库中保存我所有的用户数据，我需要使用用户对象，ORM 层将为我处理所有的持久性问题。好玩。

# 当前解决方案

这个概念并不新鲜，开发人员使用这种技术已经很长时间了，Android 开发人员自然也需要这种东西。

很快我们就有了几个第三库来处理它，只要看一下 [AndroidArsenal page](https://android-arsenal.com/tag/69) 就知道我们进入了拥挤的空间。最受欢迎的大概是 [ORMLite](http://ormlite.com/) 、 [GreenDAO](http://greenrobot.org/greendao/) 和 [DbFlow](https://github.com/Raizlabs/DBFlow) 。

# 创建模型

在 Java 中定义对象和表之间的映射有两种主要方法:一些 DSL +代码生成器(像 GreenDAO 正在使用的)，或者使用注释(像 ORMLite、DBFlow 和大多数库)。虽然我不太喜欢注释，但我必须说后者是一个更容易的解决方案。

这就是 Android 团队选择采用这种方法的原因，简单的数据模型如下所示:

```
@Entity
public class User **{**
    @PrimaryKey
    private int uid**;**

    @ColumnInfo**(**name **=** "first_name"**)**
    private String firstName**;**

    @ColumnInfo**(**name **=** "last_name"**)**
    private String lastName**;**

    *// Getters and setters are ignored for brevity,*
    *// but they're required for Room to work.*
    *//*
    *// (Author note: please go and switch to Kotlin)*
    *// (Author note #2: ok maybe you* [*should wait a bit*](https://stackoverflow.com/questions/44142964/room-persistence-lib-implementation-in-kotlin)*)*
**}**
```

像在任何其他库中一样——您可以为表或列设置自定义名称，您可以选择索引一些内容并指定模型之间的关系。

# API

为了与数据模型交互，我们使用 DAO-数据访问对象。所以谷歌也没有什么新东西。对于每个模型，我们需要声明一个具有 CRUD 操作的 DAO。每个库都包含了主要的功能，并提供了一些相同的特性。

**但是**虽然大多数 ORM 库使用了一些代码内查询构建器，Room 做了一些好事，让你用巧妙的 SQL 格式声明你的查询。

例如，您将如何在每个平台中为特定用户执行简单的查询:

```
*//Room:*
@Query**(**"SELECT * FROM user WHERE age > :minAge"**)**
public User**[]** **loadAllUsersOlderThan(**int minAge**);**

*//DbFlow:*
List**<**User**>** users **=** SQLite**.**select**()**
                         **.**from**(**User**.**class**)**
                         **.**where**(**User_Table**.**age**.**greaterThan**(**minAge**)**
                         **.**queryList**();**

*//ORMLite:*
List**<**User**>** users **=** userDao**.**queryBuilder**()**
                          **.**where**()**
                          **.**gt**(**"age"**,** minAge**)**
                          **.**query**();**
*//GreenDAO:*
List**<**User**>** users **=** userDao**.**queryBuilder**()**
                          **.**where**(**Properties**.**Age**.**gt**(**minAge**))**
                          **.**list**();**
```

一方面——它更干净，我们大多数人都熟悉 SQL 语法，但相反，您正在失去一些对象映射的能力(没有自动完成，您需要记住对象的表)。

# 实时数据

房间图书馆与谷歌宣布的其他架构组件配合得很好，特别是与[实时数据](https://developer.android.com/topic/libraries/architecture/livedata.html)的配合。因此，你可以很容易地得到关于数据变化的通知(通过[实时查询](https://developer.android.com/topic/libraries/architecture/room.html#daos-query-observable)或 [RxJava](https://developer.android.com/topic/libraries/architecture/room.html#daos-query-rxjava) ),你不需要担心数据的实时更新。

这很棒，我觉得这是 Room 的优势之一。然而，我相信这种行为也可以通过在现有的库上添加另一层来处理所有的更改来实现。

> (我编辑了文章并添加了实时数据部分，感谢[就像他们正在做的翻新](https://medium.com/u/7a8d96da8cb6#fetching_data))。

如果你喜欢这篇文章，请点击下面的♡。作为一个作家，它意味着整个世界。

**如果您想让 Medium 告诉您有关新帖子的信息，请单击关注按钮。**

*完整报道:*
[*PerfTestDbFlow-1by 1 . tsv*](http://shem8.github.io/files/posts/room/PerfTestDbFlow-1by1.tsv)
[*PerfTestDbFlow-batch . tsv*](http://shem8.github.io/files/posts/room/PerfTestDbFlow-batch.tsv)
[*PerfTestNotNull-1by 1 . tsv(绿岛)*](http://shem8.github.io/files/posts/room/PerfTestNotNull-1by1.tsv)
[*PerfTestNotNull-batch . tsv(绿岛)*](http://shem8.github.io/files/posts/room/PerfTestNotNull-batch.tsv)
[*perftestorm*](http://shem8.github.io/files/posts/room/PerfTestOrmLite-1by1.tsv)

*原载于 2017 年 6 月 2 日*[*she M8 . github . io*](http://shem8.github.io/blog/2017/06/02/room-performance/)*。*

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客们如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)