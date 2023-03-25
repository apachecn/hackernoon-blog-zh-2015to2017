# Hackapost:黑客马拉松如何成为世界上最好的学习工具

> 原文：<https://medium.com/hackernoon/hackapost-how-hackathons-can-be-the-best-learning-tool-under-the-sun-9c97e567e0a5>

![](img/46c99c0745d80ba76d35da54058e7780.png)

回想一下 Hackapost hackathon，我想分享一些关于技术栈如何工作的元素。

确实有相当多的移动部件参与其中。

首先说一下感知世界。

为了做到这一点，我们需要传感器。

为了方便我们，我们从 AllThingsTalk(简称 ATT)获得了 LoRa 开发套件形式的硬件和工具。

嗯，等等，劳拉是什么？

Lora 实际上代表一个更长的东西:“LoRaWAN”。

“LoRaWAN 是一种低功耗广域网(LPWAN)规范，适用于区域、国家或全球网络中的无线电池供电设备。”

劳拉联盟网站上有很多关于它的东西:[https://www.lora-alliance.org](https://www.lora-alliance.org)

作为一个硬件，它有一个微芯片组件的形式(实际上是一些 [Semtech](http://www.semtech.com/wireless-rf/lora.html) 许可的东西)。我们需要把这个组件插在某个地方，这样我们才能控制它。在 LoRa devkit 中，我们可以将其插入名为“Mbili Sodaq”的 Arduino 板。Sodaq 代表太阳数据采集。这有点像太阳能电池板，电池可以在没有外部电源的情况下保持系统供电(如果需要外部电源，它仍然可以工作)。更多信息请上:【http://support.sodaq.com/mbili/ 

![](img/29d195b8e21f17a7e41ade453eed5cb7.png)

让我们回到传感器。Arduino 板通常需要一些焊接或与试验板玩耍等。这一个就不一样了，因为它有所谓的“T6”凹槽插座。这些插座使得以模块化方式连接传感器变得更加容易。LoRa 开发套件包含了很多这样的传感器。

我们在黑客马拉松期间发现了这个板的一些情况:只有一个 I2C 连接器，这意味着人们必须在 GPS 模块和 3 轴加速度计之间做出选择。你不能同时拥有两者。

这里有一个组成套件的小零件的视图。

![](img/c46cd99341a8583c6059942fadf3f886.png)

因此，连接传感器很容易，获得 LoRa 调制解调器也很容易。

现在，需要一些软件来完成所有这些工作。比如将数据发送出去，以便我们可以在应用程序或网站中使用。

首先，无线电方面的事情

这就是 Proximus EnCo 派上用场的地方。他们运营着 LoRa 网络，并在全国部署了天线。

在这一点上，一些地区仍然参差不齐。

为了能够与网络通话，我们的小设备必须被识别。

使用 LoRa 开发套件时，用户在 [https://devs.enabling.be](https://devs.enabling.be) 网站上激活开发套件后，会自动获得在系统中注册的设备。

![](img/f077b467c38404a092778f11cfa6f5da.png)![](img/e0069d1a75d8f66334464edd963ca325.png)

这意味着拥有一个设备 id。

设备 id 唯一地标识了板(我真的不知道这是板，还是调制解调器本身)。

![](img/c276017217e7ddb02d043bb01dd84301.png)

还有一个重要的概念，即“云渠道”。

对于开发套件，会自动创建一个通道，将传感器数据转发到 AllThingsTalk。

通道正在使用 MQTT。MQTT 的参考地点是 http://mqtt.org

![](img/22c34c6eb4f71350aaafaec5a5da8352.png)

MQTT 是什么？这是一种物联网协议，由安迪·斯坦福-克拉克(Twitter 上的 [@andysc](https://twitter.com/andysc) )于 1999 年在 IBM 发明。新来的就这么多！

在 Pi 播客#14 上有一个对他的超级酷的采访，那是今年二月播出的。[http://thepipodcast . com/the-pi-podcast-14-Andy-Stanford-Clark/](http://thepipodcast.com/the-pi-podcast-14-andy-stanford-clark/)

对于感兴趣的人，可以从这里获得协议规范:[https://www . IBM . com/developer works/web services/library/ws-mqtt/](https://www.ibm.com/developerworks/webservices/library/ws-mqtt/)

所以，MQTT 代表“MQ 遥测传输”。它是一种“轻量级发布/订阅协议，通过低带宽、不可靠或间歇性的通信，在 TCP/IP 上为远程传感器和控制设备流动。”

啊，非常适合物联网领域。MQ 代表“消息队列”。

有很多可用的软件来处理 MQTT:[https://github.com/mqtt/mqtt.github.io/wiki/software?id =软件](https://github.com/mqtt/mqtt.github.io/wiki/software?id=software)

客户端库也很多:[https://github.com/mqtt/mqtt.github.io/wiki/libraries](https://github.com/mqtt/mqtt.github.io/wiki/libraries)

对于服务器端，可以使用 RabbitMQ 及其 MQTT 适配器([https://www.rabbitmq.com/mqtt.html](https://www.rabbitmq.com/mqtt.html))。

我想这就是 AllThingsTalk 在他们那边使用的东西。

还有一个使用非常广泛的经纪人叫 Mosquitto:[http://mosquitto.org/](http://mosquitto.org/)，这个月起代码就住在 GitHub 上:[https://github.com/eclipse/mosquitto](https://github.com/eclipse/mosquitto)

回到我们的云渠道订阅，以下是我们的详细信息:

![](img/d80a371068d13627681b928e61a28518.png)

嗯，好吧，但是我们的设备是如何发送信息的呢？

从 Enabling.co 检索数据就是使用 API 的问题。

![](img/f0100acd0c7368e2bcfb4e89fe37ba9c.png)

但这不是我们想要的。我们想发送从 Arduino 板上的传感器感应到的数据。

让我们来看看如何对 Arduino 板进行编程以实际发送数据。

为了对 Arduino 进行编程，必须使用 Arduino IDE。

![](img/4f4714d70201af33fd93ce4e54e9c9eb.png)

这个 IDE 通常有一个浅色的背景，它在编码的时候会杀了我的眼睛。正如你所看到的，它实际上是有可能得到一个黑暗的主题。就查[https://github.com/jeffThompson/DarkArduinoTheme](https://github.com/jeffThompson/DarkArduinoTheme)

为了设置 LoRa 开发工具包，让我们访问一下 AllThingsTalk。

网站本身并不能说明太多，因为真正的肉在 http://www.smartliving.io/的

呃，是的，实际上更进一步:【https://maker.smartliving.io/ 

![](img/aa2c5852557d1239ccf9ffe6d852d54c.png)

那里有关于它如何工作的文件:[http://docs.smartliving.io/kits/lora/](http://docs.smartliving.io/kits/lora/)

![](img/211cdfb89f3821678214aab3461817e8.png)

注册(“申请”)设备后，有一个不错的“游戏场”可供使用:

![](img/b2195840feb262584bd5c3d8464de313.png)

有趣的是，人们可以在操场上添加其他设备。

让我们看看我们的，包含我们的单个漂亮的小设备。

![](img/d09a5544f681f0c388bce4d9e33ace0e.png)

因为在写这篇文章之前我们有一些乐趣，你可以看到不同传感器的几个值。这显然会空出一个新的工具包。

回到硬件/Arduino 前面有一些事情要做。

基本上，有一些关键的事情要做:

*   将板卡类型添加到 Arduino IDE 中
*   将电路板、LoRa 和传感器的库添加到 Arduino IDE 中
*   获得一份“密钥”的拷贝，使设备在发送无线电信息时能够被识别，并使信息进入授权系统

![](img/f6277177104e0d9d1aa5cae9fadccaf3.png)![](img/16988adb99cf3ae81af682bff7a77bcb.png)

这些库也在 GitHub 上，您可能想了解最新信息:

[https://github.com/allthingstalk/arduino-lora](https://github.com/allthingstalk/arduino-lora)

![](img/9010dd9c814a5fb9172244bfe51f5081.png)

里面还有一组示例草图。草图是 Arduino IDE 帮助您编辑、编译和传输到 Arduino 板的东西。

还有一个 keys.h 文件，其中放有从 smartliving.io 云设置中检索到的密钥。

![](img/4d4d62cc115ea04a9e041c2bc61be366.png)

这些是代码使用的东西。

让我们来看看草图的内容:

[https://github . com/all thingstalk/arduino-Lora/blob/master/ATT _ 劳拉 _ IOT/examples/experiments/environmental sensing/environmental sensing . ino](https://github.com/allthingstalk/arduino-lora/blob/master/ATT_Lora_IOT/examples/experiments/environmentalSensing/environmentalSensing.ino)

![](img/ef296f926704c0f714737ae38915c8a4.png)

正如你所看到的，草图使用了你的按键，也有一个使用微芯片调制解调器的 ATTDevice。

从开发人员的角度来看，这看起来很容易。

在下面，是的，有很多事情正在发生，我们并不介意。

因此，通过 USB 将主板连接到您的 PC……(前提是在 Arduino IDE 中选择了正确的 COM 端口)

![](img/b6a78594d53643208d04963d9cee59a4.png)

编译上传就行了。

![](img/1cafe8df63d1365067cbe0626de66725.png)

从那时起，系统将运行其程序循环，并根据其逻辑发送数据。

![](img/7976578759d349884fb70b4e5c337ccf.png)

发送将数据推送到 ATT 平台(通过启用，但那是在幕后)。

![](img/45a4a46116e83aab669f38f9a5106678.png)

从传感器到 LoRa，从启用到智能生活，数据正在流动。

![](img/0bc65bbba4514a1a2c5821171a666b7a.png)

这就是我们可以开始谈论在我们的应用程序中实际使用数据的地方。

怎么会？

通过利用 API。

实际上有几种方法可以做到这一点。

有一种简单的方法，甚至不涉及智能生活。或者也可以。稍后将详细介绍。

我们来谈谈 [Waylay.io](http://waylay.io)

![](img/bae18d86a89f92530d6c5cf3be16e25f.png)

Waylay 实际上做了很多事情:

![](img/48a1b627a768a3c469368d00aa1f9379.png)

好玩的部分在[https://playground . waylay . io](https://playground.waylay.io)

我有机会与 Waylay 的首席技术官 Veselin Pizurica 聊天。很棒的人，超级聪明。我从我们的聊天中得到一个爆炸。在操场上弄了个账号，开始玩。

如果你想了解更多，可以在 http://docs.waylay.io/找到 Waylay docs。

![](img/462766a097fac01e1d1657cd0ba4dda4.png)

有一个传感器实际上可以直接挂钩到 Enabling 的 API。

![](img/d8445cc1f580612636ef616db28771f6.png)

代码就在那里，随时可以使用。

![](img/13fe27df19df9a6663ef81e9936a247b.png)

都是 Javascript。

一个人能用它做什么是疯狂的。但这将是另一个职位。

![](img/6bf8cc657dcaa178579019a981da56ca.png)

但这不是智能生活的 API。

如果我们想使用 Smartliving 的 API(这是我在黑客马拉松期间想做的，如果我有更多关于 Waylay 的线索，我不确定我会这样做……)

让我们来看看 Smartliving API。在[http://docs.smartliving.io/api/](http://docs.smartliving.io/api/)有记录

在处理 API 时，我首先想到的是:“我到底要如何用这个东西来验证我自己呢？”API 对此往往有自己的看法。

![](img/c2b9fdb4b694185c0cdb6cd903e044a9.png)

所以，我们需要身份证，钥匙等等。

那些在哪里？

就在这里:

![](img/cdf2c210c06fcb0368ad1449828080b4.png)

然后，乐趣开始于用头球做正确的舞蹈等等。

我实际上想使用我最喜欢的开发平台来实现这个 API，这个平台恰好是 Pharo([http://pharo.org](http://pharo.org))

![](img/1a215be2638e7f4935f682d94df83a1d.png)

最近，我也在使用 Python 和 Javascript|Node，但是它们没有“Pharo 的感觉”。

如果你想更多地了解 Pharo，这里有几篇关于 Medium.com 的好文章。

例如:

[https://medium . com/concerning-pharo/elegant-pharo-code-bb 590 f 0856d 0 #。sjqdgmtsc](/concerning-pharo/elegant-pharo-code-bb590f0856d0#.sjqdgmtsc)

它实际上是一种非常有能力的语言。我用它做了一个处理 CMTSes 和 cable modems 的解决方案:【http://pharo.org/success/CableExpertise】T2

看起来是这样的:

![](img/56dfb3f46ffda09262bd70c9d55b4048.png)

几乎可以很好地利用物联网领域的所有项目，因为没有什么限制它只做 CMTS 的工作。这是一个通用的数据收集和演示+报警的事情。

所以，我想使用 Pharo 来访问 API。

从一个小测试开始…

![](img/130f336d8875286091751d7baa62f24a.png)

该客户端是 AttClient 实例。

![](img/798457133934e82df71e6eff6d3f937f.png)

AttClient 身份验证如下所示:

![](img/a1c2b143175f467d16461557e26c94d6.png)

一个好处是你不必创建无数的类来组织事物，就像在 Objective-C 中一样，有协议，也就是分组方法。

![](img/e01d6a637d956688f15dae5f0512e57a.png)![](img/f4031f342ca82c39c0b2293f1c457b84.png)

长话短说，API 调用代码最后看起来是这样的；

![](img/0b06691e6c61f06f2948554f81109074.png)

探索这个东西不应该出现在测试中，但是，嘿，我们在黑。

它显示了这样一个对象资源管理器:

![](img/99a9a3004bfbca51c335f55f51d03a78.png)

这使得在不离开开发环境的情况下探索 API 的结果和向下钻取变得非常方便。

一旦 API 客户端工作正常，我就开始制作服务器，这样我们就可以按照前端开发人员要求的格式访问数据。

在 Pharo 中，我们有一个名为“[茶壶](https://skrishnamachari.wordpress.com/2014/08/28/teapot-pharo-web-rest-framework-it-aint-micro/)的小框架，它可以很容易地做到这一点:

![](img/124841ddac243fbbc9b4303709aad49e.png)

返回的 xml 实体由#sensors 消息生成，该消息实际上发送了带有我们实际设备 id 的' #sensors:'。

![](img/487f824ac41e243699eb31ef127822ae.png)

给予:

![](img/64f6054210797126d70d11408a55f61c.png)

我们需要 XML 输出，这就是为什么最后一行转换输出的数据。

这其实也很简单:

![](img/225f3c18dca4dfeddc245ec5ef26a647.png)

如您所见，Pharo 代码非常干净。

在这一点上，我们现在有可能以我们想要的方式使用数据。

当然，我们可以马上在 JS 前端应用程序中拥有同样的东西。

但我更感兴趣的是获得一个基于 Pharo 的组件，帮助我创建物联网应用，而不是商业案例。

我们有机会使用非常有用的硬件，测试所有传感器，通过所有路径获取数据，发现新的服务和功能，集成 API，并结识新的酷人。

对我来说，这才是最重要的，不是成为另一家创业公司的一部分。坦率地说，我已经忙于几个项目，关键是要更深入地理解所有这些。

如果物联网领域出现另一场黑客马拉松，我相信我会做好更充分的准备来应对它。

我还剩下一套超级酷的装备，对此我有了新的想法！

![](img/93c6a598498ab0a5cf197ba98ebd31aa.png)![](img/0eca03ac7c5a53153641c32efcb57bef.png)![](img/d679fd94b16536ef4760f703ed9dee3b.png)![](img/55531672d4a28f8bc938716a6186a8ed.png)![](img/5cecb80a5a1d18d5248b366949a075c2.png)[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)