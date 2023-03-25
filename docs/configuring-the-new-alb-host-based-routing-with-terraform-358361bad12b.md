# 使用 terraform 配置新的基于 ALB 主机的路由

> 原文：<https://medium.com/hackernoon/configuring-the-new-alb-host-based-routing-with-terraform-358361bad12b>

昨天， [Amazon](https://hackernoon.com/tagged/amazon) 宣布了一项期待已久的应用负载平衡器新功能。

[](https://aws.amazon.com/about-aws/whats-new/2017/04/elastic-load-balancing-adds-support-for-host-based-routing-and-increased-rules-on-its-application-load-balancer/) [## 弹性负载平衡增加了对基于主机的路由的支持，并增加了其应用规则…

### 我们很高兴宣布在应用负载平衡器上支持基于主机的路由。基于主机的路由允许…

aws.amazon.com](https://aws.amazon.com/about-aws/whats-new/2017/04/elastic-load-balancing-adds-support-for-host-based-routing-and-increased-rules-on-its-application-load-balancer/) 

在 [Globality](http://globality.com) ，我们使用 ALB 将流量路由到我们的 ECS 集群。我们利用目标群体来定位我们的许多微服务。

这个功能消除了我们路由系统中的很多复杂性，我认为用[地形](https://hackernoon.com/tagged/terraform)配置这个功能是值得的

# 代码

```
resource "aws_alb" "alb" {
  name            = "${var.name}-alb-${var.environment}"
  internal        = true
  security_groups = ["${var.security_group_id}"]
  subnets         = ["${split(",", var.subnet_ids)}"]tags {
    Environment = "${var.environment}"
  }
}resource "aws_alb_target_group" "alb_targets" {
  count     = "${length(keys(var.services_map))}"
  name      = "${element(values(var.services_map), count.index)}-${var.environment}"
  port      = "${element(keys(var.services_map), count.index)}"
  protocol  = "HTTP"
  vpc_id    = "${var.vpc_id}"health_check {
    healthy_threshold   = 2
    interval            = 15
    path                = "/api/health"
    timeout             = 10
    unhealthy_threshold = 2
  }tags {
    Color       = "${var.color}"
    Service     = "${element(values(var.services_map), count.index)}"
    Tier        = "${var.name}"
    Environment = "${var.environment}"
  }
}resource "aws_alb_listener" "alb_listener" {
  count             = "1"
  load_balancer_arn = "${aws_alb.alb.arn}"
  port              = "${element(keys(var.services_map), count.index)}"
  protocol          = "HTTPS"
  ssl_policy        = "ELBSecurityPolicy-2015-05"
  certificate_arn   = "${var.ssl_certificate_arn}"default_action {
    target_group_arn = "${element(aws_alb_target_group.alb_targets.*.arn, 0)}"
    type = "forward"
  }
}resource "aws_alb_listener_rule" "route_path" {
  count         = "${length(values(var.services_map))}"
  listener_arn  = "${aws_alb_listener.alb_listener.arn}"
  priority      = "${1 + count.index}"action {
    type = "forward"
    target_group_arn = "${element(aws_alb_target_group.alb_targets.*.arn, count.index)}"
  }condition {
    field = "host-header"
    values = ["${element(values(var.services_map), count.index)}.${var.domain}"]
  }lifecycle {
    ignore_changes = ["priority"]
  }
}
```

如您所见，我们正在创建一个只有一个监听器的负载平衡器。这个侦听器是“默认操作”，可以有许多路由规则。

在过去，这些规则只包括路径，新的特点是增加了主机头。

举个例子。默认动作是我们所有微服务中的一个微服务。然后，我们为所有其他服务的 HTTP 主机添加规则，并将流量路由到它们的目标组。

**不仅仅是一篇简短的代码文章**

真的比什么都重要，这是一个文化岗位。如果你知道你的系统有问题，你需要一直寻找解决方案，以及如何让它变得更好。

这项功能昨天才推出，今天我们已经在使用它，并融合了整个开发集群。

这从我们的 terraform 存储库中删除了大约 200 行代码，并且可能从我们用于管理和翻转部署环境的脚本中删除了大约相同数量的代码。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！