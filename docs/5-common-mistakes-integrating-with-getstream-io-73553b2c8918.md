# 与 GetStream 集成的 5 个常见错误。木卫一

> 原文：<https://medium.com/hackernoon/5-common-mistakes-integrating-with-getstream-io-73553b2c8918>

![](img/d6c82efa77b3755ed26bfaee6e4ed39b.png)

开始使用和集成一个技术解决方案，比如 Stream，可能是令人兴奋的。有时候你只想撕开盒子，开始行动。当实现一个新的 API 时，我很少想阅读一页又一页(有时很无聊)的文档——这就是你的 TL；DR 版快速有效使用 Stream 需要了解的 5 件事。

准备，预备，开始！

1.  **插入活动时，发送 foreign_id 和 time 字段。**为 foreign_id 发送一个 UUID(或其他唯一值),并获取当前时间。这样做将确保您拥有记录的唯一标准。如果不这样做，您将无法在以后更新该活动。拥有这两个字段可以确保 activity 是唯一的，你可以把它想象成一个多列主键。—因此您也不会在引擎盖下遇到副本，或者在您的提要中遇到副本。
2.  **使用 id_lte 代替 offset 进行分页。**提要中的活动是根据基于时间的 UUID 排序的。我们建议您使用`id_lt`分页。这比传统的基于偏移量的分页要好得多。
3.  **不送 PII。我们建议发送用户 id，而不是个人信息，当你收到反馈时，做一个快速的数组转换。在处理个人信息的存储时，小心谨慎总是一个好主意，但是不要让这吓到你。我们有非常成功的客户，他们的应用程序包含位置等敏感信息，甚至是来自可穿戴物联网的心率等受到严格控制的健康信息。**
4.  **保持活动规模在 1k 以下。**虽然我们允许您向 activities JSON 包中添加任何自定义字段，但如果大小超过 1k，性能就会受到影响。我们经常收到客户关于在活动中附加图片、视频等的问题。或者媒体。使用存储您的媒体的位置的参考，并在您阅读完我们的内容后在您的后端丰富它。
5.  请记住，扇出只能深入 1 层。当您添加一个活动时，它会在该 feed 之后的所有 feed 中自动更新，但不会更新到下一个级别。例如:如果 Feed C 跟在 Feed B 后面，而 Feed B 跟在 Feed A 后面(C- > B- > A)，那么向 Feed A 添加一个活动将自动扇出到 Feed B。除非您将它添加到 to 字段中，否则 Feed C 不会获得更新。(但是请注意，TO 字段的限制是 100 个提要。)

在早期阶段对上述内容进行规划可以帮助您避免意外和成本超支。

如果你还没有这样做，但是一定要检查一下 [Stream 的文档](https://getstream.io/docs/)以获得其他有用的提示和技巧。

只是为了好玩…

> 这是由软件工程师 David Stillman 领导的 GetStream.io 团队的合作。博文原文可以在[https://getstream . io/blog/5-common-missons-integrating-stream-API/](https://getstream.io/blog/5-common-mistakes-integrating-stream-api/)找到。