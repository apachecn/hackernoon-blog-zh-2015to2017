# ActiveRecord —命名查询片段占位符

> 原文：<https://medium.com/hackernoon/activerecord-named-query-fragment-placeholders-8f541535586f>

每当我想在 Rails 的原始 SQL 查询片段中使用占位符时，我会使用`?`,最后写下—

```
Post.where('published_at > ?', some_time)
```

如果我想添加另一个条件来检查`some_time`中的相同值，该怎么办？

```
Post.where('published_at > ? OR saved_at > ?', some_time, some_time)
```

老实说，编写这样的代码从来没有困扰过我，我也没有花一点力气去看看是否有更好的方法。

有一天，我和我的一个同事[普瑞蒂·库玛](https://medium.com/u/282a72573069?source=post_page-----8f541535586f--------------------------------)配对，她在她的查询片段中有一些看起来像*符号*的东西，像这样——`published_at > :date`。

我以前从未见过这样的东西，我在想这是不是某个深奥的数据库功能还是:D 的什么东西

那时我才知道，可以在查询片段中使用命名占位符，并为 [ActiveRecord](https://hackernoon.com/tagged/activerecord) 传入一个散列来解析这些值。

现在，我可以愉快地把我的声明改写成—

```
Post.where('published_at > :time OR saved_at > :time', selected_time: some_time)
```

人们可以很容易地看到许多小的胜利，比如—

*   如果占位符名称经过深思熟虑，较长的查询可读性会更好。降低认知开销。
*   你没有重复写出多次使用的变量。
*   位置耦合中断。