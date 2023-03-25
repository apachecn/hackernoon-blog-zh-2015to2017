# 不要只是放松；没精打采:CouchDB 的 JS 客户端，负责繁重的工作

> 原文：<https://medium.com/hackernoon/dont-just-relax-slouch-a-js-client-for-couchdb-that-does-the-heavy-lifting-d8232eba8e2c>

![](img/e53f3e53b882e8455f244ce319f3a0fd.png)

随着最近 CouchDB 2 的发布，CouchDB 生态系统火了！现在有一种开箱即用的方式来设置多主节点集群，该集群可以扩展以处理大量数据。而且，像 PouchDB 这样的工具可以让你的应用程序直接与 CouchDB 对话，让你的应用程序先离线。在应用程序的数据层使用 CouchDB 是一个激动人心的时刻！

即使您专注于前端密集型设计，也有可能必须在后端编写大量代码来与 CouchDB 实例通信，包括订阅实时更改。

当我们第一次开始开发数字投件箱和分级系统[Quizster](https://quizster.co)时，我们非常依赖令人敬畏的 [nano](https://github.com/apache/couchdb-nano) 客户端。通过大量的反复试验，我们发现许多混乱的逻辑，主要是为了处理大型数据集和瞬时数据库错误，开始使应用层变得混乱。我们还需要比 nano 更简洁的东西，更接近于原始的 CouchDB RESTful API。我们不希望必须将客户端绑定到特定的数据库，因为在实践中，这会导致大量的开销。我们还希望承诺成为客户的一部分，因为我们认为它们对于保持代码的可管理性至关重要。

于是，[没精打采](https://github.com/redgeoff/slouch)诞生了。

没精打采提供了你期望在 CouchDB 客户端中找到的所有典型特性。让我们来看看一些很酷的东西。

# 容错

如果与数据库的连接暂时中断，lost 会尝试重试您的请求。此外，它会重试 max_dbs_open 错误和其他瞬时错误，以防止应用程序在每次数据库出现问题时崩溃。

# 流迭代器

如今，处理大型数据集实际上是一种需求。当然，您可以对数据进行分页，但是 CouchDB 支持流，流迭代器使得处理这些流变得轻而易举。

StreamIterators 适用于所有能够返回大量文档的请求，即`db.changes()`、`db.view()`、`doc.all()`、`system.updates()`

StreamIterators 通过引入一种简单而强大的模式来处理承诺并减少代码。

## **示例 1 —顺序处理数据库中的所有文档**

```
slouch.doc.all('mydb', { include_docs: true })
  .each(function (item) {

    // If we return a promise then the all() iterator won't move on
    // to the next item until the promise resolves. This allows us
    // to iterate through a large number of docs without consuming a
    // lot of memory. It also allows us to control the flow of the
    // docs and process them sequentially so that we don't end up
    // thrashing the processor with too many concurrent promises.
    return Promise.resolve('foo => ' + item.foo);

  }).then(function () {

    // Done iterating through all docs

  }).catch (function (err) {

    // An error occurred

  });
```

## 示例 2 —最多同时处理 5 个文档

```
var Throttler = require('squadron').Throttler;
var throttler = new Throttler(5);

slouch.doc.all('mydb', { include_docs: true })
  .each(function (item) {

    return Promise.resolve('foo => ' + item.foo);

  }, throttler).then(function () {

    // Done iterating through all docs

  });
```

# 助手功能

CouchDB 冲突策略是一个非常强大的约定，是 CouchDB 及其离线功能的核心。然而，有时冲突对你来说并不重要，最好有一些函数来确保你的逻辑在这些冲突中保持不变。

## upsert

有时，即使存在冲突，您也只想强制创建或更新:

```
slouch.doc.upsert('mydb', { _id: '1', foo: 'bar' });
```

## getMergeUpsert

getMergeUpsert 允许您进行不考虑冲突的部分更新:

```
// Create a doc
slouch.doc.create('mydb', { _id: '1', foo: 'bar' })
  .then(function (doc) { // Add the `yar` attr to the doc and ignore any conflicts
    return slouch.doc.getMergeUpsert('mydb',
      { _id: '1', yar: 'nar' }); });
```

# 无精打采

这仅仅是你能对懒散的人做的事情的皮毛。我们发现[懒散](https://github.com/redgeoff/slouch)在 [Quizster](https://quizster.co) 的生产中工作得非常好，使用它极大地简化了我们的应用层，因为许多样板代码现在在[懒散](https://github.com/redgeoff/slouch)层。

我们的下一步是使用[没精打采](https://github.com/redgeoff/slouch)来开源我们的 [Spiegel](https://github.com/redgeoff/spiegel) 层，以便更容易实现可伸缩复制和更改 CouchDB 监听。

# 关于作者

杰夫·考克斯是数字投件箱和评分系统 Quizster 的联合创始人。 [Quizster，](https://quizster.co)使用完整的 JS 栈，在数据层运行 CouchDB 和 PouchDB。