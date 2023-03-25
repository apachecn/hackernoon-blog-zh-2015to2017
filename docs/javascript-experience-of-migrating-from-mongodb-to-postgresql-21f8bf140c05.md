# Javascript:从 MongoDB 迁移到 PostgreSQL 的体验

> 原文：<https://medium.com/hackernoon/javascript-experience-of-migrating-from-mongodb-to-postgresql-21f8bf140c05>

![](img/f2afea436a5ea8d9b92e0e9811d855d0.png)

## 背景

我们的项目([labcup.net](http://labcup.net))始于一个业余爱好周末项目，事实上..这自然是一个尝试新技术的好地方，这是我们大约 2 年前选择 MongoDB 的原因。该项目已经发展成为一项全职工作，并开始有付费客户。尽管试验在当时是一个正确的决定，但 MongoDB 显然不适合现在的任务(我们有相对较小的数据库，用户很少，但非常需要一致性)。
我们知道这一点，但必须继续前进，直到今年夏天，当我们花了这么多时间来发现和修复[数据库](https://hackernoon.com/tagged/database)的不一致性，我们不能再忍受下去了，我们必须采取行动。
*注意*:不一致不是因为缺少测试，而是因为缺少类型检查(或者静默转换)、缺少事务以及错误的假设；—除非检查代码，否则只能猜测结构和类型。
*注*:项目规模在 60K LOC 左右(无注释，根据 [*cloc*](https://github.com/AlDanial/cloc) )而且只是打字稿，绝对不是小项目。
我还必须在这里提到，typescript 在类型检查和结构定义方面帮助很大，但显然还需要更多。
*注意*:MongoDB 也有其他问题。例如:
-ObjectId 占用了 32 个字符，这对于在客户端发送和存储来说是一个很大的数目(我们在浏览器的 indexedDb 上缓存数据)。
-不是最大的问题，但令人讨厌且容易出错的是总是转换查询的 ObjectId(例如，如果字符串用于查询，它只是安静地返回，没有结果)
-一些神秘的不一致可能是由于令人惊讶的索引使用，其中不是所有的记录都已返回。(见此[https://engineering . meteor . com/MongoDB-queries-dont-always-return-all-matching-documents-654 b 6594 a 827 #、j1n0zyk2d](https://engineering.meteor.com/MongoDB-queries-dont-always-return-all-matching-documents-654b6594a827#.j1n0zyk2d) )

## 为什么选择 PostgreSQL？

之前我们都有使用 MySQL/MariaDb 的经验，但是 PostgreSQL 对 JSON 列和操作符有更好的支持，然而仅此一点就足以成为选择它的理由，但是像([http://www.wikivs.com/wiki/MySQL_vs_PostgreSQL](http://www.wikivs.com/wiki/MySQL_vs_PostgreSQL))这样的帖子也给我们留下了非常好的印象，我们希望深入了解。最后但并非最不重要的一点是，我不指望 Oracle 会花太多精力来长期开发 MySQL。所以 PostgreSQL 是我们的自然选择。

## PostgreSQL 用哪个 js 库？

因为我们都对 ORM 有偏见，主要是因为
-它们往往被过度设计
-它们通常不支持额外的功能(因为最小公分母)
-通常你必须学习一些新的语法，而不是使用纯 SQL
-通常你必须预定义许多东西，这是很大的开销
-这不是真正的抽象，因为你必须对数据库和 ORM 都很好，这样它只是站在路上。

…我们不会再更改数据库。

我们所寻找的是一个简单的库，轻松涵盖 95%的查询(其余的可以用 SQL 完成)。它不应该使用回调，至少应该支持承诺，但是 async-await 会更棒。
现在 [pg.js](https://github.com/brianc/node-postgres) 支持 async-await，但实际上，它太简单了。另一方面，看起来正是我们想要的。不幸的是，当我们开始使用它时，越来越多的限制出现了。例如，没有事务，没有池，每个查询打开和关闭一个连接，没有记录器，它只猜测 JSON 类型，当您插入一个空数组[]时会失败。主要是使用关系或完全 JSON 列表。经过几天的试验，我们决定编写自己的库。在 pg.js 上建立了完整的 typescript 和 async-await 支持，作为一名优秀的艺术家，s̶t̶e̶a̶l̶从 Massive 借鉴了一些想法，并最终编写了 pogi。

起初，看起来编写我们自己的处理程序库需要更长的时间，但是我们实际上更快地直接编写我们需要的东西，而不是解决问题。

![](img/61f486ce3950076efabe4f9ebca2b372.png)

## 花了多长时间？

加在一起大约是两个半人工月。大约 1 个月用于转换，1 个多月用于测试应用程序的每个部分。(也浪费了大量时间来解决不一致的问题。)

好的一面是，使用 jsonb 专栏，我们可以节省很多时间。只是必须转换的重要领域(一个与外键，需要(特殊)检查，必须或应该存在，一个可以有特殊索引等。)其余的可以放在 jsonb 列中，所以基本上不需要从 MongoDB 进行转换。

至于查询，语法与 MongoDB 没有太大的不同，所以除了连接查询之外，几乎所有的修改都很简单。但是删除大量代码并用一个优雅的小连接代替它是一件令人愉快的事情。

公平地说，这也是在一些技术部门工作的好时机。因为完整的测试无论如何都要进行，所以在那里也要进行几天。

## 到目前为止，你最喜欢/不喜欢 PostgreSQL 的什么？

当然喜欢:
- jsonb 列
-部分和转换的索引，例如:

```
CREATE UNIQUE INDEX “magical_idx” ON “users” (LOWER(“name”), NULLIF(“removed”, true));
```

因此不可能创建只有大小写不同的两个用户，但是如果用户被“删除”，您可以重用这个名称。(注:这只是一个例子，不一定是好的设计。)
——数组式，主要结合枚举，感觉就是自然。

不喜欢:
-在 MySQL 中添加创建/更新的时间戳很容易-可以添加到表定义中-在这里为每个表添加触发器有点麻烦
-复杂类型感觉有点像 hack，没有外键检查和麻烦的查询，我只是觉得不值得使用它们。

![](img/d8dfa3eda443b2fb6de51b231285dbe7.png)

## 有哪些棘手的部分？

*   在 MongoDB 中，你有唯一键(uuid ),这是很难猜测的，也使得暴力扫描有点困难。幸运的是，有一种简单的方法可以在 PostgreSQL 中创建不可猜测的索引，因此您可以在随机情况下拥有跨表的唯一键。通过将列定义为:

```
“id” varchar PRIMARY KEY NOT NULL DEFAULT (‘us’ || nextval(‘users_id_seq’)::text || (LPAD(floor(random()*1000)::text, 3, ‘0’))),
```

通过这种方式，每个表都有一个唯一的前缀(“us”代表“users”)，然后是一个序列号，最后是一个随机数，以使对用户/文件或其他内容的迭代变得更加困难(或者在适当的位置使用阻塞是不可能的)。这甚至可以通过将数字转换为使用所有字母的基数来缩短([https://www . PostgreSQL . org/message-id/482 b 7 faf . 7000902% 40 lorenso . com](https://www.postgresql.org/message-id/482B7FAF.7000902%40lorenso.com))。

*   类型转换
    使用完整的 json 文档存储转换没有问题(只有 ObjectId 对 MongoDB 来说比较麻烦)，但是当你开始使用两个系统时，不可避免地会有一些东西有不同的表示。例如，PostgreSQL bigInt 可以比 [javascript](https://hackernoon.com/tagged/javascript) 更精确地表示更大的数字。在我们的库中，我们已经解决了这个问题，如果这个数大于。MAX_SAFE_INTEGER，那么我们抛出一个异常，否则转换数字。对于 95%的情况来说，这是一个很好的折衷，在其余的情况下，需要自己处理转换。
    对于处理数组，您还必须选择:
    -使用 json(b)列，但随后您必须自己验证管理数据一致性
    -使用 PostgreSQL，例如枚举、字符串、数字或复杂类型数组
    对于第二种情况，lib 会自动转换前 3 种基本类型，但对于复杂类型，它需要一些额外的工作(要么在 PostgreSQL 中将其转换为 json，要么在 javascript 中解析字符串表示)。
*   delete vs 设置为 null
    对于 MongoDB(或者更像是对于 JSON)，你是在操作对象，比如删除一个字段其实就是从对象中删除字段:
    delete record . price；然后将整个记录发送到服务器以覆盖先前的记录。
    但是对于关系数据库，因为你只覆盖已经改变的内容，所以你必须将字段设置为空。总的来说没什么大不了的，但这是一个很小的基本差异，需要特别注意。

仅此而已，它比我们最初预期的更容易改变(这也是我们一直推迟的原因)，只是很难找到时间完全停止开发并完全专注于这个庞大的技术部(因为我们不是 MongoDB 的目标受众)。正如预期的那样，现在我们有了事务支持，许多查询变得更快了，并且有许多类型检查，也可以作为文档…

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)