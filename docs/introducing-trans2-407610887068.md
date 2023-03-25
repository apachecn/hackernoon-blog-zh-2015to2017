# Trans 2.0 简介

> 原文：<https://medium.com/hackernoon/introducing-trans2-407610887068>

![](img/e626215597e0e7e045f726296db0307a.png)

## 更小、更安全、更易用、更灵活

# 什么是反式？

[**Trans**](https://hex.pm/packages/trans) **是一个管理嵌入数据结构**的翻译的药剂库。虽然 [Trans](https://hackernoon.com/tagged/frans) 只能用于从映射或结构中获取翻译，但当与 [Ecto](https://hex.pm/packages/ecto) 结合使用时，它可以从数据库中获取和查询翻译。

传统的翻译管理方法是使用专用于翻译存储的数据库表。例如，我们可能有一个`posts`表和一个伴随的`post_translations`表。这是 Ruby gem [Globalize](https://rubygems.org/gems/globalize) 使用的方法。

虽然这种方法经过了实战检验，并且行之有效，但它也有一些可以改进的缺点:

*   这使得数据库模式变得复杂，需要为每个可翻译的模式创建一个新表。
*   这使得数据库迁移变得复杂，因为表结构的变化必须复制到转换表中。
*   它需要常量*连接*来过滤或获取记录及其翻译。当多个可翻译模式介入一个查询时，这对性能有明显的影响。

现代的 RDBMSs 支持非结构化的数据类型，比如 JSON。**我们可以利用这种非结构化的数据类型支持，将翻译嵌入到模式本身的字段中**，而不是为翻译使用单独的模式。这消除了同步维护重复数据库表的需要，并大大减少了查询中的 JOIN 子句的数量。

虽然数据库本身提供了访问非结构化数据的机制，但 Ecto 本身并没有提供一个高级接口来方便地访问这些数据。这是传送任务开始的地方。

Ecto 允许您编写定制的 SQL 片段，可以用来访问数据库中的 JSON 字段。为每个需要翻译的查询编写这些 SQL 片段会很快变得令人厌倦并且容易出错。Trans 为您生成 SQL 片段，并在编译时验证它们。

# Trans 1.0 sins

## 绕过查询的 Ecto 语法

[爱克托。查询](https://hexdocs.pm/ecto/Ecto.Query.html#content)为查询生成提供了一个很好的语法。可以使用一系列宏(或函数，取决于您喜欢的 API)编写查询，这些宏允许参数插值、数据绑定、组合和前缀。

Trans 没有增强它，而是提供了它自己的、不同的、不与之交互的 API。这造成了一系列的不便和限制。

第一个也是最明显的不便出现在用可翻译数据的一些条件编写查询时。Trans 1.0 不是在构建查询时指定条件，而是要求首先构建查询，然后将查询传递给添加所需条件的函数。

第一个也是最明显的不便出现在用可翻译数据的一些条件编写查询时。对于普通的 ect 查询，您将在构建查询本身时指定条件。相反， **Trans 1.0 要求首先构建查询，然后将其传递给一个函数，该函数将添加所需的条件。**

The **QueryBuilder** component bypassed **Ecto.Query** functionality. Ugly to say the least…

这种方法的另一个问题是，可翻译字段上的**条件只能添加到查询**的主模式上。联接模式和关联已经过时了…哎哟！

## 混合模式模块中的职责

翻译容器是嵌入模式翻译的字段。Trans 默认使用一个名为 translations 的字段，但允许对其进行定制。

在 Trans 1.0 中，有两种选择:直接调用 QueryBuilder 函数，或者在模式模块中使用 Trans 来设置一些方便的函数以避免重复。设置便利函数是最舒适的选择，但是它**给不属于那里的模式模块**增加了额外的责任和关注。

Trans 1.0 polluted the modules interface and mixed concerns that should belong to other parts of the application.

## 对翻译错误不安全

Trans 1.0 针对翻译错误执行了一些基本的检查。例如，试图在不可翻译的字段上添加查询条件会导致运行时`ArgumentError`。

**Trans 2.0 改进了这些检查，并在编译阶段而不是运行时产生错误。**

Trans would let you add conditions on non-existing fields which resulted in run-time errors.

# Trans 2.0 改进

## 包含用于查询的 Ecto 语法

在 Trans 2.0 中，QueryBuilder 组件已经完全重写:**它现在在编译时运行，它唯一的任务是生成所需的 SQL 片段**。QueryBuilder 模块中所有不同的功能都被统一到了`translated/3`宏中。

作为一个宏，QueryBuilder 可以在编译时生成 SQL 片段，并将其添加到正在编译的查询中。这样我们可以在创建 Ecto 查询时使用`translated/3`宏，**与** `**Ecto.Query**` **和** `**Ecto.Query.Api**`提供的其余函数和宏进行交互。

The **translated/3** macro is compatible with **Ecto.Query** and **Ecto.Query.Api**

## 从模式模块中提取责任

对于 Trans 2.0，**模块仅导出一个名为** `**__trans__**`的下划线函数，该函数返回模块的 Trans 配置。

这个**保持了客户端模块的整洁，并保持了包含在 QueryBuilder 和 Translator 模块**中的逻辑，而不是将它分散在客户端代码中。

## 尽快进行安全检查

如前所述，Trans 1.0 在运行时做了一些基本的检查。由于每个模块的翻译配置是在编译期间设置的，Trans 2.0 在运行前使用它来执行安全检查。

想翻译一个不存在的字段吗？您的应用程序无法编译。想要翻译不可翻译的字段？呜…你的应用程序无法编译。嗯……你明白了。这使得 Trans 2.0 比之前的版本更安全，更平易近人。

# 未来计划

通过此次更新，Trans 处于良好的维护和持续改进状态。我想在 Trans 的未来版本中包含的主要功能有:

*   **支持 MySQL** 。MySQL 5.7 引入了一个原生 JSON 类型，Trans 可以像使用 PostgreSQL JSONB 类型一样使用它。在 [MySQL 数据库驱动](https://github.com/xerions/mariaex)仓库中有一个[未解决的问题](https://github.com/xerions/mariaex/issues/119)来增加对这种类型的支持。
*   [**指定可用的区域设置**](https://github.com/belaustegui/trans/issues/14)**[**自定义回退序列**](https://github.com/belaustegui/trans/issues/12) 。目前，当翻译不存在时，Trans 会回到默认语言。可以对这种行为进行定制，以便在回到默认语言之前检查其他语言环境。**
*   ****使翻译成为嵌入式模式，并创建一个定制的 Ecto 类型**。这仍然是一个未完成的想法，但是这里的主要目标是将翻译表示为嵌入式模式。这样，翻译可以指定它们自己的变更集，并执行所需的验证或转换。**

# **包扎**

**Trans 首次发布是在 2016 年 6 月 4 日，所以很快就到了一年的寿命。这是一段有趣的旅程，**我们发布了 6 个版本，提交了 159 个，下载量达到了近 250 次**。谢谢大家！**

**随着这个新版本的发布，Trans 已经达到了一个成熟的水平，使得它在未来的时间里更加可用、安全和可维护。所以期待未来更多的改进和特性。**

**如果你发现了一个 bug，或者有任何想法或评论，欢迎发表在这里，或者[在 GitHub](https://github.com/belaustegui/trans/issues/new) 上发表一个问题。在 Elixir 论坛的 [Trans 线程中也有有趣的讨论。](https://elixirforum.com/t/trans-embedded-translations-for-elixir/840)**

**在结束这篇文章之前，我想特别感谢我的朋友们[óscar de arriba](https://medium.com/u/ff2508be75f8?source=post_page-----407610887068--------------------------------)， [Victor Ortiz](https://medium.com/u/a15f8add882e?source=post_page-----407610887068--------------------------------) ， [Enol Iglesias](https://medium.com/u/a70e615c440b?source=post_page-----407610887068--------------------------------) ，[Ruben Sierra](https://medium.com/u/d0b19f8b8579?source=post_page-----407610887068--------------------------------)和[dreaming choes](https://medium.com/u/909015f6e9c2?source=post_page-----407610887068--------------------------------)对❤️.的所有支持在 [ElixirConf 上见。欧盟](http://www.elixirconf.eu/)！**

**[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)****[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)****[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)**

> **黑客中午是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。**
> 
> **如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！**

**![](img/be0ca55ba73a573dce11effb2ee80d56.png)**