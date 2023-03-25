# 不要在 MySQL 全文搜索上浪费时间

> 原文：<https://medium.com/hackernoon/dont-waste-your-time-with-mysql-full-text-search-61f644a54dfa>

我花了宝贵的时间尝试使用 MySQL 版本以来包含的 [InnoDB 全文搜索](https://dev.mysql.com/doc/refman/5.7/en/innodb-fulltext-index.html)功能，最终发现**它工作的用例相当有限** …

作为示例，让我们创建一个非常基本的表:

```
CREATE TABLE document (
  id int PRIMARY KEY,
  content longtext NOT NULL,
  FULLTEXT KEY (content)
)
```

该表填充了**200 万行**。`content`的平均长度为 2 千字节。

现在，让我们运行一个简单的查询:

```
SELECT id 
FROM document 
WHERE MATCH(content) AGAINST ('commercial' IN BOOLEAN MODE) 
LIMIT 50
```

它是在 50 毫秒内执行的**。没问题。**

现在，让我们在同一个查询中结合对“普通”列的过滤和全文搜索:

```
SELECT id 
from document 
WHERE MATCH(content) AGAINST ('commercial' IN BOOLEAN MODE)
AND id > 10000000
LIMIT 50
```

它是在 50 秒内执行的**，这使得它无法使用…**

请注意，这是与上面相同的查询，只有一个附加条件(`id > 10000000`)。此外，该查询不返回任何行，因为附加条件排除了所有行(所有 id 都低于 10，000，000)。

**为什么这么慢？**这是因为我的查询中使用的术语“商业”很常见(大约 300，000 个文档包含该术语)，还因为 MySQL 不擅长合并索引。基本上，MySQL 使用全文索引来查找“商业”一词，然后它执行一种嵌套连接来查找 300，000 行并检查`id`上的条件。前者很快，后者极慢。

如果 MySQL 能够使用内存位图组合多个索引，那就太好了。这将使内置的全文搜索更加有用。与此同时，我必须找到一个替代方案。

如果您有任何关于如何在不切换到另一个数据库引擎的情况下解决这个问题的想法，请联系我。

*感谢* [*卡洛斯奥特罗巴罗斯*](https://medium.com/u/f17156a1915f?source=post_page-----61f644a54dfa--------------------------------) *举报一个错别字。*