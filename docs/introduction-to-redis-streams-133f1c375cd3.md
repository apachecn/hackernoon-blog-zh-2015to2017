# 卡夫卡和雷迪斯溪流

> 原文：<https://medium.com/hackernoon/introduction-to-redis-streams-133f1c375cd3>

先说队列设计。

我们有一本很长的书，我们想让很多人读。有些人可以在午餐时间阅读，有些人在周一晚上阅读，有些人把它带回家过周末。这本书太长了，以至于在任何时候，我们都有数百人在阅读它。

我们书的读者需要知道他们在书里读到哪里了，所以他们通过在书里放一个书签来记录他们的位置。有些读者读得很慢，把书签放在靠近开头的地方。其他读者半途而废，把他们的放在中间，再也不回来了。

更糟糕的是，我们**每天都在给这本书增加页面**。没有人能真正完成这本书。

最终我们的书塞满了书签，直到有一天它变得太重而无法携带，没有人能再阅读它。

一个非常聪明的人于是决定不允许读者在书里面放书签，而是必须由**在他们的日记上写下他们读到的第**页。

这是阿帕奇卡夫卡的设计，而且是非常有韧性的设计。读者往往不是负责任的公民，往往不会清理自己的垃圾，这本书可能是我们公司发生的所有重要事件的日志。

其他队列的常见替代设计是让队列服务跟踪读取器的位置——这意味着需要为每个读取器分配内存。表现不佳的读取器可能会重复请求新的队列会话，这会使队列服务不堪重负。这通常不是一个好的设计，因为我们希望读者可以自由阅读，而不会对队列造成任何风险。

# **阿帕奇卡夫卡**

卡夫卡是围绕一系列事件设计的，比如:

> 1001. **tim** 已经**购买了**travel deal’**Fiji**’
> 1002。**蒂姆**已经**将**他的订阅偏好更新为**每日**
> 1003。使用' **iphone** '
> 1004， **sam** 已在中**登录** **。**山姆**已经**开通**旅游交易**巴厘岛**’
> 1005。**山姆**已经**使用**桌面网页**
> 1006 在**中登录** **。**山姆**已经**购买了**交易**巴厘岛****

Kafka 事件读取器跟踪它们一直读到的流中的 ID，这意味着事件服务器不需要跟踪它们。这允许 Kafka 事件服务器保持可预测的内存使用，即使有许多行为不良的读取器。编辑:这过于简化了，因为卡夫卡确实通过跟踪消费者群体的补偿来提供帮助——不管一般原理是什么。

# 卡夫卡听起来很棒，为什么是 Redis Streams？

Kafka 是存储事件流的绝佳选择，它是为高规模而设计的。为了达到这种规模，卡夫卡采取了额外的复杂性。供应和管理 Kafka 设置确实需要理解一些复杂的概念。对于较小的项目，更简单、更小的系统可能是更好的选择。虽然我们都喜欢解决谷歌规模的问题，但这是很少需要的。

Redis 是简单的、通常非持久的数据存储最常见的选择之一。它在所有流行的编程语言中都有很好的库支持，并且被大多数开发人员很好地理解。这是为了简单性而牺牲强大的分布式弹性的一个极好的例子。Redis 现在支持 Kafka 事件流概念的一个更简单的版本，使得每个人都可以容易且廉价地获得架构概念。

为了开始使用 Redis 流，我将介绍 Redis 版本 5 中的两个新命令。按照这个例子，您可以在大约 2 分钟内创建一个使用 AWS ElastiCache 的在线 Redis 实例。

# 写入 Redis 流

> XADD stream _ name * key 1 value 1 key 2 value 2(等等)

XADD 允许我们编写事件流。让我们创建一个反映上面例子的事件流。我们将把我们的流命名为“事件”。

```
XADD events * user tim action purchase item travel:fiji
XADD events * user tim action preferences subscription daily
XADD events * user sam action login platform iPhone
XADD events * user sam action visit item travel:bali
XADD events * user sam action login platform “desktop web”
XADD events * user sam action purchase item travel:bali
```

“*”用于将可选参数与键值集分开。

这将把所有这些动作写到“事件”流中。

# 从 Redis 流中读取

> XREAD COUNT 2 流事件项目 id

XREAD 允许我们从这个队列中读取项目。让我们一次读两个项目:

```
demo.wiftycloud.com:6379> XREAD COUNT 2 STREAMS events 0
1) 1) “events”
   2) 1) 1) 1512991598699–0
         2) 1) “user”
            2) “tim”
            3) “action”
            4) “purchase”
            5) “item”
            6) “travel:fiji”
      2) 1) 1512991602438–0
         2) 1) “user”
            2) “tim”
            3) “action”
            4) “preferences”
            5) “subscription”
            6) “daily”
```

我们正在请求流“events”中的项目，从列表的开头开始(通过传递“0”)。我们只要求 2 个项目。为了获得接下来的两个项目，我们现在从 Redis 返回的最后一个 ID 开始，而不是从“0”开始，在上面的示例中是“1512991602438–0”。

```
> XREAD COUNT 2 streams events 1512991602438–0
1) 1) “events”
   2) 1) 1) 1512991605766-0
         2) 1) “user”
            2) “sam”
            3) “action”
            4) “logon”
            5) “platform”
            6) “iPhone”
      2) 1) 1512991617871-0
         2) 1) “user”
            2) “sam”
            3) “action”
            4) “visit”
            5) “item”
            6) “travel:bali”
```

其他选项是可用的— BLOCK 允许我们让 Redis 服务器在连接上等待，直到事件可用。

在这两个命令 XADD 和 XREAD 之间，我们可以非常容易地构建一个业务事件队列。

**Node.js 示例**

库对流的支持还没有完全准备好，但是目前可以使用自定义命令。使用 ioredis 做这件事的一个例子可以在[这里](https://gist.github.com/forkfork/c27d741650dd65631578771ab264dd2c)找到。

# TL；速度三角形定位法(dead reckoning)

Kafka 令人惊叹，Redis Streams 正在成为 Kafka 的一个伟大的 LoFi 替代品，用于管理一系列事件。