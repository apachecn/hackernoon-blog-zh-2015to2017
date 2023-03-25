# Swift 的意外 SQLite

> 原文：<https://medium.com/hackernoon/unexpected-sqlite-with-swift-ddc6343bcbfc>

有一个我几乎每天都喜欢的 Swift 库，因为我编写的许多应用程序都使用数据库进行本地存储。 [GRDB.swift](https://github.com/groue/GRDB.swift) 将所有赌注压在了 [SQLite](https://hackernoon.com/tagged/sqlite) 上，这是一个既非常有名，又不太知名的库。

SQLite 被设计成嵌入式的，并且访问本地数据库。这使它有别于客户机-服务器数据库，并带来了许多独特的特性。不幸的是，很少有开发人员使用它们，假设他们甚至知道它们的存在。这是因为除非你用 c 编程，否则这些特性通常是不可用的。

关于数据库库也有很强的文化假设，从低级驱动程序到成熟的 ORM，中间是 SQL 查询构建器。打破这些假设需要大量的交流和教学，例如由生动的社区和非常活跃的公司支持的数据库引擎。

本文的其余部分列出了 SQLite 和 GRDB 的一些意想不到的特性。

## WAL 模式，又名高效多线程

WAL 表示[提前写日志](https://sqlite.org/wal.html)。这是一种允许 SQLite 支持并发数据库读取器和写入器的技术，并且极大地改进了多线程应用程序。例如，当 UI 线程从数据库加载数据时，它永远不会被阻塞。

GRDB 通过 [DatabasePool](https://github.com/groue/GRDB.swift/blob/master/README.md#database-pools) 类支持 WAL 模式，并提供了一个[并发指南](https://github.com/groue/GRDB.swift/blob/master/README.md#concurrency)，涵盖了高效多线程应用的最佳实践。

## 活性 SQLite

SQLite 提供了[数据变更通知回调](https://sqlite.org/c3ref/update_hook.html)和[编译时授权回调](https://sqlite.org/c3ref/set_authorizer.html)。第一个通知修改的行，当后者告诉 SQL 语句中涉及哪些表和列时。

当您将它们结合起来时，您就能够判断一个 SQL 语句是否有机会影响另一个查询的结果。这是通过 [GRDB 的 ValueObservation](https://github.com/groue/GRDB.swift/blob/master/README.md#valueobservation) 进行数据库观察的诀窍，内置对 Combine 和 RxSwift 的支持。

## 面向协议的 ORM

一个 ORM ( [对象关系映射](https://en.wikipedia.org/wiki/Object-relational_mapping))将原始数据库行映射到名为 records 的简单对象。

ORM 中常见的特性有获取和持久化方法、关系、延迟加载、记录自动更新和记录唯一化。最后三个是典型的 ORM，由*管理*记录，并假装它们直接映射数据库行。这种错觉通常会为所有记录类型引入一个基类，并在应用程序线程之间设置栅栏，以避免共享数据的突变。

由 Swift 和 Rust 等语言引入的对不变性和[值类型](https://developer.apple.com/swift/blog/?id=10)的关注带来了一种编写 ORM 的新方法:现在可以放弃公共基类，跨线程共享记录，并使用数据库作为整个应用程序的唯一来源。GRDB [唱片](https://github.com/groue/GRDB.swift/blob/master/README.md#records)和 Rust 的[柴油](https://diesel.rs)建立在这些新的基础上。

## 有用的错误

太多的数据库库假装数据库错误没有发生，很容易忽略它们，或者发出难以理解的错误消息。GRBD 使用全范围的 [Swift 错误](https://github.com/apple/swift/blob/master/docs/ErrorHandlingRationale.rst)，并提供尽可能详细的[错误](https://github.com/groue/GRDB.swift/blob/master/README.md#error-handling)。

## 轻松的 SQL

GRDB 欢迎不熟悉 SQL 和 SQLite 的开发人员:它的[查询接口](https://github.com/groue/GRDB.swift/blob/master/README.md#the-query-interface)使得不用写一行 SQL 就能访问数据库成为可能。

同时，没有一个查询生成器能够生成所有可能的查询，偶尔出现的复杂查询在编写为普通 SQL 时可能更容易阅读。这就是为什么 GRDB 确保你永远不会为你的 SQL 技能买单。

例如，您可以从 SQL 以及 Swift 请求中加载记录:

```
// Two one liners that load the same array of Person:
try Person.filter(emailColumn != nil).fetchAll(db)
try Person.fetchAll(db, """
    SELECT * FROM persons
    WHERE email IS NOT NULL
    """)
```

[反应式延伸](http://github.com/RxSwiftCommunity/RxGRDB)同样用途广泛:

```
let request = SQLRequest("SELECT * FROM persons")
request.rx.changes(in: dbPool)
  .subscribe(onNext: { db: Database in
    print("Persons table has changed.")
  })

try dbPool.write { db in
  try Person(name: "Arthur").insert(db)
  try Person(name: "Barbara").insert(db)
} // Prints "Persons table has changed."
```

本着同样的精神，GRDB 支持所有种类的主键、惟一键和外键:您可以自由地设计关系模式的完整性，并在列不符合要求时摆脱沉闷的`id`列。

SQLite 和 GRDB 是两个提供简单和高级工具来帮助开发应用程序的库。查看[文档](https://github.com/groue/GRDB.swift/blob/master/README.md#documentation),获得更多意想不到的好处，如高级 unicode 支持、记录更改跟踪、内存受限设备的维护、定制的 SQLite 构建、定制的全文索引…

[https://github.com/groue/GRDB.swift](https://github.com/groue/GRDB.swift)

这篇文章是 GRDB 故事的一部分:

1.  [取值](/@gwendal.roue/grdb-stories-993b9d016867)
2.  [内存消耗](/@gwendal.roue/grdb-stories-cae49d47dc21)
3.  [从调试器查询数据库](/@gwendal.roue/grdb-stories-592d03b42384)
4.  [主键自由度](/@gwendal.roue/grdb-stories-8c37a03ef72d)
5.  [FetchedRecordsController](/@gwendal.roue/grdb-stories-1da44bdb53ab)
6.  [对象和数据库之间的松散耦合](/@gwendal.roue/grdb-stories-9bfcfb8b6a88)
7.  [处理 SQLite 并发的四种不同方式](/@gwendal.roue/four-different-ways-to-handle-sqlite-concurrency-db3bcc74d00e)
8.  [如何用 GRDB.swift 构建 iOS 应用](/@gwendal.roue/how-to-build-an-ios-application-with-sqlite-and-grdb-swift-d023a06c29b3)
9.  **使用 Swift 的意外 SQLite**

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿美族家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！