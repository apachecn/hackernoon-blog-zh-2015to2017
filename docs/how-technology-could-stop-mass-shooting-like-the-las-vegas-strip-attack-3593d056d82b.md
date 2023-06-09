# 科技如何阻止像拉斯维加斯大道袭击那样的大规模枪击事件？

> 原文：<https://medium.com/hackernoon/how-technology-could-stop-mass-shooting-like-the-las-vegas-strip-attack-3593d056d82b>

## 应用正确的技术组合可以保护无辜的平民免受暴力袭击，比如拉斯维加斯乡村音乐节**。**

在经历了 91 号公路丰收音乐会恐怖袭击的彻底破坏和极度悲伤后，我们——一群电子和计算机工程师，头脑风暴如何保护无辜的人在[未来](https://hackernoon.com/tagged/future)免受类似袭击。执法小组花了一个多小时在曼德勒海湾酒店追踪并与袭击者交战，此时他已经对无辜的人群耗尽了所有弹药。如果枪手能被更快地发现，一些宝贵的生命可能会被挽救，这一想法向我们提出了这一挑战。

如何在黑暗中找到一个从 1000 多英尺外开枪的枪手？

以下是我们的发现——通过适当组合应用现有的技术，这样的射手可以在大约 10 到 15 秒内被发现并阻止！

在人们开始被子弹击中之前，连续不断的巨大射击声是发生可怕事情的唯一迹象。从声音的三角测量中找到枪声的来源已经有[几年了](https://en.wikipedia.org/wiki/Gunfire_locator)，并且随着先进的 [DSP](https://en.wikipedia.org/wiki/Digital_signal_processing) 系统对来自麦克风阵列的输入进行实时处理，可以非常准确。

![](img/64a048db6fe7b0be65fabd4c8934530c.png)

A conceptual microphone array that works with a DSP and Artificial neural networks to triangulate the gunshot position and distance.

即使在嘈杂的环境中，人工神经网络也可以高精度地将声音模式与先前记忆的声音样本进行分离和比较，并可以区分原始声音和回声。

即使枪上安装了抑制器，声音仍然足够大，传感器可以准确地确定枪手的方向和距离。

我们分析了第一轮射击的音频(大约每秒 10 发子弹，大约 100 发子弹)，发现将枪声与其他背景噪声隔离是容易实现的。

![](img/715c3c2f602fefa9bfb1a71b03244bb8.png)

Audio clip from the recording of the Las Vegas Country Music Festival attack. The spikes represent gunshots.

这些系统能够在不止一个攻击者的情况下同时跟踪多个来源。

这种大型集会必须用一辆或多辆装有这种炮火定位系统的战术警车来保护，这种炮火定位系统可以自动控制机动化主要武器的方位和高度，在第一声枪响后的 2 至 4 秒内将它指向并瞄准确切的炮火源。

安装在主要武器上的探照灯和激光束将有助于更快地瞄准目标，并震慑/警告攻击者。

![](img/91cf6251323bcd3fcf6b055c2a2372c3.png)

因为探照灯和激光束会将射手的注意力吸引到设备上，所以应该使用防弹玻璃盾牌来保护所有的设备和操作者。

从枪中射出的子弹非常热，在先进的热感相机中会被照亮，这可以用来帮助精确瞄准。

热感相机将允许使用隐蔽的红外探照灯，而不是可见光，允许在不引起攻击者注意的情况下操作。

![](img/b148f2e21a8c4d355253619a165df777.png)

*High Speed thermal cameras can capture the bullet as it leaves the gun at a very high temperature*.

在不可能或无法安全实现干净爆头的情况下，可以部署一队配备了禁用措施的无人机，并用小型炮火定位器进行引导，或由母单位远程控制，以接近炮火源。

由于采取直飞路径会有被击落的风险，无人机可以被编程为采取高架路径，让攻击者措手不及。

![](img/0ba69e26e1f7d4513c46ddb581c62128.png)

Drones can fly close to the attacker, send back live video and deliver non-lethal countermeasures.

这种无人机可用于对攻击者及其周围环境进行视频分析，也可用于部署非致命性对策，如胡椒喷雾、催泪瓦斯或诱惑物。

母机和无人机上的大功率扩音器可以用来警告建筑物的其他居住者远离窗户，保持低姿态并隐蔽起来，以防反击不可避免。

把所有这些放在一起会有多难？

一点也不难。几乎所有这些技术都已经到位，其中一些被用于军事目的。

准备好这样的东西需要多长时间？

由于这些技术中的大部分都是经过测试和验证的，即使不需要几个小时，也不需要几天就能把它们组合在一起，并在现场观看。

这些要花多少钱？

考虑到受伤者的医疗费用和宝贵的生命损失，这笔钱一点也不贵。

在枪击已经开始，一些人已经失去生命之后，找到一个枪手有什么好处？

显然，这将阻止更多的人受伤和死亡，但在心理层面上，在这些场所出现这样一种高科技防御武器，以及攻击者在最初几秒钟内就会被杀死的意识，应该会阻止更多邪恶的人尝试另一次模仿攻击。做好充分准备，让别人知道我们做好了充分准备，是最好的防线。让科技拯救我们所有人！

webandcrafts.com