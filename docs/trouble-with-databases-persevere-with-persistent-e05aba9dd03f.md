# 数据库有问题？持之以恒的坚持！

> 原文：<https://medium.com/hackernoon/trouble-with-databases-persevere-with-persistent-e05aba9dd03f>

我们的[最近系列](https://mmhaskell.com/blog/2017/8/7/the-future-is-functional-haskell-and-the-ai-native-world)在[周一早上哈斯克尔](https://www.mmhaskell.com/)专注于机器学习。特别是，我们深入研究了 Haskell 张量流库。虽然人工智能确实是一个巨大的领域，但它并不占日常工作的大部分。要构建一个基本的生产系统，还有许多更简单的任务。在我们最新的系列中，我们将学习许多不同的库来执行这些任务！

在第一篇文章中，我们将讨论[持久性](https://hackage.haskell.org/package/persistent)。许多库允许您进行快速 SQL 调用。但是坚持不止于此。使用 Persistent，您可以将 Haskell 类型链接到数据库定义。您还可以进行类型安全查询，以省去解码数据的麻烦。总而言之，这是一个非常酷的系统。

这个系列的所有代码都会在 [Github](https://github.com/jhb563/ProdHaskellSeries) 上！为了跟随这篇文章，看一看`[persistent](https://github.com/jhb563/ProdHaskellSeries/tree/persistent)` [分支](https://github.com/jhb563/ProdHaskellSeries/tree/persistent)。

# 我们的基本类型

我们首先考虑一个简单的用户类型，如下所示:

```
data User = User
  { userName :: Text
  , userEmail :: Text
  , userAge :: Int
  , userOccupation :: Text
  }
```

假设我们想在 SQL 数据库中存储这种类型的对象。我们首先需要定义表来存储我们的用户。我们可以用手动 SQL 命令或通过编辑器来实现，但不管怎样，这个过程都容易出错。该命令类似于以下内容:

```
create table users (
  name varchar(100),
  email varchar(100),
  age bigint,
  occupation varchar(100)
)
```

当我们这样做时，没有任何东西将我们的 Haskell 数据类型链接到表结构。如果我们更新 Haskell 代码，我们必须记得更新数据库。这意味着编写另一个容易出错的命令。

从我们的 Haskell 程序中，我们还想基于用户的结构进行 SQL 查询。我们可以写出这些原始命令并执行它们，但同样的问题也适用。这种方法容易出错，而且根本不是类型安全的。坚持帮助我们解决这些问题。

# 持久和模板 Haskell

我们可以从 Persistent 获得这些奖金，而不需要那么多额外的代码！为此，我们将使用模板 Haskell (TH)。我们在过去为不同的数据类型派生[透镜和棱镜](https://mmhaskell.com/blog/2017/6/12/taking-a-close-look-at-lenses)时见过一次。在那种情况下，我们注意到了一些赞成和反对的意见。它确实允许我们避免编写一些样板代码。但是它也会使我们的编译时间变长。这也会让没有经验的 Haskellers 更难访问我们的代码。但是对于镜头，它只为我们节省了几十行代码。使用 Persistent，th 会生成更多的代码，所以好处肯定大于坏处。

当我们用 th 创建镜头时，我们使用了一个简单的声明`makeLenses`。在这里，我们将做一些更复杂的事情。我们将使用一种叫做“准引用者”的语言结构。这是一个代码块，遵循程序员或库中设计的一些语法，而不是普通的 Haskell 语法。它经常用在做某种外来函数接口的库中。我们用括号和管道的组合来界定一个准报价器。下面是 Haskell 调用的模板。准引用者是最后一个论点:

```
import qualified Database.Persist.TH as PTHPTH.share [PTH.mkPersist PTH.sqlSettings, PTH.mkMigrate "migrateAll"] [PTH.persistLowerCase||]
```

`share`函数接受设置列表，然后接受准报价器本身。然后，它为我们的数据模式生成必要的模板 Haskell。在本节中，我们将定义数据库将使用的所有不同类型。我们注意到某些特定的设置。我们特别指定了`sqlSettings`，所以我们在这里所做的一切都将集中在 SQL 数据库上。更重要的是，我们还创建了一个迁移函数，`migrateAll`。在这个模板 Haskell 被编译之后，这个函数将允许我们迁移我们的数据库。这意味着它将为我们创建所有的表！

但是在我们看到这一点之前，我们需要重新定义我们的用户类型。我们将在准引用程序中定义`User`，而不是用普通的 Haskell 方式。注意，这个级别的模板 Haskell 需要许多编译器扩展。这是我们的定义:

```
{-# LANGUAGE TemplateHaskell            #-}
{-# LANGUAGE QuasiQuotes                #-}
{-# LANGUAGE TypeFamilies               #-}
{-# LANGUAGE MultiParamTypeClasses      #-}
{-# LANGUAGE GADTs                      #-}
{-# LANGUAGE GeneralizedNewtypeDeriving #-}
{-# LANGUAGE RecordWildCards            #-}
{-# LANGUAGE FlexibleInstances          #-}
{-# LANGUAGE OverloadedStrings          #-}PTH.share [PTH.mkPersist PTH.sqlSettings, PTH.mkMigrate "migrateAll"] [PTH.persistLowerCase|
  User sql=users
    name Text
    email Text
    age Int
    occupation Text
    UniqueEmail email
    deriving Show Read
|]
```

Haskell 中的普通数据定义有很多相似之处。我们改变了格式，颠倒了类型和名称的顺序。但还是能看出来是怎么回事。字段名都在那里。我们仍然像在 Haskell 中一样派生基本实例。

但是我们也增加了一些新的指令。例如，我们已经说明了表名应该是什么(默认情况下应该是`user`，而不是`users`)。我们还创建了一个`UniqueEmail`约束。这告诉我们的数据库，每个用户必须有一个唯一的电子邮件。迁移将处理创建所有必要的索引以使其工作！

这个模板 Haskell 将为我们生成普通的 Haskell 数据类型。所有字段都将有前缀`user`，并且按照我们的指定，将是骆驼大小写。编译器还会为我们的类型生成某些特殊的实例。这些将使我们能够使用 Persistent 的类型安全查询函数。最后，这段代码生成了我们将在查询中用作过滤器的镜头，我们将在后面看到。

# 实体和键

Persistent 也有一个允许我们处理数据库 id 的构造。对于我们放入模式中的每个类型，我们都有一个相应的`Entity`类型。一个`Entity`引用我们数据库中的一行，它将一个数据库 ID 与对象本身相关联。数据库 ID 的类型为`SqlKey`，是`Int64`的包装器。所以下面看起来像是一个有效的实体:

```
import Database.Persist (Entity(..))sampleUser :: Entity User
sampleUser = Entity (toSqlKey 1) $ User
  { userName = “admin”
  , userEmail = “admin@test.com”
  , userAge = 23
  , userOccupation = “System Administrator”
  }
```

这个漂亮的小抽象允许我们避免用数据库 id 混淆用户类型。这允许我们的其他代码使用更纯的`User`类型。

# SqlPersistT Monad

现在我们有了模式的基础，我们如何通过 Haskell 代码与数据库进行交互呢？作为一个具体的例子，我们将访问一个 PostgresQL 数据库。这需要`SqlPersistT`单子。所有查询函数都返回这个单子中的动作。monad 转换器必须位于 monad 之上，monad 是`MonadIO`，因为我们显然需要 IO 来运行数据库查询。

如果我们试图从一个普通的`IO`函数进行数据库查询，我们首先需要的是一个`ConnectionString`。该字符串对有关数据库位置的信息进行编码。连接字符串通常有 4-5 个组件。它包含主机/IP 地址、端口、数据库用户名和数据库名称。例如，如果您在本地机器上运行 Postgres，您可能会看到这样的内容:

```
{-# LANGUAGE OverloadedStrings #-}import Database.Persist.Postgresql (ConnectionString)connString :: ConnectionString
connString = “host=127.0.0.1 port=5432 user=postgres dbname=postgres password=password”
```

现在我们有了连接字符串，我们准备调用`withPostgresqlConn`。该函数接受字符串，然后接受需要一个`backend`的函数:

```
-- Also various constraints on the monad m
withPostgresqlConn :: (IsSqlBackend backend) => ConnectionString -> (backend -> m a) -> m a
```

`IsSqlBackend`约束迫使我们使用符合 Persistent 准则的类型。单子只是 T2 的同义词。所以一般来说，我们对这个后端做的唯一一件事就是把它作为`runReaderT`的参数。一旦我们做到了这一点，我们就可以将`SqlPersistT`中的任何动作作为参数来运行。

```
import Control.Monad.Logger (runStdoutLoggingT)
import Database.Persist.Postgresql (ConnectionString, withPostgresqlConn, SqlPersistT)…runAction :: ConnectionString -> SqlPersistT a ->  IO a
runAction connectionString action = runStdoutLoggingT $ withPostgresqlConn connectionString $ \backend ->
  runReaderT action backend
```

注意，我们添加了一个对`runStdoutLoggingT`的调用，这样我们的操作就可以记录它的结果，正如 Persistent 所期望的那样。每当我们使用`withPostgresqlConn`时，这是必要的。下面是我们运行迁移功能的方式:

```
migrateDB :: IO ()
migrateDB = runAction connString (runMigration migrateAll)
```

这将创建`users`表，完全符合我们的数据定义！

# 问题

现在，让我们快速检查一下可以运行的查询类型，以此结束本文。我们可以做的第一件事是在数据库中插入一个新用户。为此，持久具有`insert`功能。当我们插入用户时，我们将得到该用户的一个密钥。下面是为我们特定的`User`类型指定的`insert`的类型签名:

```
insert :: (MonadIO m) => User -> SqlPersistT m (Key User)
```

当然我们也可以反过来做。假设我们有一个用户的密钥，我们想从数据库中获取它。我们需要`get`函数。当然，如果数据库中没有相应的用户，这可能会失败，所以我们需要一个`Maybe`。：

```
get :: (MonadIO m) => Key User -> SqlPersistT m (Maybe User)
```

我们可以对满足`PersistRecordBackend`类的任何类型使用这些函数。当我们使用模板 Haskell 方法时，这是免费的。因此，您可以对模式中的任何类型使用这些查询。

但是 SQL 允许我们做的不仅仅是用键查询。假设我们想要得到所有符合特定标准的用户。我们将使用`selectList`函数，它复制了 SQL `SELECT`命令的行为。运行选择的不同方式需要几个不同的参数。这两种列表类型看起来有点复杂，但是我们将更详细地研究它们:

```
selectList 
  :: PersistRecordBackend backend val 
  => [Filter val] 
  -> [SelectOpt val]
  -> SqlPersistT m [val]
```

和以前一样，TH 模式中的任何类型都可以满足`PersistRecordBackend`约束。所以我们知道我们的`User`型适合。让我们来看看第一个论点。它提供了不同过滤器的列表，这些过滤器将决定我们获取哪些元素。例如，假设我们想要所有年龄小于 25 岁、职业为“教师”的用户。还记得我提到的生成的透镜吗？我们将使用这些镜头创建两个不同的滤镜。

```
selectYoungTeachers :: (MonadIO m, MonadLogger m) => SqlPersistT m [User]
selectYoungTeachers = select [UserAge <. 25, UserOccupation ==. “Teacher”] []
```

我们使用`UserAge`镜头和`UserOccupation`镜头来选择要过滤的区域。我们使用“小于”运算符来表示年龄必须小于 25 岁。同样，我们使用`==.`操作符来匹配职业。然后我们提供一个`SelectOpts`的空列表。

第二个选择操作列表提供了一些我们可能在`select`语句中预期的其他特性。首先，我们可以对返回的数据进行排序。我们还将在这里使用生成的镜头。例如，`Asc UserEmail`将通过电子邮件订购我们的清单。这里是一个有序查询，我们也将自己限制为 100 个条目。下面是该查询的样子:

```
selectYoungTeachers’ :: (MonadIO m) => SqlPersistT m [User]
selectYoungTeachers’ = selectList [UserAge <=. 25, UserOccupation ==. “Teacher”] [Asc UserEmail]
```

其他类型的`SelectOpts`包括极限和偏移。例如，我们可以进一步修改该查询以排除前 5 个用户(按照电子邮件的顺序)，然后将我们的选择限制为 100:

```
selectYoungTeachers' :: (MonadIO m) => SqlPersistT m [Entity User]
selectYoungTeachers' = selectList
  [UserAge <. 25, UserOccupation ==. "Teacher"] [Asc UserEmail, OffsetBy 5, LimitTo 100]
```

这就是使查询类型安全和合理的全部内容。我们知道我们实际上是在过滤对我们类型有意义的价值观。我们不必担心错别字会在运行时破坏我们的代码。

# 结论

Persistent 为我们提供了一些与 Haskell 数据库交互的优秀工具。模板 Haskell 机制生成了许多样板代码来帮助我们。例如，我们可以迁移数据库，为 Haskell 类型创建正确的表。我们还可以执行以类型安全的方式过滤结果的查询。总而言之，这是一次奇妙的经历。

没试过哈斯克尔吗？每当您尝试运行 SQL 查询时，其他语言是否会因运行时错误而让您感到沮丧？你应该给哈斯克尔一个机会！下载我们的[入门清单](https://www.mmhaskell.com/checklist)以获得一些对您有帮助的工具！

如果你对 Haskell 有点熟悉，但不确定如何合并像 Persistent 这样的库，你应该看看我们的 [Stack 迷你课程](http://academy.mondaymorninghaskell.com/p/your-first-haskell-project)。它将带你了解使用 Stack 创建一个简单的 Haskell 程序的基本知识。