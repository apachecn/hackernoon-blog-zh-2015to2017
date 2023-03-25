# 裂化柴油

> 原文：<https://medium.com/hackernoon/grokking-diesel-652cb8886a63>

Diesel ( [http://diesel.rs](http://diesel.rs) )是用 Rust 编写的 ORM(对象关系映射器)和查询生成器。它支持 Postgresql、Mysql 和 SQLite。它利用 Rust 的自定义派生功能来生成您需要的所有代码，以便在与数据库交互时获得 Rust 类型系统的所有功能。这意味着您获得了代码的编译时验证，从而消除了可能的运行时错误。

Diesel 的功能非常强大，但是仅仅按照例子来做可能还是会让你挠头，问一些问题，比如“这些类型是从哪里来的？”、“我应该在这里添加什么”、“这是如何工作的？”。本文旨在从一个中级 Rust 开发人员的角度，对这个主题进行一点阐释。

有许多主题，这篇文章将不会涵盖，但希望您将能够“grok”足够的柴油，以便其余的变得显而易见，并且您理解它是如何工作的，在某种程度上，您在这个机箱中探索的新领域也变得更容易理解。

# 第一步

你应该先浏览一下 Diesel 官方网站上的[入门指南](http://diesel.rs/guides/getting-started)。本文主要关注 Postgresql，到目前为止，让 Postgresql 服务器为您的测试运行的最简单的方法是使用 Docker([www.docker.com](http://www.docker.com/))...下面的一行程序应该可以解决这个问题:

```
docker run --rm --detach --name postgres --env POSTGRES_USER=username --env POSTGRES_PASSWORD=password --publish 127.0.0.1:5432:5432 postgres
```

> *这是*而不是*用于生产，当容器停止时，整个容器将被移除(使用* `*docker stop postgres*` *),但它对于入门指南来说非常方便。*

# 关于本文其余部分的格式

本文假设您已经阅读了入门指南；它故意掩盖了一整套特征。这是有意的，这样做是为了让文章尽可能简洁。

# 核心组件和社区

其核心柴油由 4 个主要成分组成:

到目前为止，您应该已经了解了`diesel` crate、`diesel_codegen` crate(代码生成器)和`diesel` cli。

最后一个主要组件，即“秘密武器”或“非官方指南”(据我所知)，是官方回购中的测试套件([https://github . com/diesel-RS/diesel/tree/master/diesel _ tests](https://github.com/diesel-rs/diesel/tree/master/diesel_tests))。每当我陷入困境时，它就充当我的向导，尽管随着项目的继续发展，它肯定会被文档所取代。

# 环境

当我开始使用 Diesel 时，我被`schema.rs`中的`infer_schema!`宏弄糊涂了(下面会详细介绍)，它反过来使用了`"dotenv:DATABASE_URL"`。这个`dotenv`箱子只是一个方便的库，允许你把你的`DATABASE_URL`放在一个隐藏的环境文件中，ala `.env` (dot env，明白吗？).

接下来，你也可以在你的环境中指定`DATABASE_URL`，当你在一个 docker 容器 ala [12 因子应用](https://12factor.net/)中运行你的使用 Diesel 的 Rust 二进制时，这特别方便。

Diesel cli 工具还读取`.env`文件，如果该文件不可用，您需要在环境中定义`DATABASE_URL`或者将`--database-url`参数传递给它。

> *一定要查看* `*examples/sqlite/getting_started_step_3/README.md*` *文件，以便了解如何为 SQLite 配置* `*DATABASE_URL*` *(它不使用 URI 格式)。*

# 基本流程

创建使用 Diesel 构建的应用程序的核心工作流程可以分解如下:

# 设计一个模式

对于经验丰富的 SQL 老手来说，这是非常明显的，幸运的是，对于我们其余的人来说,`diesel` cli 的`migration`子命令允许我们轻松地迭代我们的设计，甚至随着时间的推移不断发展。然而，预先清楚地知道您希望数据库是什么样子是非常有用的，应该注意的是，Diesel 只适用于有主键的表。

# 创建迁移

遵循《入门指南》中的模式，注意您可以随时使用`diesel migration generate`子命令添加更多的迁移。每当您运行`diesel migration run`时，迁移将按顺序运行。您可以通过发出`diesel migration redo`命令重新运行上一次迁移，如果您确实遇到了问题，您可以运行下面的命令，但是请不要在生产数据库上这样做，`diesel database reset`。

设计表格时，你应该使用复数形式的表格名称。例如，Diesel 将采用模型`User`并搜索表`users`。您可以定义自定义的表名，但是了解 Diesel 开发人员的假设可能会避免一些混乱。

Diesel 将获取`PascalCase`Rust struct(它可能描述单个对象，或者数据库表中的行),并将它们翻译成`snake_case`表名，并在末尾添加一个`s`,使其“多元化”。例如，`AFancyNamedObject`将被假设映射到一个名为`a_fancy_named_objects`的表。

# 从数据库中推断模式

Diesel 能够检查您的实际数据库，并推断出 Rust 中使用的模式，这将用于创建必要的 DSL(领域特定语言),允许您以安全、快速和强类型的方式与数据库进行交互。

入门指南和示例使用了`infer_schema!`宏。使用这个宏的主要缺点是，首先，你*不知道 Diesel 实际上是如何解释你的数据库类型的(这对你的模型很重要，这将在后面揭示),其次，它在编译时需要一个自举的数据库实例，当作为管道的一部分进行编译时，可能会有点痛苦，因为你的数据库和编译器工具链不能互相使用。*

建议您使用`diesel print-schema`子命令，简单地将推断的模式复制并粘贴到项目中的一个文件中(在《入门指南》中，这是`schema.rs`，但也可以很容易地粘贴到`lib.rs`或`main.rs`)。

您将看到一个类似于以下内容的`table!`宏:

```
table! {
    users {
        id -> Integer,
        name -> VarChar,
    }
}
```

您想要获取数据类型(在本例中是`Integer`和`VarChar`)并立即运行到`docs.rs/diesel`并将其插入到文档搜索栏中。这将直接带您到`diesel::types::Foo`(也探索`diesel::types`)，并允许您检查每种类型已实现的`ToSql`和`FromSql`特征。举个例子，`Integer`映射到 Rust 中的`i32`。这在实现模型或处理编译时错误时非常有用。

# 创建模型

与模式一样，您不需要*将您的模型放在`models.rs`文件中。建议您使用 Rust modules 系统来拆分您的模型，类似下面的内容可能会有所帮助，尤其是在处理大量模型时。*

```
models/
  users/
    mod.rs
  posts/
    mod.rs
```

> *我最初挣扎着把用来驱动柴油的“魔法”从惯用的铁锈中分裂出来。原来，一旦你知道了它是如何构造的，以及现在如何处理生成的代码，Diesel 就只是一个熟悉的老东西。*

下面是一个用 Rust struct 表示的示例模型，直接摘自入门指南:

```
#[derive(Queryable)]
pub struct Post {
    pub id: i32,
    pub title: String,
    pub body: String,
    pub published: bool,
}
```

模型是普通的 Rust 结构，*似乎*映射到你的表。这通常是简单模型的情况，然而，注意到`Queryable`结构，顾名思义，实际上映射到您想要从 SQL 查询中获得的*结果*是非常重要的。

`Queryable` struct 是您希望从 SQL 查询中检索的单个对象或行(部分或完整)。这个`User`对象可以从`users`表中查询，或者任何返回正确类型的查询，如下面的`Queryable`部分所讨论的。

请注意，diesel 的 SQL 类型不支持 Postgresql 中的无符号整数(这是 Postgresql 的一个限制)，因此查看文档中适当的`diesel::*::types`来了解您的数据库支持什么是值得的。

# 衍生，又名“密码魔术”

Diesel 的代码生成器主要用于使用 SQL magic 嵌入您的 Rust 结构，而无需您手动编写大量功能。它还将 Rust 类型的系统变成了神奇的成分，可以用来构建快速可靠的 SQL，并传输到数据库。

为了让您的结构具有 SQL 的优点，您可以使用 Rust 的自定义派生功能，例如:

```
#[derive(Queryable)]
pub struct Post {
    pub id: i32,
    ...
```

一个更复杂的例子:

> *我喜欢在测试中偶然发现以下内容……这确实让我对作者的天才一笑置之，但也让我挠头，“这些东西到底用在哪里，它们是如何工作的？”*

```
#[derive(PartialEq, Eq, Debug, Clone, Queryable, Identifiable, Insertable, AsChangeset, Associations)]
#[table_name = "users"]
pub struct User {
    pub id: i32,
    pub name: String,
    pub hair_color: Option<String>,
}
```

首先，新手给你一个“专业建议”，你应该安装 cargo 子命令`expand`:

```
cargo install cargo-expand
```

这允许您运行下面的命令，并且实际上*看到*生成的内容(警告，本指南假设您了解 Rust，但是下面的内容可能会让即使是经验丰富的 Rust 开发人员也热泪盈眶，因此可以随意跳过它或用作睡前读物)。

```
cargo expand
```

所生成的 DSL 的全部优点显露出来了…特别有趣的是每个模型的列。解析输出是无聊的读者或喜欢挑战的读者的一项练习。

让我们更详细地看一下推导过程。

## PartialEq，Eq，调试，克隆

这些是大多数开发人员在适当的时候依赖的标准 Rust 派生。如果你喜欢使用`println!`(或者闪亮的新`eprintln!`)进行调试，你可能至少需要`Debug`。

> `*#![deny(missing_debug_implementations)]*` *是一个非常有用的编译器指令，因为它会在任何没有* `*Debug*` *实现的结构上出错，如果您正在开发一个库，这尤其有用。*

## 可查询的

大多数开发人员通常想对数据库做的第一件事是查询其中的数据。为了做到这一点，你需要将你的`struct`装饰如下:

```
#[derive(Queryable)]
struct User {
    id: i32,
    firstname: String,
    lastname: String,
    age: i32,
}
```

这将导致`diesel_codegen`生成反序列化类型为`(i32, String, String, i32)`的查询结果所需的查询 DSL，该查询结果映射到 Postgresql 中的`(Integer, Text, Text, Integer)`,更多示例见文章末尾。

> *在 SQLite 中，日期/时间类型也可以反序列化为* `*String*` *。*

使用 Diesel 时，主键列是必需的，但无论如何，这可能是一个好的实践。

如何使用 DSL 来构造查询将在本文后面讨论。

## 可插入的

除非您来自现有的系统，否则您可能也想将数据插入到数据库中。典型的“可插入”对象如下所示:

```
#[derive(Insertable)]
#[table_name="users"]
struct NewUser {
    firstname: String,
    lastname: String,
    age: i32,
}
```

请注意，我们已经删除了`id`字段，因为 SQL server 将为我们处理这个问题(对于高级用例，这可能会有所变化)。

还要注意，我们现在显式地命名该表为`users`，这对于`AsChangeset`也是需要的，并且对于`Identifiable`是推断出来的

## 可辨认的

不可避免地，您将使用 SQL 连接一次从多个表中构造结果。为了使连接成功地解析目标表中的精确对象，需要对这个`struct`进行如下注释:

```
struct User {
    id: i32,
    firstname: String,
    lastname: String,
    age: i32,
}
```

默认情况下，Diesel 将假设您的主键名为`id`，如果不是，您可以按如下方式覆盖它:

```
#[derive(Identifiable)]
#[primary_key(guid)]
struct User {
    guid: i32,
    firstname: String,
    lastname: String,
    age: i32,
}
```

## 联合

需要用您的`Identifiable`数据充实的表格需要注释如下:

```
#[derive(Associations)]
#[belongs_to(User)]
struct ActiveUsers {
    id: i32,
    user_id: i32,
    last_active: NaiveDateTime
}
```

这将允许您通过查找与`User`表中的`id`字段相对应的`user_id`字段定义的用户，将`User`数据加入到该表中。如果您的外键字段没有在模式`type_id`中指定，您需要手动映射它:

```
#[derive(Associations)]
#[belongs_to(User, foreign_key="user_lookup_key")]
struct ActiveUsers {
    id: i32,
    user_lookup_key: i32,
    last_active: NaiveDateTime,
}
```

## AsChangeSet

`#[derive(AsChangeset)]`用于更新，更多详情可在本进行中指南中找到:[https://github . com/diesel-RS/diesel/blob/master/guide _ drafts/all-about-updates . MD](https://github.com/diesel-rs/diesel/blob/master/guide_drafts/all-about-updates.md)

# 使用柴油

此时，您已经准备好用 Diesel 做一些事情，可能会插入一些数据并进行一些查询。《入门指南》中有多个示例介绍了这一点。然而，有些东西需要拆开包装，希望能让灯泡继续工作。

首先，在这一点上，强调你正在进入正常的生锈世界是非常重要的。我这么说的意思是，Diesel 的代码和魔法部分现在退居二线了。

这意味着当您查看示例代码时，不会有更多的疏忽。你只需要相信编译器，像平常一样花时间解开语句和表达式。你会发现你正在调用普通的方法，传递普通的数据结构(或者对它们的引用),你可以像对待其他 Rust 代码一样`println!`、调试、步进、重新排序和组织。不幸的是，我花了很长时间才理解这一点，但这是一个重要的见解，可以让你无所畏惧地编码。

# 连接到数据库

`postgresql`示例使用函数`pub fn establish_connection() -> PgConnection`将连接包装成一个方便的函数调用，以便在示例代码的其余部分重用。

`PgConnection`为你封装了`posgresql`的句柄，保留这个对象或者按需重新创建它。需要时，查看`r2d2`创建一个数据库连接池。

使用 Diesel 执行的所有操作都取决于可用的连接对象。

# 插入数据

本指南在`src/lib.rs`中指定了以下功能。让我们一步一步来:

```
pub fn create_post(conn: &PgConnection, title: &str, body: &str) -> Post {
    use schema::posts;
    let new_post = NewPost {
        title: title,
        body: body,
    }; diesel::insert(&new_post).into(posts::table)
        .get_result(conn)
        .expect("Error saving new post")
}
```

首先注意连接`&PgConnection`的引用。正如对`src/bin/`中的其他一些文件所做的那样，一个新的`PgConnection`也可以通过调用如下内容来实例化:

```
let conn = establish_connection();
```

下一行，`use schema::posts;`，困扰了我很长时间，因为这是使用生成的代码。在`diesel::insert`表达式中，我们看到了`posts::table`的用法。

此时，获取`cargo expand`的输出并在某种 IDE 中查看它可能是个好主意。尝试以下方法:

```
# Check out a copy of Diesel     git clone [https://github.com/diesel-rs/diesel.git](https://github.com/diesel-rs/diesel.git)
cd diesel/examples/postgres/getting_started_step_3/# Build the example (assuming your postgres instance is ready
# and running; see the docker hint above)echo DATABASE_URL=postgres://username:password@localhost/diesel_demo > .env
diesel setup
cargo build# Expand the code (assuming cargo-expand is installed)cargo expand --lib > expanded.rs
```

如果您在输出下搜索`mod schema`，您还会看到`mod posts`，在那里您会找到一个`table`结构(空的，但是有一堆特征被导入)。

接下来我们有一个新的物体(T4)，叫做 T5。

最后我们有实际的柴油方法`insert`。如果您查阅文档，您会看到 Diesel 模块有 5 个功能，其核心是`insert`、`delete`、`insert_default_values`、`select`和`update`。

如果您查看`insert`函数，您会看到它接受`records: T`(在本例中是我们的新用户，但也可以是`Vec<T>`)并返回一个`IncompleteInsertStatement`对象，该对象有一个名为`into()`的方法，该方法接受您的`table`结构。您也可以调用`into(::schema::posts::table)`并避免使用语句。

在这种情况下，`schema`是*的名称，也就是您的*模块的名称，因为模式是在`schema.rs`文件中生成的。

# 查询数据

同样，我们将在指南中引用一个函数，在本例中是`src/bin/show_posts.rs`文件:

```
extern crate diesel_demo;
extern crate diesel;
use self::diesel_demo::*;
use self::diesel_demo::models::*;
use self::diesel::prelude::*;fn main() { use diesel_demo::schema::posts::dsl::*;
    let connection = establish_connection();
    let results = posts.filter(published.eq(true))
        .limit(5)
        .load::<Post>(&connection)
        .expect("Error loading posts"); println!("Displaying {} posts", results.len()); for post in results {
        println!("{}", post.title);
        println!("----------\n");
        println!("{}", post.body);
    }
}
```

从头开始，首先我们导入自己的板条箱(示例`Cargo.toml`将`crate`名称指定为`diesel_demo`)。我们进口柴油箱。

`use self::diesel_demo::*;`让我们访问`establish_connection()`函数，`use self::diesel_demo::models::*;`让我们访问我们在`models.rs`文件中定义的实际结构，在本例中是`Post`结构。

`use self::diesel::prelude::*;`是一个必要的导入，将一大堆柴油特性和类型纳入范围。这是 Diesel 工作所必需的，超出了本文的讨论范围。

在`main()`函数中，我们再次遇到了一些魔法的使用，特别是`use diesel_demo::schema::posts::dsl::*;`行。当我第一次开始使用 Diesel 时，我被上面插入代码时使用的`schema::tablename::*`导入和这里使用的`schema::posts::dsl::*` dsl 导入的区别难住了。

看一下`cargo expand`的输出，可以得到一些澄清:

```
pub mod dsl {
    pub use super::columns::*;
    pub use super::table as posts;
}
```

简而言之，`schema::posts::dsl::*`将您的表的`columns`带入范围。为您生成的每个列类型都有一个在其上实现的`expression_methods`集合。换句话说，`dsl`(领域特定语言)允许我们在表名中使用`columns`(由`schema.rs`生成的代码定义)并对其应用逻辑，以便构造我们的 SQL 查询。(见[http://docs . diesel . RS/diesel/expression _ methods/global _ expression _ methods/trait。expression methods . html # method . desc](http://docs.diesel.rs/diesel/expression_methods/global_expression_methods/trait.ExpressionMethods.html#method.desc)

如果您发现了将`table`方便地导入到 dsl 模块中(我认为这是为了方便)，那么您将获得额外的加分。

## 构建查询

在 SQL 中，我们构造一个 select 表达式来从我们的数据库中返回值，在 Diesel 中，我们使用 Rust 的类型系统来构造类型检查的、安全的版本。这对于任何曾经与 SQL 查询的脆弱性及其隐含的安全风险作斗争的人来说都是非常好的…只有有效的 SQL 才能成功编译。

下一个表达式`posts.filter(published.eq(true))`反映了我们想要在`posts`表上运行`filter`方法(也可以通过`use schema::posts::dsl::*`语句方便地导入到我们的上下文中)。`filter`将构建的过滤器作为其输入。通过组合列及其表达式方法来构造过滤器。

要检查结果，您可以将相关代码重写为:

```
use diesel::debug_sql;let posts_with_sql = posts.filter(published.eq(true))
    .limit(5);println!("SelectStatement: {:#?}", posts_with_sql);let results = posts_with_sql
    .load::<Post>(&connection)
    .expect("Error loading posts");
```

如果您查看终端的输出，您会看到返回了一个`SelectStatement`对象。您还可以使用`println!("SQL: {}", debug_sql!(posts_with_sql));`查看将要生成的 SQL(使用`#[macro_use] extern crate diesel;`导入 diesel)。

本节的主要内容是，首先使用从`dsl`模块导入的`table`和`columns`构建相关的 SQL 查询，这可以通过`println!`和`debug_sql`进行自省。

您应该尽可能地熟悉您可以在列上调用的不同的`expression_methods`——它将帮助您顺利地构建您想要在应用程序中使用的 SQL 查询。

# 获得结果

本文中我们要处理的最后一项实际上是读取您的结果。在`show_posts.rs`二进制中，这是通过以下代码行实现的:

```
let results = ...omitted...
    .load::<Post>(&connection)
    .expect("Error loading posts");
```

可以看到，我们正在调用我们前面检查过的`SelectStatement`结构的`.load()`方法。`.load()`方法是通用的，所以我们需要给编译器一些提示，告诉它我们想返回什么类型。加载函数的参数是对由`establish_connection`返回的`connection`对象的引用(或借用)。

`load`返回一个结果对象，在这个可执行文件中，我们通过用`expect()`方法展开结果来处理它。然后，如果成功，我们将结果传递给`results`绑定。

参见文档中的`diesel::prelude::LoadDsl`或`diesel::prelude::FirstDsl`了解`load`的一些替代方案。正如将要看到的，这些方法返回一个`QueryResult`，它只是一个`Result<T, Error>;`类型的别名。

在刚才提到的所有方法中，要么返回单个对象(`QueryResult<T>`)，要么返回对象的向量(`QueryResult<Vec<T>>`)。换句话说，数据库表中所选列的一行或多行。

## 与结果角力

如果我们想得到所有的结果，我们可以使用代码:

```
let results :Vec<Post> = posts
    .load(&connection)
    .expect("Error loading posts");
```

`load`和`get_results`是等价的。也就是说，它们都返回一个`Vec<T>`结果(`QueryResult`)。注意，代码被重新格式化，以说明在绑定`let results: Vec<Post> = ...`中给编译器的返回类型提示。

最后，有时你会使用一个`select`方法或`join`方法(本文中没有举例说明，但是请查看上面提到的‘非官方指南’又名`diesel_tests`),它们会返回不直接映射到你的模型中的字段的行。在这种情况下，使用`tuple`从你的`load`方法中收集你的结果。这可能看起来像:

```
let results: Vec<(i32, String, String, bool)>= posts
    .load(&connection)
    .expect("Error loading posts");
```

上面，我们将`Post`型号或`struct`表示为其组成部件的`tuple`。

# 我们没有涵盖的内容以及下一步要做什么

本文中没有涉及到很多内容，尽管就其本身而言，这是一个相当大的信息量。如前所述，目标是解释足够多的 Diesel 及其结构，以便感兴趣的开发人员可以“搜索”或理解它，然后自助。

我希望你能走到这一步，并享受这段旅程，请发送反馈并享受 Rust 和 Diesel。

特别感谢《Diesel》的作者 Sean Griffin，他对本文进行了事实核查。