# RealmList <realmstring>是一个反模式！</realmstring>

> 原文：<https://medium.com/hackernoon/realmlist-realmstring-is-an-anti-pattern-bfc10efb7ca5>

![](img/1e3ddbdb86752771ef44040ff4a32a08.png)

编辑:任何来自 4.0.0-RC1 和更高版本的服务器都支持本地的`RealmList<String>`，所以用那个代替。

— — — — — — — — — — — — — — — — — — — —

老实说:我真的不喜欢说“ *X 是反模式*”的标题。特别是当特定方法的缺陷不明显时，相反的方法也被认为是反模式，等等。

在`RealmList<RealmString>`的情况下，你根本无法真正“支持”它。我甚至应该在我的[领域模式设计文章](/@Zhuinden/designing-the-schema-of-realm-effectively-and-other-realm-tips-feb76c5b6072#.fxmu0jq9u)中提到这一点。当没有更好的解决方案时，使用`RealmList<RealmString>`来克服`[RealmList of primitives is not supported](https://github.com/realm/realm-java/issues/575)`(从 4.0.0 开始不再是一个限制)的想法是作为一个黑客设计的。除了丑陋和对开发者来说不完全短暂之外，它也带来了一些问题。

好吧，有一个简单的解决方案，但是至少你不会用它来膨胀你的领域模式:)

# 现实主义者的问题<realmstring></realmstring>

## 1.)您需要为它定义一个新的 RealmObject

你需要创建一个 RealmObject，它并不是一个独立的类，一个不必要的包装器。它还创建了一个链接，这意味着…

## 2.)通过链接访问的对象需要手动删除

基本上，如果你在一个类中使用了`RealmList<RealmString>`，那么为了清除绑定到它的`RealmString`对象，你必须首先迭代 RealmResults，在它们的托管 RealmList 上调用`deleteAllFromRealm()`，并且只有**然后**才能真正删除对象。对 RealmResults 进行迭代会为每个被访问的对象创建一个代理，因此对于大型数据集来说这可能是内存密集型的，即使这些数据应该直接属于 RealmObject，而不是通过链接。

## 3.)链接查询比普通查询慢

简单地说，链接查询比索引字段上的查询花费更多的时间。

## **4。)任何对** `**RealmString**` **表的写入都会通知任何其他具有** `**link to it (pre-3.0)**`的 `**RealmObject**`

**(仅适用于 Realm 3.0 之前)粗粒度更改监听器假设如果修改了`RealmString`，您的对象可能已经被修改，这会导致对`RealmChangeListener`的不必要调用，而后者通常会调用`adapter.notifyDataSetChanged()`。**

# **解决方案:将 RealmList <primitive>存储为字符串</primitive>**

**是的，问题是人们试图将原语存储为一个`List`。不过，这不一定是一个列表。考虑一下——您可以将任何元素列表打包成一个字符串。甚至您的日常 JSON 解析器也一直在这么做。**

**对于原始值列表，将列表打包成字符串特别容易。**

**从 `[RealmList<RealmInteger>, …](https://github.com/realm/realm-java/issues/575#issuecomment-173676006)` [一期](https://github.com/realm/realm-java/issues/575#issuecomment-173676006)看[这个主旨:](https://github.com/realm/realm-java/issues/575#issuecomment-173676006)**

**它没有为`List<String>`创建不必要的包装器，而是用连接分隔符将它映射到一个单独的`String`字段中。对于 [Java](https://hackernoon.com/tagged/java) ，这可以通过 Commons-Lang 的`StringUtils.join()`或流 API 反向端口来完成。或者用`StringBuilder`手动。哪个都行。**

# **为什么这个效果很好？**

**由于 [Realm 0.88.0，你需要将 Realm 作为一个 Gradle 插件](https://realm.io/news/realm-java-0.88.0/)添加到项目中，对吗？随之而来的是 Realm-Transformer，它将所有直接的字段访问转换成代理方法调用。**

**这很棒，因为这允许我们手动创建像`total`和`this.total = quantity * price;`一样的“计算属性”。**

**但是这允许我们创建自定义的 getter/setter，即使你给它一个列表，它也设置 String 字段，或者返回一个列表，即使该字段是一个字符串。**

**大概是这样的:**

**如果我们不想将`Commons-Lang`添加到项目中，可以使用 Spring Framework 中的这个方法:**

**或者类似的东西。加入一个字符串应该不会太难。**

# **结论**

**不要创建不必要的链接。吹扫`RealmList<RealmString>`。**

# **添加注释(2017 年 1 月 17 日):**

**但是如果您仍然需要将元素分离到它们自己的列表中，那么为它创建一个单独的 RealmObject，而不是一个`RealmString`。这里有一个具体的例子:**

**这样，你保留了一个双向链接，你的电话号码可以独立地查询**和分类**，允许像`find the telephone numbers that contains “___"`这样的查询，然后使用`telephoneNumber.getPerson()`来得到它属于谁。**

**[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)****[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)****[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)**

> **[黑客中午](http://bit.ly/Hackernoon)是黑客们下午的开始。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。**
> 
> **如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！**

**![](img/be0ca55ba73a573dce11effb2ee80d56.png)**