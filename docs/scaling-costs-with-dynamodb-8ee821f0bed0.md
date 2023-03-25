# 利用 DynamoDB 降低成本

> 原文：<https://medium.com/hackernoon/scaling-costs-with-dynamodb-8ee821f0bed0>

![](img/c70b64516eb6ecd73960bde75ddcddfc.png)

**原帖:**【https://blog.polymail.io/post/scaling-costs-with-dynamodb】T2

随着公司的成长，积累的数据越来越多。使用电子邮件既令人兴奋又令人恐惧的一点是，用户一开始就带着大量的数据集进来。早期，我们知道我们需要一个[数据库](https://hackernoon.com/tagged/database)，让我们能够快速扩展。

一般来说，公司有两种做法。

# 垂直缩放

只需升级到具有更多 CPU、更多 RAM 和更多存储的更大实例。你所要做的就是多付钱。💸 💸与普遍的看法相反，这是一种可行的短期方法，因为它几乎不需要工程投资。

# 水平缩放

或者，您可以将数据库拆分到多个更小、更便宜的实例(或机器)上。过去十年中，开源和 NoSQL 技术的兴起使得这一点变得更加可行。

更进一步来说，有几种方法可以进行水平缩放，或者 [**分片**](http://searchcloudcomputing.techtarget.com/definition/sharding) 。

**您可以将不同的表放在不同的实例上。**

![](img/2e0218aea7ef9232a6904aa67e91c927.png)

这是最简单的选择。它还具有[隔板模式](https://docs.microsoft.com/en-us/azure/architecture/patterns/bulkhead)的额外好处，这意味着您可以将关键表(如 users 表)与不太关键的服务(如 notifications 表)隔离开来。只要你不需要连接，你就是可靠的！但是这对我们不起作用，因为我们试图共享一个单线程表。

**你可以做基于远程的分片。**

![](img/516552703fa004a026b05e5de972ac52.png)

在实践中，这是非常困难和混乱的。您需要为您的范围选择合适的大小，适当地处理键的分配以确保没有实例过载，并在删除一个项目时处理键的重新分配。

**你可以做基于哈希的分片。**

![](img/6ab883b709b3b7c94c9867d7d6ed0169.png)

这比基于范围的分片要简单一些，因为哈希函数会为您处理平衡，只要您选择一个合适的键以统一的方式分布您的数据集。例如，如果您的每个用户拥有大致相同的数据量，那么用户 ID 可能是一个统一的键。

# 扩展实例的数量

对于前两个很容易；如果您有更多的表格或范围，只需将它们放在新的机器上。

对于基于散列的分片，当您启动一个新实例时，您必须重新平衡集群，因为在引入新实例后，可能最初映射到一个实例的键可能已经改变。当然，我们是工程师。我们很聪明。我们可以使用[一致散列](https://akshatm.svbtle.com/consistent-hash-rings-theory-and-implementation)来减少这种重新平衡工作。

# 其他因素

可伸缩性只是数据库的一个方面。但这并没有停止。 ***可用性*** 怎么样？**冗余 怎么样？ ***安全*** 怎么样？这些都是基础设施工程师不得不思考的现实问题。**

# 托管服务

为了解决这些问题，我们将目光转向托管数据库服务，如 AWS 的 DynamoDB 和 GCP 的 Spanner。查看我们下面的帖子，了解更多关于利用这些服务来处理我们用户产生的数千万封电子邮件的信息，以及我们的真实体验！

[](https://blog.polymail.io/post/scaling-costs-with-dynamodb) [## 利用 DynamoDB 降低成本

### 一个关于我们如何利用托管数据库服务来解决可扩展性问题的故事

blog.polymail.io](https://blog.polymail.io/post/scaling-costs-with-dynamodb)