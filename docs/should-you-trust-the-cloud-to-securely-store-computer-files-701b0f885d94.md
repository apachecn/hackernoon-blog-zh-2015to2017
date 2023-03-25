# 你应该信任“云”来安全地存储计算机文件吗？

> 原文：<https://medium.com/hackernoon/should-you-trust-the-cloud-to-securely-store-computer-files-701b0f885d94>

![](img/183ba9966f968293b80b8f3b6dce3b61.png)

> 到了 2010 年，约翰·l·米勒(John L. Miller)从事了几项分布式和云存储服务。分布式系统博士。[原载](https://www.quora.com/Do-you-trust-the-cloud-to-securely-store-computer-files/answer/John-L-Miller)于 [Quora](http://quora.com?ref=hackernoon) 。

如果你有好的密码，存储在可靠的云服务中的文件是你能拥有的最安全的文件。谷歌、微软和亚马逊都为消费者文件存储提供可靠的云服务。

是什么让他们安全？

*   **冗余**。这项服务通常会在不同的地方存储每份数据的至少三份副本。您需要让所有三个副本在完全相同的时刻消失(例如，从三个独立的硬盘驱动器故障中消失)以丢失该数据。在从事过 EB 级存储服务之后，我只在相对较小的一部分数据(例如 10^-9)中看到过这种情况。即使这样通常也能恢复，只需要几天时间。
*   **安全**。如果您保管好您的凭证(例如，您的密码)，只有您可以访问这些文件。它们位于远程、物理安全的数据中心的机器硬盘上。你访问这些数据的连接通常是安全的，这样人们就不能偷听了。最大的危险是你如何保护你的本地机器。
*   **安全共享**。如果你想给其他人读权限(甚至允许他们写)，你可以。不需要复制文件，不需要给他们寄优盘。

就个人而言，我的家用电脑因为硬盘故障丢失了数百千兆字节的数据(视频、音频和一些重要的东西)。我放在云服务中的数据从未丢失过。

有两个附带条件:

1.  如果你的密码被破解，如果有人知道你的密码，他就可以访问这些数据。就像在你的家用机器上一样。
2.  病毒仍然可以破坏云数据的本地副本。如果您将电脑设定为自动将副本上传到云，那么您可以用损坏的副本覆盖云副本。这不是云存储的问题，而是你的机器的问题。

一些云存储具有文件版本控制，以帮助您从意外删除和覆盖中恢复。

总的来说，云是一个安全存储数据的好地方。

> 到了 2010 年，约翰·l·米勒(John L. Miller)从事了几项分布式和云存储服务。分布式系统博士。[原载](https://www.quora.com/Do-you-trust-the-cloud-to-securely-store-computer-files/answer/John-L-Miller)于 [Quora](http://quora.com?ref=hackernoon) 。
> 
> 更多来自 Quora 的趋势科技答案，请访问[HackerNoon.com/quora](https://hackernoon.com/quora/home)。