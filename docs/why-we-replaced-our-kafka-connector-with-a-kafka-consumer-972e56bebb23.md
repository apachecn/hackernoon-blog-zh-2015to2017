# 为什么我们用 Kafka 消费者替换了 Kafka 连接器

> 原文：<https://medium.com/hackernoon/why-we-replaced-our-kafka-connector-with-a-kafka-consumer-972e56bebb23>

![](img/b1cd86fb905672f09c13fd58870003d8.png)

Confused?

几个月前，我写了一篇关于[在我们开始使用自己的水槽连接器](https://hackernoon.com/writing-your-own-sink-connector-for-your-kafka-stack-fa7a7bc201ea)后，创建自己的水槽连接器的文章。令人惊讶的是，上周我们用[卡夫卡消费者](https://kafka.apache.org/documentation/#consumerapi)代替了它。我将回顾我们的经验，并尝试在这篇短文中写出这两种技术的优缺点。

## 但是为什么呢？

说实话，我们对我们定制的弹性水槽连接器非常满意；它已经处理了我们的流量(每秒高达 7000 条产品数据(*每条约 1000 行)*，在 4 个 docker 容器上)，并且没有发生任何性能故障。然而，**对于定制的 Kafka 连接器来说，调试和测试是非常困难的！**

![](img/a635e1e7376948073e5302d50fe1cf99.png)

Yes, unfortunately.

你不能轻易地编写你的集成测试，因为它是你的 Kafka 堆栈中的某种插件。你需要模仿内部的 Kafka 类，创建[反腐败层，](https://docs.microsoft.com/en-us/azure/architecture/patterns/anti-corruption-layer)在单元测试上花费大量时间，而且……你仍然不满意，因为没有端到端或集成测试。

假设您在产品中有一个 bug，但是由于上述原因，您的测试没有完成，所以您无法解决它。好，您想在本地调试连接器。不幸的是，对于卡夫卡连接器来说，这是一个相当奢侈的词。如果需要在某个地方放一个断点，需要做很多“ *wtf…真的吗？”*这里所说的[人员](https://stackoverflow.com/a/45719652)。创造你的环境？你可以把你的连接器连接到 Landoop 的 fast-data-dev 栈，但是同样，这不是调试，你只是克隆了问题，并试图在一个更可控的基础设施中解决它。

出于这个原因，我们用一个普通的 Kafka 消费者替换了我们的定制水槽连接器，现在维护它就像是小菜一碟。(至少目前如此……)

我们对性能感到疑惑，但测试结果给我们留下了深刻的印象。对于我们的性能问题，它们几乎是相同的。也许它可以显示一个相当大的流量的变化，但这个时候你可以正确的缩放，这是卡夫卡最好的优势。

## 向卡夫卡消费者转移

调试和测试 Kafka 消费者相当容易，就像一个常规的 API 一样。如果你决定转向消费者，你可以用许多编程语言来写。我们最初考虑用 Python 或者 C#来写，但最终选择了 Java。因为我们可以在短时间内复制 Kafka Connect 项目中的类。(您必须为您的定制 Kafka 连接器使用 Java)

我想在这里强调主要的一点:提交补偿。

在自定义 Kafka 连接器中，如果 put 方法中没有异常，框架会自动提交消息:

It is in your …SinkTask class.

在 Kafka Consumers 中，您可以再次使用自动提交，但是这一次您应该考虑到框架是在您收到主题消息之后提交的。我想说的是，如果你的代码在传输数据前抛出异常，你就丢失了消息。

相反，您可以手动提交，这很容易模仿 Connect 的行为，只需在完成工作后提交即可:)

Committing manually at the end of the process

## 那么，你的意思是不推荐卡夫卡连接器吗？

不要！如果您需要简单地将您的主题数据传输到另一个系统，或者相反，并且有一个社区/汇合支持的 Kafka 连接器，请使用它！-稍微配置一下，嘣，就好了。但是，如果您因为某些原因必须编写自己的 Kafka 连接器，请考虑测试和调试是很难的。

感谢阅读，

有时候我会发一条有用的信息: [@_skynyrd](https://twitter.com/_skynyrd)