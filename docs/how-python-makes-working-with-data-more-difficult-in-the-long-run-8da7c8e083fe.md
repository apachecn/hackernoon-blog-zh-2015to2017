# 从长远来看，Python 如何使数据处理变得更加困难

> 原文：<https://medium.com/hackernoon/how-python-makes-working-with-data-more-difficult-in-the-long-run-8da7c8e083fe>

在我们开始之前，让我们先弄清楚术语。当我在[软件开发](https://hackernoon.com/tagged/software-development)的上下文中提到“处理数据”时，我可能指的是以下两种情况之一:

1.  *交互地*处理数据，可能使用 Jupyter (née IPython)或实时解释器
2.  *编写、测试、阅读、审查*和*维护主要操作数据的*程序

**简而言之:Python 在交互式数据分析方面很棒，但在编写处理复杂数据结构的长期程序方面却很糟糕。**

第二个定义可能过于宽泛，但我会马上澄清。在此之前，让我第一个说 [Python](https://hackernoon.com/tagged/python) 是一种**不可思议的**语言，用于交互式地处理或*探索*数据。围绕数据操作、可视化和数据科学涌现出来的第三方包和工具的生态系统已经相当引人注目。

如果处理交互数据是你的专长，Python 应该是你的铁锤。

但是第二种解释呢？实际上，它可以被认为是第一个的逻辑延伸。假设您正在编写一个程序，在数据库中查询一个搜索词，进行一些情感分析，然后用 JSON 返回结果。与数据库结果、您的情感分析库返回的结果以及您生成的 JSON 交互工作是自然的第一步。你仍然处于“探索模式”。还没有真正写程序，只是看看数据是什么样子，以及你需要如何操作它。

一旦你对每一步的数据“形状”有了“感觉”，你就可以开始写程序了。在实现程序逻辑时，您可能会回头参考在探索过程中创建的输出示例。特别是对于深度嵌套的数据结构(我正在看你，“每个人对 JSON 的滥用……”)，在你的头脑中保持数据在每个阶段的“形状”通常太难了。

但是 Python 使得处理数据变得容易，所以你的程序很快就完成了。它是有效的，有很好的文档记录，甚至有 100%的测试覆盖率。如果你永远不需要回到这个代码，万岁！你的任务完成了。

# 动态类型是万恶之源(j/k…有点…)

Python 的特性使得你的程序很容易编写，但是同样的特性也会使你的程序难以审查、阅读和(最重要的)维护。Python 的动态类型系统意味着，在大多数情况下，当数据在系统中移动时，您不必枚举定义数据的完整的字段、类型和值约束。你可以把它全部塞进一个`dict`！异类价值观 FTW！

在 C 或 Go 这样的静态类型语言中，上面的任务会更加费力和耗时。例如，在 Go 中，为了解析并返回来自某个 web API 的 JSON 响应，首先需要创建一个`struct`，它的字段和字段类型*与响应的结构*完全匹配。下面是如何准备使用来自`[etcd](https://github.com/coreos/etcd)`的 JSON 响应(取自他们的客户库):

```
type Response struct {
 // Action is the name of the operation that occurred. Possible values
 // include get, set, delete, update, create, compareAndSwap,
 // compareAndDelete and expire.
 Action string `json:"action"`// Node represents the state of the relevant etcd Node.
 Node *Node `json:"node"`// PrevNode represents the previous state of the Node. PrevNode is non-nil
 // only if the Node existed before the action occurred and the action
 // caused a change to the Node.
 PrevNode *Node `json:"prevNode"`// Index holds the cluster-level index at the time the Response was generated.
 // This index is not tied to the Node(s) contained in this Response.
 Index uint64 `json:"-"`
}type Node struct {
 // Key represents the unique location of this Node (e.g. "/foo/bar").
 Key string `json:"key"`// Dir reports whether node describes a directory.
 Dir bool `json:"dir,omitempty"`// Value is the current data stored on this Node. If this Node
 // is a directory, Value will be empty.
 Value string `json:"value"`// Nodes holds the children of this Node, only if this Node is a directory.
 // This slice of will be arbitrarily deep (children, grandchildren, great-
 // grandchildren, etc.) if a recursive Get or Watch request were made.
 Nodes Nodes `json:"nodes"`// CreatedIndex is the etcd index at-which this Node was created.
 CreatedIndex uint64 `json:"createdIndex"`// ModifiedIndex is the etcd index at-which this Node was last modified.
 ModifiedIndex uint64 `json:"modifiedIndex"`// Expiration is the server side expiration time of the key.
 Expiration *time.Time `json:"expiration,omitempty"`// TTL is the time to live of the key in second.
 TTL int64 `json:"ttl,omitempty"`
}
```

每个字段后的“`json:...`”部分描述了从 JSON 消息中封送对象时该字段的名称。注意，因为`Response`包含一个嵌套对象(`Node`)，我们也必须完全定义这个嵌套对象。

*注意:公平地说，这里有* ***和*** *一些快捷方式，人们可能会在 Go 中采用它们来减少对上述部分的需求，但它们很少被采用(并且有很好的理由)。*

在 Python 中，你可能会说:

```
result = make_etcd_call("some", "arguments", "here")
```

如果你想知道这个`node`是不是一个目录，你可以敲出来:

```
if result.json()['node']['dir']:
    # make magic happen...
```

Python 版本比 Go 版本代码更少，编写时间更短。

# “我看不出有什么问题”

Python 版本比较好吧？让我们考虑一下“好代码”的两种定义，这样我们就可以清楚我们所说的更好是什么意思。

1.  短小、简洁且可以快速编写的代码
2.  可维护的代码

如果我们使用第一个定义，Python 版本“更好”。如果我们用第二个，**那就糟糕多了。**Go 版本，尽管包含了一大堆样板式的定义代码，*明确了我们可以在* `*result*`中预期的数据的确切结构。

> 老板:“你能告诉我关于 Python 版本的什么，只看我们上面的代码？”
> 
> 我:“嗯，它是 JSON，有一个‘node’对象，这个对象可能有一个‘dir’字段。”
> 
> 老板:`dir`里是什么类型的值？它是布尔值、字符串还是嵌套对象？”
> 
> 我:“呃，我不知道。不过，这是真的！”
> 
> 老板:“Python 里的其他东西也是。是否保证`dir`是响应中`node`对象的一部分？”
> 
> 我:“呃....”

我已经达到了描述一部分代码的“3-Uh”限制。如果你参考 Go 版本，你可以回答这些问题，相比之下听起来像一个该死的天才。

但是这些正是你的同事在代码评审中应该问的问题。Go 版的问题答案不言而喻。Python 版本的答案就没那么多了…

# 做出改变

当我们需要对 Python 版本进行更改时会发生什么？也许我们想说“只有`make magic happen`的目录才被创建，而不是对每个响应都有一个目录？”

在 Go 版本中，如何做到这一点非常清楚。与 Python 版本相比，Go 版本就像是`etcd` `Response`的亚历山大图书馆。对于 Python 版本来说，*我们没有任何本地参考来找出* `*result*` *的结构和我们需要做出的改变。我们必须去查阅 HTTP API 文档。让我们希望:*

*   它存在
*   它保养得很好
*   管道没有堵塞

这是一个*非常*简单的变化，我们正在谈论一个*非常*简单的 JSON 对象。我可以讲一些恐怖的故事，当你在 Elasticsearch JSON 回复中陷入困境时会发生什么…(剧透警告:`response['hits_']['hits_']['hits_']...`)。

然而，乐趣并不仅仅停留在*改变*代码上。请记住，我们是专业人士，所以我们所有的代码都经过了同行评审和单元测试。在正确更新了代码*之后，我们仍然几乎不能对它进行推理。突然间，我们又回到了我和老板之间的对话，我说了很多“嗯”，他想知道为什么他没有去做木工。*

# *大家都慌了！*

*我已经描绘了一幅使用 Python 以可维护的方式操作复杂(甚至不那么复杂)的数据结构的暗淡画面。然而，事实上，这是大多数动态语言共有的缺点。在本文的后半部分，我将描述不同的人/公司正在做的事情，从简单的事情，如走向“编辑器中的动态数据”[一直到 Dropboxian 的“类型注释所有的东西”](https://www.dropbox.com/s/efatwr0pozsargb/PyCon%20mypy%20talk%202016.pdf?dl=0)。简而言之，在这个领域有很多有趣的工作在进行，并且*很多人*都参与其中(注意 Dropbox 面板中第二个演讲者的名字)。*

**原载于 2016 年 11 月 13 日*[*jeffknupp.com*](https://jeffknupp.com/blog/2016/11/13/how-python-makes-working-with-data-more-difficult-in-the-long-run/)*。**

> *[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)的机会。*
> 
> *要了解更多信息，请阅读我们的“关于”页面、[喜欢/在脸书上给我们发消息](http://bit.ly/HackernoonFB)，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)*
> 
> *如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！*