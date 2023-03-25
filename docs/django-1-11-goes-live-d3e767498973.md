# Django 1.11 上线

> 原文：<https://medium.com/hackernoon/django-1-11-goes-live-d3e767498973>

![](img/4f0dabf112c702f46f7692a4c4c6caa4.png)

该版本已被指定为长期支持(LTS)版本，这意味着至少在未来三年内将应用安全和数据丢失修复。

> [**点击阅读更多关于 Django 的文章。**](https://djangostars.com/blog/?utm_source=medium&utm_medium=hackernoon.com&utm_campaign=django%201.11&utm_content=click%20here)

# 新功能:

这个特性允许为数据库创建复合和定制索引。自定义索引为数据库查询提供了更好的优化，并且可以加快数据库的响应时间。

定制表单的输出绝对是一件痛苦的事情。虽然目前我们只是简单地使用 API，而且这个特性似乎没有必要，但它仍然是一个很好的改进，可以方便标准表单的定制。至少我们可以在 django admin 中看到这一点。

*   [**子查询表达式**](https://docs.djangoproject.com/en/1.11/releases/1.11/#subquery-expressions) **使用 ORM 创建显式子查询**

这很有趣，因为 Django 1.8 为 Django ORM 引入了条件表达式。子查询表达式扩展了 ORM 的功能。因此，您可以在没有原始 SQL 查询的情况下创建更复杂的东西。

您可以使用**子查询**表达式向 QuerySet 添加显式子查询。

例如，要用帖子最新评论作者的电子邮件地址来注释每个帖子:

```
>>> from django.db.models import OuterRef, Subquery >>> newest = Comment.objects.filter(post=OuterRef('pk')).order_by('-created_at') >>> Post.objects.annotate(newest_commenter_email=Subquery(newest.values('email')[:1]))
```

在 PostgreSQL 上，SQL 看起来像:

```
SELECT "post"."id", ( SELECT U0."email" FROM "comment" U0 WHERE U0."post_id" = ("post"."id") ORDER BY U0."created_at" DESC LIMIT 1 ) AS "newest_commenter_email" FROM "post"
```

## **您可能还喜欢:**

[](https://djangostars.com/blog/merging-django-orm-with-sqlalchemy-for-easier-data-analysis/?utm_source=medium&utm_medium=hackernoon.com&utm_campaign=django%201.11&utm_content=youmayalsolike1) [## 将 Django ORM 与 SQLAlchemy 合并以简化数据分析

### Django 产品的开发通常简单明了:优秀的文档，许多现成的工具…

djangostars.com](https://djangostars.com/blog/merging-django-orm-with-sqlalchemy-for-easier-data-analysis/?utm_source=medium&utm_medium=hackernoon.com&utm_campaign=django%201.11&utm_content=youmayalsolike1) [](https://djangostars.com/blog/django-performance-optimization-tips/?utm_source=medium&utm_medium=hackernoon.com&utm_campaign=django%201.11&utm_content=youmayalsolike2) [## Django 性能优化技巧

### 当开发人员接到一个在 Django 上进行性能优化的任务时，我经常会遇到这种情况…

djangostars.com](https://djangostars.com/blog/django-performance-optimization-tips/?utm_source=medium&utm_medium=hackernoon.com&utm_campaign=django%201.11&utm_content=youmayalsolike2) 

**引用外部查询集中的列**

当子查询中的 queryset 需要引用外部查询中的字段时，请使用 OuterRef。它的行为类似于 F 表达式，只是在解析外部 queryset 之前，不会检查它是否引用了有效字段。

OuterRef 的实例可以与子查询的嵌套实例一起使用，以引用不是直接父级的包含 queryset。例如，此 queryset 需要在一对嵌套的子查询实例中才能正确解析:

```
>>> Book.objects.filter(author=OuterRef(OuterRef('pk')))
```

**Exists()** 子查询

Exists 是使用 SQL EXISTS 语句的子查询子类。在许多情况下，它的性能优于子查询，因为当找到第一个匹配行时，数据库能够停止对子查询的求值。

例如，要在每篇文章上标注是否有最近一天的评论:

```
>>> from django.db.models import Exists, OuterRef >>> from datetime import timedelta >>> from django.utils import timezone >>> one_day_ago = timezone.now() - timedelta(days=1) >>> recent_comments = Comment.objects.filter( ... post=OuterRef('pk'), ... created_at__gte=one_day_ago, ... ) >>> Post.objects.annotate(recent_comment=Exists(recent_comments))
```

在 PostgreSQL 上，SQL 看起来像:

```
SELECT "post"."id", "post"."published_at", EXISTS( SELECT U0."id", U0."post_id", U0."email", U0."created_at" FROM "comment" U0 WHERE ( U0."created_at" >= YYYY-MM-DD HH:MM:SS AND U0."post_id" = ("post"."id") ) ) AS "recent_comment" FROM "post"
```

# 以前的版本

随着 Django 1.11 的发布，Django 1.10 已经到了主流支持的末期。Django 1.10 将在 2017 年 12 月之前的八个月内获得安全和数据丢失修复。

*来源:*【https://docs.djangoproject.com/en/1.11/releases/1.11/】T2

[![](img/45a1ce60def7b50ffe2311c7a897d600.png)](https://djangostars.com/services/python-django-development/?utm_source=medium&utm_medium=hackernoon.com&utm_campaign=django%201.11&utm_content=banner_end)

我们随时欢迎您提出问题，分享您想阅读的话题！

> 如果你觉得这篇文章有用，请点击👏下面的按钮:)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)