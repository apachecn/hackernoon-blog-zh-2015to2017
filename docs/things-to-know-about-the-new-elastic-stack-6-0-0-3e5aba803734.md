# 关于新的弹性堆栈 6.0.0 要知道的事情

> 原文：<https://medium.com/hackernoon/things-to-know-about-the-new-elastic-stack-6-0-0-3e5aba803734>

![](img/0d7f8e2dea03fbeca2db3ea7c2b24d56.png)

构建完整而有弹性的堆栈 6.0.0 正式正式发布，这是数千个拉请求、数百名提交者的努力、两个 alpha 版本、两个 beta 版本和两个候选版本的最终结果！

以下是您需要了解的关于 6.0.0 的重要信息

## 弹性搜索 6.0

随着向 Lucene 7 的迁移，Elasticsearch 6.0 增加了强大的功能，如索引排序以加快查询排序，稀疏文档值以减少磁盘存储。另外，全新的升级体验。

![](img/5b02bc3f39774311fbc2af067fabc0dd.png)

*   无停机升级
*   迁移助手和跨多个集群的搜索
*   使用序列 id 加快重启和恢复速度
*   使用排序索引加快查询速度

# 基巴纳

仅仪表板模式、全屏模式、将保存的搜索导出到的能力。csv、通过 X-Pack Gold 及更高版本的 UI 创建警报、X-Pack Basic 中的迁移助手，所有这些都通过对比度更改、键盘可访问性增强等功能变得更易于访问

![](img/64504dc0cfc38ff1922069aff5760cc3.png)

*   可视化代码的 CSV 导出和重构
*   阈值警报的观察器 UI
*   X-Pack 监控群集警报的电子邮件通知
*   X-Pack 许可证到期的群集警报

# Logstash

同一个 Logstash 实例中的多个独立管道，以及附加的 UI 组件——X-Pack Basic 中的管道查看器和 X-Pack Gold 中的 Logstash 管道管理。

![](img/c165845dc255de2e8196390bc73de2a5.png)

*   通过多条管道简化处理
*   利用弹性堆栈集中管理管道
*   可视化管道逻辑和性能
*   从摄取节点到日志存储的平滑路径

# 搜索

Auditbeat 加入了 Beats 家族，以利用您的 Linux 审计框架。此外，Filebeat 和 Metricbeat 中的新选项更深入地挖掘了容器和编排指标。

![](img/51ac389ece864b689703e403482d5e16.png)

*   Kubernetes 和 Docker 的日志和指标
*   审计节拍—运营安全
*   新命令和配置布局
*   更高效的 Metricbeat 存储
*   内部管道重构和更好的性能

# ES-Hadoop

对 Spark 结构化流的一流支持已在 6.0 中登陆，同时重写了连接器映射代码，以更好地支持多个映射。还增加了对读写新连接字段的支持。用户现在还可以利用除内联以外的脚本类型进行更新操作。

![](img/f7e052845a65f7e2fd16b4d283a3dd27.png)

*   Spark 2.2.0 和对 Spark 结构化流的稳定支持
*   支持新的连接字段
*   多重映射和多重索引读取

这些是我在 Elastic Stack 6.0.0 版本中的要点，每一个都是精心挑选的，以获得一个整体的想法。