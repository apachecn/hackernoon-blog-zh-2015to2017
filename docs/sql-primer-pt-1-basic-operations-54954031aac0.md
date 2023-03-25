# SQL 初级—基本操作

> 原文：<https://medium.com/hackernoon/sql-primer-pt-1-basic-operations-54954031aac0>

最近在前端做了很多编码；事实上，我已经做了这么多，我可能需要创建一个快速入门。如果你像我一样需要复习，或者刚刚掌握 SQL，需要一个快速参考，你可能会喜欢这个。对于本入门，我将使用 MySQL 语法。

# SQL 适合我的项目吗？

用外行人的话来说，SQL 是一种我们用来永久保存数据的语言。为了更好地理解这一点，让我进一步解释一下。你通常有一个项目，通常由一个前端组成，可能用 HTML/CSS/JavaScript 编写，然后你通常有一个后端，如 NodeJS 或 Rails。SQL 的用武之地是，你的后端通常通过一个 ORM(对象关系映射)，像[mongose](https://code.tutsplus.com/articles/an-introduction-to-mongoose-for-mongodb-and-nodejs--cms-29527)或[活动记录](https://www.tutorialspoint.com/ruby-on-rails/rails-active-records.htm)，连接到你的存储数据的数据库，这些数据是使用 SQL 创建的。这是任何软件记住您的用户名和密码的方式，以便您可以在以后再次登录。

# create, read, update, and delete

SQL 的基本操作是 **CRUD** ，是 **C** reate、 **R** ead、 **U** pdate、 **D** elete 的缩写。大多数情况下，您总是在创建、读取、更新或删除数据库中的表。通常，您有一个连接到的数据库，并在该数据库中创建通过记录存储数据的表。如果您曾经处理过 Excel 表，那么可以想象数据库就是 Excel 文件本身，您在数据库中创建的表就是 Excel 表文件中的表页，而记录就是 Excel 表页中的行。

要为项目运行创建新数据库，请执行以下操作:

```
CREATE DATABASE name_of_database;
```

在这个例子中，我们将创建一个名为`project`的数据库

```
CREATE DATABASE project;
```

现在我们必须声明我们将使用数据库名`project`:

```
USE project
```

## 创造

现在我们已经创建并使用了一个名为`project`的数据库，我们需要开始向它添加一些表，这样我们就可以保存一些记录。

这将在数据库中创建一个新表。

```
CREATE TABLE table_name (
  table_name_id INT AUTO_INCREMENT PRIMARY KEY,
  column_name VARCHAR(20)
);
```

对于我们的例子，让我们在数据库中创建一个名为`users`的表。下面一行代码应该可以帮助我们完成这个任务:

```
CREATE TABLE users (
  user_id INT AUTO_INCREMENT PRIMARY KEY, 
  username VARCHAR(20),
  age INT
);
```

现在我们有了一个表，我们可以保存某种类型的数据，在 SQL 中我们称之为记录。插入记录的方式如下:

```
INSERT INTO table_name (column1, column2, ...) VALUES (value1, value2, ...);
```

因此，让我们在表中插入两条记录，我们的方法是:

```
INSERT INTO users (username, age) VALUES ('john', 34);
INSERT INTO users (username, age) VALUES ('mary', 18);
```

## 阅读

通常，一旦有了一个表，就想从中检索信息。从表中检索所有记录的方法如下:

```
SELECT * FROM table_name;
```

让我们从`users`表中检索所有内容:

```
SELECT * FROM users;
```

然后我们可以通过使用 SQL 中的`WHERE`子句来指定，这样我们就不会得到所有的东西。

```
SELECT * FROM table_name WHERE column = value;
```

因此，如果我们要从我们的表`users`中获取`john`，我们应该这样做。

```
SELECT * FROM users WHERE username = 'john';
```

## 更新

更新是为了当事情发生变化或我们犯了错误时，希望记录能够反映出来。

```
UPDATE table_name 
SET column = value, column1 = value1, ...
WHERE another_column = another_value;
```

假设我们数据库中的`mary`已经老了一岁，我们想要更新它

```
UPDATE users
SET age = 19
WHERE username = 'mary';
```

## 删除

Delete 是很容易理解的，但是它允许我们删除不再需要的记录或者删除不再使用的表。

```
DELETE FROM table_name
WHERE column = value;
```

假设`john`决定不再想成为我们软件的一部分，并想从我们的产品中删除自己。

```
DELETE FROM users
WHERE username = 'john';
```

因为我们几乎完成了 SQL 初级教程 Pt。让我们从数据库中删除我们的表，然后再从数据库中删除`DROP`。

移走我们的桌子

```
DROP TABLE table_name;
```

对我们来说会是

```
DROP TABLE users;
```

现在结束它，让我们放弃我们不再需要的数据库本身。

```
DROP DATABASE database_name;
```

为了我们。

```
DROP DATABASE project;
```

希望你们喜欢这本书，并度过了愉快的时光。跟我上 Pt。2 !我来过一下`JOIN`和数据库关系！

![](img/073503fa8f5c7d189dd6704ce595ec00.png)

*感谢阅读！请分享推荐给其他感兴趣的读者！*

*查看我的作品集*[*http://mohammedchisti.com*](http://mohammedchisti.com)