# 在灾难发生前阻止它

> 原文：<https://medium.com/hackernoon/stopping-an-iot-debacle-before-it-happens-874e067c72e9>

物联网行业喜欢重蹈覆辙。

本周，我们在 [Slideshare](http://bit.ly/2hjJE5T) 上发布了一份演示文稿，尽管它的布局比正常布局更冗长，但它强调了物联网新兴领域 LPWAN(代表低功耗广域网)正在犯的一些根本性错误。我们特别关注一项技术， [LoRa](http://www.semtech.com/wireless-rf/lora.html) ，这是一款出色的无线电产品，由 2B 一家名为 [Semtech](https://finance.yahoo.com/quote/SMTC?p=SMTC) 的硅公司制造。Semtech 公司的人很聪明，他们生产很棒的收音机，我们喜欢 LoRa。

关于这项技术的一些细节在甲板上——LoRa 基本上是一种新型的 LPWAN 无线电，它以一些新颖的方式使用一些不太新的技术来实现数公里的范围和多年的电池寿命。它的售价不到 5 美元，这让许多要求极低成本终端的物联网开发者趋之若鹜。如果你能过滤掉来自蜂窝行业的噪音，这可能是目前低功耗物联网领域最令人兴奋的事情，因为蜂窝行业正在过早地炒作自己的 LoRa 竞争对手。

由于 LoRa 只是一个收音机，它需要一个网络堆栈来运行应用程序。这就是物联网 deja vue 的用武之地:Semtech 似乎正在支持一些由 IBM Research 提供的 LoRa 免费软件，名为 LoRaWAN。这是一个便宜、简单的网络堆栈，可能很适合做概念测试或业余爱好者的实验。但令人惊讶的是，出于我们在本演示中概述的包括安全性在内的所有原因，一些开发商甚至一些手机运营商正不顾一切地在商业推广中使用 LoRaWAN。

我们总共发现了十个问题，但其中一个巨大的缺陷无疑是由于在设计过程中缺乏对安全性的优先考虑，这就是无线固件更新。在 LoRa 上运行 LoRaWAN，OTA 固件更新不是一个选项。不知何故，开发者——这是 90 年代的物联网——被期望“手动”更新固件。因此，如果您的电池供电温度跟踪端点恰好有一个 USB 连接器(罕见)，并且您不介意支付技术人员走到它旁边(昂贵)并“连接”固件更新(不可扩展)，作为 LoRaWAN 客户，您没有什么可担心的。但是您的终端需要一个安全补丁？祝你好运！如果你像我们一样认为 OTA 固件更新的缺乏只是无线弊端，那么 LoRaWAN 就是物联网行业重复 ZigBee 等安全“最糟糕做法”的一个很好的例子。

我们制作了这个[演示](http://bit.ly/2hjJE5T)来提醒开发者劳拉旺的危险，并提醒他们包括[我们的东西](http://haystacktechnologies.com/technology/)在内的替代物。但是关于物联网管理的第二点(或第一点，取决于你的观点)不能被夸大。像[飞利浦 Hue](http://www.techhive.com/article/3138872/internet-of-things/researchers-hack-philips-hue-smart-bulbs-from-the-sky.html) 灯泡惨败或 [Dyn/Mirai 未来组合](https://nakedsecurity.sophos.com/2016/10/24/mirai-mirai-on-the-wall-through-the-looking-glass-of-the-attack-on-dyn/)僵尸网络攻击这样的灾难给日常消费者和企业用户一种物联网安全[恐惧](http://www.usatoday.com/story/tech/news/2016/10/03/internet-things-brian-krebs-ddos-attack-distributed-denial-of-service/91481588/)的感觉。监管者也开始对“帮助”感兴趣。

希望 LoRa 不会成为另一个物联网安全警示故事——它足够年轻，能够做出一些中期修正。LoRa 本质上是一项优秀的技术，它只是通过 LoRaWAN 制造了一些可修复的青少年错误。

— [帕特·伯恩斯](https://www.linkedin.com/in/patrickeburns)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)的机会。
> 
> 要了解更多信息，请阅读我们的“关于”页面、[喜欢/在脸书上给我们发消息](http://bit.ly/HackernoonFB)，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！