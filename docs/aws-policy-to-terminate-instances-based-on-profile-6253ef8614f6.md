# 基于概要文件终止实例的 AWS 策略

> 原文：<https://medium.com/hackernoon/aws-policy-to-terminate-instances-based-on-profile-6253ef8614f6>

终止 EC2 实例是一个关键的操作，默认情况下应该拒绝，只有特定的角色才明确允许。

有些情况下，我们希望一个实例能够终止其他实例，例如一个*工作人员*角色能够检查 *Web* 实例的健康状态，并终止坏的实例。

我发现的大多数通过 IAM 策略允许 ec2:TerminateInstances 的[示例](https://aws.amazon.com/blogs/security/resource-level-permissions-for-ec2-controlling-management-access-on-specific-instances/)都指定了基于源 ip、用户认证方法或目标实例标签的条件。

以下代码片段显示了如何基于目标实例概要文件允许 ec2:TerminateInstances:

```
{
  "Sid": "TerminateWebInstances",
  "Action": [
    "ec2:TerminateInstances"
  ],
  "Condition": {
 **"StringEquals":
      { "ec2:InstanceProfile":
        "arn:aws:iam::<accountId>:instance-profile/<Web>" }**    },
  "Resource": [
    "arn:aws:ec2:us-west-1:<accountId>:instance/*"
  ],
  "Effect": "Allow"
}
```

在上面的例子中，这个代码片段将被添加到角色 *Worker* 的策略中，并将允许 *Worker* 终止任何具有实例概要文件 *Web* 的目标。

确保使用您想要终止的目标实例概要文件更改您的帐户区域、帐户 id 和<web>。</web>

您是否让实例终止其他实例，如果是，如何终止？我很想知道。请在下面评论或[在 Twitter 上找到我](https://twitter.com/0x0ece)。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)、[喜欢/在脸书上给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！