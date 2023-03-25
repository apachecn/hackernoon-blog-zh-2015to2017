# 九头蛇虫:第二部分

> 原文：<https://medium.com/hackernoon/the-hydra-bug-d98214029358>

![](img/74c04a68ce56873317483760c75a8a5b.png)

Image copyright [Andrew Jian](https://www.flickr.com/photos/andrew_jian) [CC-BY](https://creativecommons.org/licenses/by/2.0/)

## iPXE — 60%的情况下，它每次都有效

在解决了[简介](/@vishvananda/the-hydra-bug-4a9ceb208436)中的一个小错误后，[第一部分](/@vishvananda/the-hydra-bug-e4d3af2d61c0)阐述了我是如何花了一周时间为我的基于 KVM/vf 的虚拟机管理原型发现并解决 hydra 错误的。

我的 iPXE 构建第一次就成功了。我在《T4》第一部中稍微夸大了事实。重启失败并不完全是意外。我在原型开发过程中遇到过几次这个错误，但是我会通过使用不同的 vf 或者重启主机来修复它。继续阅读冒险的第二部分。

在虚拟机第二次启动时，iPXE 无法通过 iSCSI 进行通信。打开 intelxvf 驱动程序的调试显示没有接收到数据包。一旦 vf 处于这种状态，它将保持断开状态，直到主机重新启动。很明显，虚函数中有一些持久的状态没有被清除。

## 简介:英特尔网卡配置

英特尔 82599 网卡的配置是通过写入特定的 DMA 位置来执行的。各个位置控制不同的特性，在数据手册中称为“寄存器”。例如，有一个寄存器控制应该从哪里读取包数据。

一些寄存器只能通过物理功能写入(例如，在管理程序主机上)，但是其他寄存器可以通过虚拟功能写入。这就是虚函数驱动程序建立发送和接收队列的方式。

如果一个错误持续存在于内核/驱动程序的多个实例中，那一定是因为某个寄存器值已经被改变了。iPXE 中的驱动程序自己设置寄存器值，因此它可以通信，但是 linux 驱动程序稍后会在内核接管后重新配置寄存器。

我通读了英特尔[数据表](http://www.intel.com/content/www/us/en/embedded/products/networking/82599-10-gbe-controller-datasheet.html)，试图理解可能会出错的地方。我找到了用于设置寄存器的 [iPXE 代码](https://github.com/ipxe/ipxe/blob/master/src/drivers/net/igbvf/igbvf_main.c),看看它是否无法重置某些东西。奇怪的是，代码很清楚地向物理设备发送了一条请求重置的消息。为什么不会重置呢？

我惊讶地发现，物理功能的[驱动程序代码](http://lxr.free-electrons.com/source/drivers/net/ethernet/intel/ixgbe/ixgbe_sriov.c?v=4.8#L650)(这是虚拟机管理程序端的物理设备的驱动程序)在接收到 vf 重置消息时并没有重置一切。虽然一些值(如 mac 地址)被重置，但另一些值没有重置(如发送和接收队列)。这给了我一个假设:linux 中的 vf 驱动程序一定设置了一些寄存器，当 iPXE 第二次运行时，它使用了不正确的值。

我仔细阅读了代码，寻找一个主机端的解决方法来执行 vf 的完全重置。最终我在[的 ethtool](http://lxr.free-electrons.com/source/drivers/net/ethernet/intel/ixgbe/ixgbe_ethtool.c?v=4.8#L986) 找到了一个。我现在可以通过使用 ethtool 更改父设备的环参数来重置队列。这对于测试来说太棒了，因为这意味着我不需要重启，但是不幸的是，它一次重置了所有的虚函数。这意味着在创建虚拟机时重置一个虚拟机的网卡将重置同一机器上的所有其他虚拟机。那可不太友好啊！所以我要修改 iPXE 端才能正确复位。

最初，我试图比较 linux vf 代码和 iPXE vf 代码来寻找不同之处，但是它们在风格上的差异足以使它变得困难。我想知道是否有某种方法可以让我查看 vf 寄存器的当前设置。幸运的是，英特尔为 [ethregs](https://sourceforge.net/projects/e1000/files/Ethregs%20-%20Register%20Dump%20Tool/1.20.1/) 提供了源代码，这是一个可以转储当前寄存器值的工具。

我的目标是在 iPXE 将网卡的寄存器重置为 linux 之前和 linux 之后的相同状态后，获取网卡的寄存器。通过 ethregs 和 diff，我发现第二个 RX 队列的配置有所不同。RX 队列的寄存器包含一个 DMA 位置。这是网卡将数据包内容写入驱动程序的地方。在第一次启动时，第二个 RX 队列将寄存器设置为零——它被禁用。在第二次启动时，那里有一个值。

深入研究代码，我发现 iPXE 只配置了一个 RX 队列。一幅画面开始变得清晰。linux 驱动程序正在配置多个 RX 队列，并告诉硬件它的接收环在内存中的位置。在重新启动时，iPXE 将第一个队列设置为其接收环，但是第二个 RX 队列保留了一个不再使用的内存位置。NIC 很高兴地将一些数据包发送到第二个队列，但是没有任何东西在读取它！

我更喜欢保持 iPXE 代码简单；它不需要多个读队列。因此，我添加了一点代码，以便在它执行现有配置之前重置所有的 RX 和 TX 队列。使用新代码的第一次引导仍然运行良好，但是第二次仍然失败。显然还有更多寄存器需要复位。

通过细致的区分，我发现了更多的差异。主要的违规寄存器似乎是一个名为 INTELXVF_PSRTYPE 的寄存器，数据手册告诉我它配置数据包如何在报头和数据之间拆分。网卡可以做一些报头处理，linux 利用了这一点。这种高级设置只会让 iPXE 感到困惑，因为它希望整个数据包都出现在它的队列中。

SRRCTL 寄存器中也有一些被 linux 改变的标志。这些似乎不太可能导致问题，但我还是重置了它们。有了我的新变化，每种情况下的寄存器都尽可能地接近:在分配的内存位置或计数器上有微小的差异，但稳定值是相同的。

剩下的就是看第二次启动是否真的有效。我切换到我的虚拟控制台来检查 iPXE 的输出。有那么一会儿，我想它又停下来了，但是它成功了，我被食物迎接了。成功！

我跑了第三个和第四个靴子的好措施。一切顺利。看来我的原型已经完成了。我为一个上游[提案](http://lists.ipxe.org/pipermail/ipxe-devel/2016-December/005288.html)编译了一个补丁。我开始做其他事情。我的原型变成了一个几乎可以投入生产的系统。在我们发布之前，修复我在[第一部分](/@vishvananda/the-hydra-bug-e4d3af2d61c0)中解决的内核错误可能会很好。那次冒险在[第三部](/@vishvananda/the-hydra-bug-1e71347d6759)中有详细描述。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)