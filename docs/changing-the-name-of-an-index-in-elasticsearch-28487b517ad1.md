# 在 Elasticsearch 中更改索引的名称

> 原文：<https://medium.com/hackernoon/changing-the-name-of-an-index-in-elasticsearch-28487b517ad1>

![](img/d8845da741713f12ddabdd2ba0d12b16.png)

嘿，

今天我碰巧写了一个脚本来解决一个看起来很多人都面临的特定问题:重命名一个给定的 Elasticsearch 索引。当然，有文档记录的解决方案，但是我没有很快找到一个脚本来让我到达我想要的地方——现在，名为`a`的索引中的所有数据都可以在名为`b`的索引中查询，并且设置了所有属性。

*注意。:以下代码针对 Elasticsearch 2.4.6。*

它来了。

# 逐步重建索引

实现我们的目标有四个步骤:

1.  创建一个 Elasticsearch 索引并用一些数据填充它；
2.  获取原始索引的配置；
3.  使用所需的配置创建新索引；
4.  运行`_reindex`行动；
5.  删除旧索引。

## 0.创建一个 Elasticsearch 索引并用一些数据填充它

为了使用默认参数(例如碎片和副本的数量)创建索引，我们可以针对 Elasticsearch HTTP 端点发出一个`POST`，指定所需的索引(在本例中为`acme-production`:

当然，它没有数据索引:

现在我们用一些数据填充它:

我们可以通过再次查看`/_cat/indices`终点来验证这一点:

## 1.获取原始索引的配置

因为重命名只不过是“创建、复制和删除”，所以我们需要用旧索引的属性创建一个新索引。为了正确实现这一点，我们必须复制旧的配置:

ps。:这里我使用了轻量级命令行 JSON 处理器 [jq](https://stedolan.github.io/jq/) ，以便从调用`/<index>/_settings,_mappings`返回的更大对象中获取映射和设置对象。这样我们就可以把它赋给一个变量，然后在以后使用它。

## 2.使用所需的配置创建新索引

使用旧的配置(存储在`index_config`变量中),我们能够基于它创建索引:

*ps。:即使在* `*$index_config*` *对象中有一个* `*uuid*` *，也没关系——它会在新的索引中被一个新的* `*uuid*` *所取代。*

## 3.运行`_reindex`动作

正确配置两个索引后，我们就可以将旧索引中的数据放入新索引中了:

到目前为止，您应该已经填充了新的索引。现在的问题是删除旧的索引:

## 4.删除旧索引

如果您不打算使用旧索引，现在是时候放弃它了:

## 化名呢？

我在 Reddit 上收到了一些非常有趣的反馈，我想在这里分享一下。

事实证明，有时我们可以通过使用别名来避免`reindex` ing(参见[弹性搜索指数别名](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/indices-aliases.html))。

其思想是，当我们需要通过使用另一个名称来引用一个索引所覆盖的内容时，我们可以创建某种指向真正索引的“指针”,并对这个指针(别名)执行所有常规操作。允许这样做的 API 本质上允许我们`CRUD`(创建、移除、更新和删除)别名，使我们完全有可能执行我们想要的:实现索引的“重命名”，即使只是虚拟的。

那我们就这么做吧。

首先，像前面一样创建一个`acme-production`指数，并添加一些数据:

然后创建一个名为`acme-staging`的`alias`:

如果我们检查指数，我们会发现我们没有任何新的指数，虽然:

但是我们有化名:

这允许我们对`acme-staging`执行查询并从`acme-production`检索数据:

现在，如果我们希望不允许对旧索引的请求呢？好像我们真的改名了，没有重复？然后我们需要使用[打开/关闭索引 api](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/indices-open-close.html) 关闭旧索引:

```
curl -XPOST [http://localhost:9200/acme-production/_close](http://localhost:9200/acme-production/_close)
```

然后我们可以试着从`acme-production`得到:

酷，我们想要的，哈？现在，如果我们试图从`acme-staging`得到:

我们也无法恢复。

对我来说，这听起来很合理，因为别名只是指向另一个索引(已关闭)的指针。

所以，总而言之，如果你想让一个新的索引指向一个已有的索引(就好像你在重命名)，别名将会拯救你，你将需要执行 0 数据复制。

如果您需要像“重命名”这样的东西，并且不允许访问旧索引，那么`alias`不会帮助您(将不得不使用`reindex + delete`策略。

我以前从来没有用过别名，知道它们的存在真的很好！有时候它肯定会非常有用。

# 关闭思路和资源

作为一个从未真正深入研究 Elasticsearch 如何工作的人，我发现重建索引的整个概念非常简单。官方文档非常好，有了它，我能够很快解决这个问题。恭喜弹力搜索团队！

谢谢，

*完成*

*原载于 2017 年 11 月 21 日* [*ops.tips*](https://ops.tips/blog/change-name-of-index-elasticsearch/) *。*