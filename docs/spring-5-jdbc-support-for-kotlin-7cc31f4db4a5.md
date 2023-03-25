# 春天 5 JDBC 支持科特林

> 原文：<https://medium.com/hackernoon/spring-5-jdbc-support-for-kotlin-7cc31f4db4a5>

[Spring 5 上周发布](https://spring.io/blog/2017/09/28/spring-framework-5-0-goes-ga)有很多令人兴奋的功能，但对我来说，最重要的是 [Kotlin](https://hackernoon.com/tagged/kotlin) 支持。不仅因为我热爱这门语言，还因为我为它做出了贡献。(我知道，不要脸的塞)

这种支持中包含的一个特性是一组针对`JdbcTemplate`和`MapSqlParamaterSource`的扩展函数，它为您提供了一个更加惯用的 API。

让我们把使用 JDBC 访问关系数据的[和 Spring](https://spring.io/guides/gs/relational-data-access/) 官方指南翻译成 Kotlin

# 使用 Spring 5.0 JDBC

在本例中，我们将使用 Spring Boot 2.0 快照

## 客户数据类别

## 使用 JDBC 模板

来评论一下最有趣的台词吧。

1.  `log`在第 46 行的伴随对象中声明
2.  功能`with`将帮助我们节省一些打字。在`with`区块内，我们可以访问`jdbcTemplate`的所有成员。
3.  `splitUpNames`是一个`List<Pair<String, String>>`。稍后我们将使用`Pair`的析构声明。
4.  在任何具有带析构声明(`component1()`、`component2()`等等)的参数类型的函数中，您可以用它们来替换它。在这种情况下，我们用`(String, String)`替换`Pair<String, String>`。
5.  `batchUpdate`的最后一个参数是一个`ParameterizedPreparedStatementSetter<T>`，它是一个 Java 8 函数接口。我们可以使用 Kotlin 中的函数接口作为函数，包括[析构](https://hackernoon.com/tagged/destructuring)声明。
6.  这个查询是一个扩展函数。改变参数的顺序，将 `RowMapper<T>` 切换为`(ResultSet, Int) ->T`。因为我们没有使用最后一个参数(行号)，所以可以用 _

说到扩展函数，你可以在这里查看 kdoc。

## 使用`NamedParameterJdbcTemplate`

`NamedParameterJdbcTemplate`是`JdbcTemplate`的一个版本，它使用命名参数(`‘:parameter1’`，而不是占位符(`‘?’`)。Spring 5 没有为`NamedParameterJdbcTemplate`提供任何扩展函数(Kotlin 提供的 API 已经足够好了)，但是它为相关类`MapSqlParameterSource`提供了[扩展函数](https://docs.spring.io/spring-framework/docs/5.0.0.RELEASE/kdoc-api/spring-framework/org.springframework.jdbc.core.namedparam/index.html)。

1.  `jdbcOperations`返回一个普通的`JdbcTemplate`如果你仍然需要使用它
2.  `sources`是一个`List<MapSqlParameterSource>`
3.  在类似数组的 setter 中设置参数的扩展函数。
4.  没有类似数组的 getter 来读取扩展函数，但是我们可以很容易地添加一个。
5.  这个插入查询使用命名参数。
6.  `query`接收一个`Map<String, *>`作为第二个参数，在 Kotlin 中非常容易使用。

# 结论

带有 Kotlin 的 Spring JDBC 为您的关系数据库应用程序提供了一个可靠的 API。享受吧。