# 如何查询 JSONB，初学 sheet cheat

> 原文：<https://medium.com/hackernoon/how-to-query-jsonb-beginner-sheet-cheat-4da3aa5082a3>

假设我们必须在 PostgreSQL 9.5+数据库上查询一个带有`metadata` JSONB 列的`user`表。

![](img/32e2c5d971bc54da8c28851ef1271f5e.png)

## 1.按第一级属性的值选择项目(#1 路)

您可以使用`metadata`上的`@>`操作符进行查询。该操作符可以将部分 JSON 字符串与 JSONB 列进行比较。是*围堵*操作员。对于这种情况，您可能需要在`metadata`列中添加一个杜松子酒索引。

```
SELECT * FROM users WHERE metadata @> '{"country": "Peru"}'; 
```

## 2.按第一级属性的值选择项目(#2 路)

`->>`操作符以文本形式获取一个 JSON 对象字段。如果您想要查询 JSONB 列中的简单字段，请使用它。您可以在`metadata->>'country'`上添加一个 B 树索引。

```
SELECT * FROM users WHERE metadata->>'country' = 'Peru';
```

## 3.选择项目属性值

再次，`->>`操作符以文本形式获取一个 JSON 对象字段。直接在`SELECT`里用就行了。

```
SELECT metadata->>'country' FROM users;
```

## 4.仅选择具有特定属性的项目

您可以将`->>`操作符与您在文本上使用的经典操作符一起使用:`=`、`<>`、`IS NULL`等。不要忘记用 B 树索引来索引`metadata->>'country'`。

```
SELECT * FROM users WHERE metadata->>'country' IS NOT NULL;
```

## 5.按嵌套属性的值选择项目

您可以同时使用`@>`或`->>`，就像第一级属性一样。根据自己的选择添加一个索引。

```
SELECT * FROM users WHERE metadata->'company'->>'name' = "Mozilla";SELECT * 
  FROM users 
  WHERE metadata @> '{"company":{"name": "Mozilla"}}';
```

## 6.通过数组中的属性值选择项目

记住`@>`操作符检查 JSONB 列中的包含，您可以通过将`{"x":["a"]}`传递给`WHERE`子句来查询类似于`{"x": ["a", "b", "c"]"}`的数组:

```
SELECT * FROM users WHERE metadata @> '{"companies": ["Mozilla"]}';
```

## 7.属性上的 IN 运算符

有时，我们可能需要选择 JSONB 列中的属性与一组可能的值匹配的项目。

```
SELECT * FROM users 
  WHERE metadata->>'countries' IN ('Chad', 'Japan');
```

## 8.插入整个对象

照常使用`UPDATE ... SET`，将整个对象作为 JSON 传递。

```
UPDATE users SET metadata = '{"country": "India"}';
```

## 9.更新或插入属性

使用`||`操作符将实际数据与新数据连接起来。它将更新或插入值。

```
UPDATE users SET metadata = metadata || '{"country": "Egypt"}';
```

## 10.移除属性

操作员`-`从对象上取下钥匙。

```
UPDATE users SET metadata = metadata - 'country';
```

# 最后一个音符

对 JSONB 对象的查询几乎和经典的 SQL 查询一样简单。我在这里只贴了几个例子，关于对我来说最常见的用例。这是给我的一个提示，我希望它也能帮助其他人。

您可以阅读 PostgreSQL 文档，其中有更多的示例和更精确的解释:

[](https://www.postgresql.org/docs/9.6/static/functions-json.html) [## PostgreSQL:文档:9.6: JSON 函数和操作符

### 注意:对于 json 和 jsonb 类型，这些操作符有并行的变体。字段/元素/路径…

www.postgresql.org](https://www.postgresql.org/docs/9.6/static/functions-json.html) [](https://www.postgresql.org/docs/9.6/static/datatype-json.html) [## PostgreSQL:文档:9.6: JSON 类型

### PostgreSQL 只允许每个数据库使用一种字符集编码。因此，JSON 类型不可能…

www.postgresql.org](https://www.postgresql.org/docs/9.6/static/datatype-json.html) 

请随时提出建议、反馈和批评。我很乐意了解更多。

感谢[艾米莉·施耐德](https://medium.com/u/adfa6e96ed77?source=post_page-----4da3aa5082a3--------------------------------)(再次)的点评。