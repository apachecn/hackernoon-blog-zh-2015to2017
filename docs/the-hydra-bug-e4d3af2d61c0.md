# 九头蛇 Bug:第一部分

> 原文：<https://medium.com/hackernoon/the-hydra-bug-e4d3af2d61c0>

![](img/74c04a68ce56873317483760c75a8a5b.png)

Image copyright [Andrew Jian](https://www.flickr.com/photos/andrew_jian) [CC-BY](https://creativecommons.org/licenses/by/2.0/)

## 一夜平分使一个坚强的人变得谦卑

[介绍](/@vishvananda/the-hydra-bug-4a9ceb208436)解释了我如何设法让一个虚拟机使用虚函数在 iSCSI 上引导。一切似乎都很顺利，但是当我尝试完整的 linux 操作系统时，我发现了一个严重的问题。请继续阅读《九头蛇的发现》。

我有一个基于 busybox 的 linux 的工作启动，但是我需要尝试一个完整的 linux 操作系统。我拿起一个我知道能在真实硬件上与 iSCSI 一起工作的 Oracle Linux，创建一个新的 iSCSI LUN，并尝试引导它。

几秒钟之内，我兴奋地看到 grub 出现在我的虚拟控制台上。屏息静气，我等待着美丽的登录屏幕迎接我。我等啊，等啊，等啊…除了闪烁的光标什么也没有。起初，我怀疑镜像已经损坏，但是在真实硬件上从目标引导测试工作正常。

我怀疑它在将 iSCSI 卷从 iPXE 传递到内核时遇到了某种问题。我在内核命令行中添加了各种调试选项，以查看在引导过程的早期可能会发生什么。还是一无所获。

无奈之下，我尝试 early_printk=console。最后，一些文字！我从 BIOS 中看到关于内存分配的日志，一些关于 ACPI 启动的消息，然后是同样闪烁的光标。

我尝试了内核命令行选项来关闭 ACPI 并禁用早期引导过程的其他部分，但似乎没有什么帮助。会不会是 iPXE 中的另一个错误导致了这个问题？回到 git-平分。

我必须做的第一件事是找到一个有效的提交。我切换回虚拟模式，并验证它是否挂起。然后我从 ipxe-roms 包中尝试旧的 iPXE 版本。令人惊讶的是，它有效。

几个小时的平分和测试之后，我发现了导致挂起的[提交](https://github.com/ipxe/ipxe/commit/5350b65a3ce727f20e9d9fa7b9c2c0af52cfb7bd)。我检查了之前的提交并运行了虚函数的构建…失败。原来，中断提交是在我的网卡的驱动程序支持被添加之前。幸运的是，提交是关于改变压缩的，我认为我可以很容易地恢复它。我切换回最近的提交，添加一个损坏的恢复，并生成一个新的构建。

兴高采烈，确信我有一个解决方案在手，我开机…看到同样邪恶的闪烁光标。得意/泄气/g .我一定错过了什么！我反复检查我的构建和命令行选项。我切换回虚拟世界。没有骰子。恢复提交并不能解决问题。一定有另一个更近的提交导致了相同的挂起。时间不早了，但我烦得睡不着。我检查出有问题的提交，在它后面添加一个 revert，并在它上面重新设置所有新提交的基础。然后我又把它一分为二。

我很快找到了导致问题的第二个提交，它也是旧的。现在我有两个回复，光标仍然困扰着我。似乎每次我恢复一个提交，我都会找到另一个来代替它。显然，这种虫子就像九头蛇:每次我砍掉一个头，就会有一个新的代替它。

接下来是整个晚上的二等分、重新基础化和测试。当太阳升起的时候，我有 5 个不同的提交，都导致了同样的问题。我有一个工作构建，有一大堆随机的恢复和重置。这个建筑完全无法维护。为了有机会维护它，我必须把所有的回复都放在树的顶端。不幸的是，它们都被最近的提交所改变，所以我不得不手工重写所有的回复，希望我没有破坏任何东西。

此外，警钟长鸣。事情很可疑。5 个完全不相关的提交怎么会导致同一个 bug？其中一个[提交](https://github.com/ipxe/ipxe/commit/9aa8090d069eb0b36769f33544faf0e7e429e844)只是给一个函数调用增加了一个额外的参数。这怎么会破坏内核启动呢？我决定制作一个最简单的工作版本和不完整版本的例子，看看有什么不同。

我启动时启用了 early_printk，无论有没有上面的提交。我对输出进行了比较，发现两次运行之间的差别非常小。在引导过程的早期，内核记录 bios 报告的空闲内存范围:

```
e820: BIOS-provided physical RAM map:
BIOS-e820: [mem 0x0000000000000000–0x000000000009c3ff] usable
BIOS-e820: [mem 0x00000000000f0000–0x00000000000fffff] reserved
BIOS-e820: [mem 0x0000000000100000–0x000000007fef6fff] usable
BIOS-e820: [mem 0x000000007ff95000–0x000000007ffdffff] usable
```

虚拟机分配了 2G 内存。请注意 2G 末尾附近的小间隙，据报告它是可用的。挂起的引导程序报告较少的可用内存。测试了各种版本后，我把它缩小到 109 页(4K)。如果有超过 108 页的空闲空间，内核就会启动。差距小一点就挂了。这似乎意味着内核正在重写 iPXE 的一些重要部分。

## 简介:BIOS e820 例程

BIOS 使用了早期引导过程中的一些内存。如果引导装载程序或内核用自己的数据覆盖了 BIOS 的大部分，一切都会变得混乱，所以 BIOS 有一个接口，内核可以询问它正在使用什么内存。

为了与 BIOS 通信，它将 e820 放在 AX 寄存器中，调用 INT 15，BIOS 给内核一个已用内存的映射。iPXE 代码用自己的代码替换 bios 中断处理程序，并将自己添加到内存映射中。这样做的结果是，更大的 iPXE 版本将在内存映射中报告更多的保留内存。

不同数量的保留内存解释了多个修补程序如何会导致相同的问题。任何使 iPXE 二进制文件变得更大的改变都可能将差距缩小到要求的 109 页以下。不幸的是，它并没有真正告诉我们为什么这个差距的大小很重要。iPXE 似乎正确地报告了保留的内存，所以内核不应该覆盖它。也许内核不尊重保留空间？事实上，一些日志在启动后显示内核使用了不应该使用的空间:

```
No NUMA configuration found
Faking a node at [mem 0x0000000000000000–0x000000007ffdffff]
Initmem setup node 0 [mem 0x00000000–0x7ffdffff]
NODE_DATA [mem 0x7ffb9000–0x7ffdffff]
```

节点存储器的开头(0x7ffb9000)与 iPXE(结束于 0x7ff95000)重叠。我们发现了一个内核错误吗？我的 Oracle linux 映像有一个旧的、修改过的内核(UEK，一个添加了 dtrace 和其他特性的 3.8 内核)，所以我的第一步是查看这个问题是否仍然存在于一个新的内核中。尝试标准的 Oracle linux(基于 3.10)内核在启动时打印相同的分配信息，但是，您瞧，它通过了挂起，并一直显示到登录提示符。

我尝试了一个新的 UEK(基于 4.1)，它很好。我找到一个 fedora 3.8 内核，它也可以启动。看来，无论出了什么问题，都是这个老 UEK 特有的。此时，我有一个选择。我可以开始尝试调试这个特定的内核，或者尝试在 iPXE 端解决这个 bug。如果我在 iPXE 之后在内存中保留这 108 页(或者更少，当前版本只保留 75 页)会怎么样？它只有 400K 的内存。今天，这只是沧海一粟。

对 iPXE 的修改很简单:

```
--- a/src/arch/x86/interface/pcbios/hidemem.c
+++ b/src/arch/x86/interface/pcbios/hidemem.c
@@ -37,3 +37,3 @@ FILE_LICENCE ( GPL2_OR_LATER_OR_UBDL );
/** Alignment for hidden memory regions */
-#define ALIGN_HIDDEN 4096 /* 4kB page alignment should be enough */
+#define ALIGN_HIDDEN 0x80000
```

最后一次构建，瞧，内核启动得很好。我仍然想找到这个 bug，但是我有更紧急的事情，所以只能等了(直到[第三部分](/@vishvananda/the-hydra-bug-1e71347d6759))。你看，虚拟机工作完全正常，但如果我重新启动它，iPXE 无法 iSCSI 启动。我的工作还没完成。故事在[第二部](/@vishvananda/the-hydra-bug-d98214029358)中继续。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)