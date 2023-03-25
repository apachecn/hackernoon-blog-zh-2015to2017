# 九头蛇 Bug:简介

> 原文：<https://medium.com/hackernoon/the-hydra-bug-4a9ceb208436>

![](img/74c04a68ce56873317483760c75a8a5b.png)

Image copyright [Andrew Jian](https://www.flickr.com/photos/andrew_jian) [CC-BY](https://creativecommons.org/licenses/by/2.0/)

## 温和的开始

几个月前，我正在开发一些基于 KVM 的虚拟机管理代码的原型。目标是使用英特尔网卡上虚拟功能的 pci-passthrough 通过 iSCSI 引导 linux 虚拟机。我以为会很简单。接下来的几周是我遇到过的最棘手的调试，导致了一个我命名为“九头蛇 bug”的 Bug，因为，就像九头蛇一样，我每去掉一个头，就会有一个新的头冒出来。

虚拟机启动使用一款名为 [iPXE](https://ipxe.org) 的开源软件。要了解我的基本工作流程，请查看我的 KVM + iSCSI 教程的[第一部分](/@vishvananda/kvm-iscsi-part-i-iscsi-boot-with-ipxe-f533f2666075)和[第二部分](/@vishvananda/kvm-iscsi-part-ii-pci-passthrough-a-virtual-function-1fbc1ed40311)。我面临的第一个问题是在过渡到虚拟功能的过程中，如 [KVM + iSCSI 第二部分](/@vishvananda/kvm-iscsi-part-ii-pci-passthrough-a-virtual-function-1fbc1ed40311)所述。请继续阅读这篇报道。

在确认我可以使用虚拟函数正确引导我的映像之后，我切换到准备好的虚拟函数。我看到 iPXE 在我的 VNC 控制台中启动，但是它很快打印出一个[错误](http://ipxe.org/err/2e8520)。我检查了 [iPXE 文档](http://ipxe.org/download#debug_builds)并打开了英特尔驱动程序的调试(`DEBUG=intel,intelxvf`)，但是没有一条消息看起来可疑。我想，也许，如果我将调试输出与 virtio 驱动程序的类似输出进行比较，我可以看出它有什么不同。

我用 virtio 模型号构建 iPXE 并打开调试(`make bin/1af41000.rom DEBUG=virtio-net`)，并通过传递一个 romfile 参数指定 qemu 应该使用它。奇怪的是，我在我的控制台上看到同样的错误。

搞什么？我来回试了几次，试图找出我可能做错了什么，在第二次尝试虚拟化时，它成功启动了。奇怪的是，控制台中没有调试输出。在这一点上，我确信我要疯了。纯属运气，我注意到我忘记了在命令行中包含 romfile 参数。我的调试版本没有被加载，它自动退回到我的 qemu 包附带的旧的 virtio iPXE 选项 rom。啊哈！

所以一个旧版本的 iPXE 能用，新版本坏了。此时，简单的解决方案是回到旧的工作提交。不幸的是，我不能只使用旧版本，因为虚函数支持是最近才添加到 iPXE 中的。此外，我在去年的某个时候构建了 iPXE，当时我非常确定它在工作。我推断这一定是最近才发生的事情。如果我有一个简单的方法来确定什么东西坏了就好了。git-平分救援！

## 简短的旁白:Git 平分

[Git-bi section](https://git-scm.com/docs/git-bisect)是一个很棒的工具，可以找到历史上引入 bug 的时间点。它使用二分搜索法以最有效的方式找到违规的提交。如果树中的每个提交都是可构建的，那么对分就容易多了，这是保持 git 历史整洁的一个极好的理由。

虽然可以用脚本来编写对分过程，但是手动执行是相当容易的。检查完中断的提交和一个旧的、正在工作的提交的 sha 后，开始对分:

```
git bisect start
git bisect bad # current HEAD doesn’t work
git bisect good $SHA # SHA is the working commit
```

它将为您检查提交，并告诉您还有多少步骤。然后，您可以构建软件并测试它。如果有效，你输入`git bisect good`。否则你输入`git bisect bad`。然后它会自动检查一个新的提交，您重复这个过程。最终，您将聚焦于引入 bug 的确切提交。

为了加快测试周期，我编写了一个脚本来构建 iPXE 并引导 vm。即使在工作构建和中断构建之间有数千个提交，一分为二很快揭示出中断构建的[提交](https://github.com/ipxe/ipxe/commit/196f0f2551a4f82d2968c6e3a50aaa54a45ec779)仅在两周前被合并。

修复并不明显(错误是在 BIOS 磁盘读取的一些不可理解的低级汇编中)，所以我向上游报告了它，并决定在它崩溃之前使用提交的 iPXE 进行我的进一步测试。有了新的 iPXE 版本，我能够成功启动我的 cirros 映像。

现在构建工作正常了，我已经准备好发现九头蛇 bug 本身了。我在第一部分中发现了这个 bug 和一个解决方法。[第二部](/@vishvananda/the-hydra-bug-d98214029358)绕到了一个相关的 bug。在[第三部分](/@vishvananda/the-hydra-bug-1e71347d6759)中对该 bug 进行了全面的分析和修复。