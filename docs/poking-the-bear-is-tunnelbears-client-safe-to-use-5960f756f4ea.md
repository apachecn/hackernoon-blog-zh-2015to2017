# 戳熊:TunnelBear 的客户端使用安全吗？

> 原文：<https://medium.com/hackernoon/poking-the-bear-is-tunnelbears-client-safe-to-use-5960f756f4ea>

![](img/ff312238699d79e91200a3754cb4f59e.png)

TunnelBear 正迅速成为“普通”人使用 VPN 的最受欢迎的选项之一。它很容易使用，针对性很强，但不令人讨厌的营销也没有坏处。另外，我对那些将出色的产品和可爱的前端结合在一起的品牌有一种非理性的喜爱。同样的原因，我爱上了[整洁的麦克风](http://www.neatmic.com/bee/King-Bee-Microphone)。

## 不是开源的

TunnelBear 便宜且非侵入性，但不是开源的。帐户管理在他们的服务器上进行，使用唯一的用户令牌。因此，客户端并不那么重要，不会提供太多的攻击面。但是攻击 TB 的服务不是这篇文章的重点。

也就是说，客户机毕竟是运行在你的电脑上的一个专有软件，管理着你通信中最隐私的部分:你的互联网连接。因此，了解它的工作原理以及它对你的了解和分享是非常重要的。

隧道熊还没有一个主要的纷争，而且(剧透)，我在我的分析中没有发现任何。然而，这个软件的某些部分可能会让你感到惊讶。

## Windows 操作系统

TunnelBear 面向更广泛的“普通人”受众，所以我的分析集中在 Windows 客户端。说“TunnelBear 不是开源的”并不完全正确。首先[它使用自由/开源软件组件](https://www.tunnelbear.com/notices)，并且它是用 C#编写的，如果你不熟悉逆向工程，这就等同于拥有一份完整的源代码的整洁的格式和优化的副本——除去注释。

## 资源

TunnelBear 基础安装将大约 165 个文件复制到您的 PC 上。该软件的独立部分是

*   资产(如 UI 中使用的音频文件和图片)
*   证书(由 Google 颁发，根和密钥)
*   语言汇编(由于一些奇怪的原因，我的副本包含法语资源，尽管实际的应用程序是英语的)
*   一份 [obfs4](https://github.com/Yawning/obfs4) 的代理引擎(Tor 组件)
*   各种(非常脆弱的)批处理脚本来管理[路由](https://technet.microsoft.com/en-us/library/bb490991.aspx) -ing、监控和 DNS(-缓存)
*   一份 [Hyper-V 网络 VSP 绑定](https://gallery.technet.microsoft.com/Hyper-V-Network-VSP-Bind-cf937850)管理工具
*   OpenSSL 的构建
*   OpenVPN 驱动程序
*   GalaSoft [MVVM 轻型工具包](http://www.mvvmlight.net/)
*   BitStadium 的 [HockeyApp SDK](https://www.hockeyapp.net/)
*   IntelliBear，一个 DNS 代理，不是用 C#写的。这是使用上述批处理脚本的应用程序。
*   [MahApps Metro](http://mahapps.com/) ，UI 工具包
*   ManagedWifi (NativeWifi)，一个简单的 wlanapi Windows API 包装器
*   一个 JSON (de)解析器
*   所有其他隧道承载代码。NET 程序集(带有完整的调试信息)

所以相当重。但这是堵住窗户上每个可能的漏洞所需要的。如果你只是想代理你的浏览器，像 [TorGateway](https://github.com/turbo/TorGateway) 这样的东西就足够了。但不是每个应用程序都支持互联网选项，所以其他连接仍然会暴露你的实际 IP 地址。你需要代理*一切*。

## 启动

这个应用程序是由这个搞笑的脚本启动的:

```
Set WshShell = CreateObject(“WScript.Shell”)
cmds=WshShell.RUN(“TBear.Client.exe”, 0, True)
Set WshShell = Nothing
```

那么让我们来看看我们的入口点，TBear.Client，我看的是 2.3.29.0 版本。在代码中多次提到了版本 3，并且对启用或禁用版本 3 的更新进行了许多检查，稍后会有更多的介绍。该集合链接到所有图像、声音和字体资产。它还包含默认的英语资源。

> 没有熊手套=冒险的网恋
> Grr。你的私处露出来了…

等等。过去分词（past participle 的缩写）

首先是连接助手例程。它只是通过使用`Utils.PingTunnelBear()`等待互联网连接，默认最多尝试 3 次。它 ping`www.tunnelbear.com`作为检查。

实际应用程序是异步启动的。启动时，它首先恢复默认代理。这适用于 windows 中的“internet 选项”代理设置。这些作为地址和标志(开/关)存储在`Software\Microsoft\Windows\CurrentVersion\Internet Settings`中。如果现有的代理地址是本地的，它只重置代理地址，如果您已经在使用另一个代理，这是一个好主意。

它还确保在系统电源模式改变时恢复运行并重新连接 VPN。

现在来看实际的启动处理程序。这是我们第一次遇到一个叫做`Utils.IsProd`的奇特旗帜。这个标志控制许多事情。其中一个是客户端访问的服务器，tunnelbear.com 用于生产，而 staging.tunnelbear.com 用于其他目的。

然而，更重要的是，这禁用或启用了内置于每个例程中的全面日志记录到标准输出功能:

```
Utils.IsGodModeUnlocked = **false**;
**if** (!Utils.IsProd) {
  Logger.**InitializeConsole**();
}
```

另一面奇特的旗帜是不言自明的`AutoUpdater.IsBeta`。

然后测试系统(或当前用户)是否能够运行 intelli bear(DNS 代理，记住)。这是通过在端口 53 上创建一个虚拟 VPN 实例来实现的。如果失败，用户可能没有权限或者已经有东西在那里运行。

最后，我们开始解析命令行选项。以下是可用的:

*   `-lang`，用于设置用户界面语言
*   `-autoconnect`，启动时是否自动连接
*   `-tcp`，强制 VPN 使用 TCP
*   `-intellibear-error`，记录最后一个 IB 错误(`-intellibear-info`记录最后一条信息语句)
*   `-intellibear-verbose`强制 IB 进入详细模式。
*   `-reset`强制复位
*   `-uitest mode`运行此 UI 测试模式
*   `-autologin user password`使用这些凭证登录并将它们“安全”地存储在磁盘上

根据这是否是您的第一次启动，您会看到“onboarding”或“main”窗口。通过曲棍球的遥测也在这一点上被初始化。

但是，让我们仔细看看我们最敏感数据的存储例程:

```
**int** k = Array.IndexOf<string>(e.Args, “-auto-login”);
**if** (e.Args.Length > k + 2) {
  Utils.**SafeSave**(“STORE_USER”, e.Args[k + 1]);
  Utils.**SafeSave**(“STORE_PASS”, e.Args[k + 2]);
}
```

我们在《熊》中找到了这个函数。Common，帮助器函数的集合。当然，这个函数内置了日志记录，但是唯一记录的是被修改的键的名称。长话短说，数据使用[受保护数据](https://msdn.microsoft.com/en-us/library/2fh8203k(v=vs.110).aspx)加密并保存到[隔离存储器](https://msdn.microsoft.com/en-us/library/3ak841sy(v=vs.110).aspx) KVS。这里没有问题。基本上所有的设置都在 IsolatedStorage 中管理。

帐户设置

有趣的事情发生在服务器端。如果您点击了任何会改变您帐户状态的内容，您将会在 TunnelBear 网站上这样做。例如，当您在 UI 中单击“升级”时，会发生以下情况:

```
Utils.**LaunchBrowser**(string.**Format**(Utils.**GetBaseWebsite**() + “account#/change?token={0}”, task.Result.Result.**ToString**()));
```

基本网站是生产服务器或临时服务器。但是令牌是如何生成的呢？

```
**new** **TBearClient**().**GetDeepLinkToken**(Utils.**SafeRestore**(“STORE_USER”), Utils.**SafeRestore**(“STORE_PASS”));
```

考虑到普通用户的密码，这似乎没有很高的熵。让我们深入了解一下。

DeepLinkToken 生成器实际上是一个两步过程，因为它首先咨询 API。

## API 回退

与前端一样，API 的基本 URL 取决于它是否是生产版本。所以是`[https://(staging).tunnelbear.com/core/](/(staging).tunnelbear.com/core/)`。但是坚持住。此 API 可能被阻止。那么 TunnelBear 是如何规避这一点的呢？

这就是 API 备份 IP LUT 的用武之地。它在启动时通过从 AWS 更新来填充。整个过程有点复杂(在 TunnelBear 中，所有事情都是通过委托任务来完成的)，但归结起来就是:

*   轮询[https://S3 . amazonaws . com/tunnelbear/(redacted)/config 3 . JSON](https://s3.amazonaws.com/tunnelbear/bluebear/config3.json)以获得要使用的 API 备份 IPs 和 API 网关主机名。
*   一丝不苟地记录一路上的一切。
*   `foreach` IP，添加`[https://ip/core/](https://ip/core/a)`作为 LUT 的备份地址。

API 通常返回四个 IP。这是一个我没有预料到的非常棒的回退，但是在我看来，实现确实有一些主要问题:

*   这个 API(尽管我在上面对它进行了审查)可以被任何软件轻易轮询。然后可以自动将其添加到阻止列表中。
*   所有的 IP 都可以被服务签名或者甚至是地区阻止，因为它们在网络和地理意义上都非常接近。

`GetApiBase()`虽然不使用 IPs，但只有网关，也就是 LUT 的第一个入口。

## 登录令牌

但是我跑题了。回到登录令牌。没什么特别的，有点让人担心。对`api/core/getLoginToken`的简单 POST 请求。帖子数据是

*   `username=turbo`
*   `password=nicetry`

就是这样。标头也拒绝缓存，并使用设置中的语言。我想看看我的代币。现在，回退实际上使这种方式更容易。我编辑了上面的完整 API URL，因为 tunnelbear.com API 域有一个 CloudFlare WAF，所以 IP 可能不应该是公共的；-).

这也干扰了 cURL(如果你没有伪造 UA，你只是得到一个 CF 挑战站点)。但是使用其中一个 IP:成功！

```
$ curl -data “username=minxomat%40gmail.com&password=nicetryagain” -k [https://(redacted)/core/getLoginToken](https://138.68.89.127/core/getLoginToken){“details”:”15242acd-****–****-****-7484152ce15c”,”result”:”PASS”}
```

PASS 表示成功，因为如果请求失败，服务器也会发送 200 OK。`-k`杀死证书错误(没有域名，咄！).

现在，我们可以看到令牌确实只依赖于明文密码。如果你知道使用 TunnelBear 的人的电子邮件地址，你只需要暴力破解密码。当然这是完全不可行的。使用普通 URL，您将达到 CloudFlare 的极限，使用 IPs，您可以将攻击分散到四个点，但您仍然会很快被发现为流量异常。

如果您看到包含 DeepLinkToken 的网站，您仍然需要登录。但是如果你登录了，它会带你去它应该去的地方。

## 诊断模式

如果你的熊行为不端，有一个故障排除模式，可以通过命令行 arg `-help`启动。

## 自动连接 Wifi

TunnelBear 的一个不寻常的特点是它的 [WiFi 管理功能](https://help.tunnelbear.com/customer/portal/articles/2458314)。仔细想想，这是一个必不可少的功能(目前也是 windows 版本独有的)。它轮询 Windows WLAN API 以检查当前的 WiFi 网络是否打开，并立即启用 VPN 连接，即使它是关闭的。

如果您已启用记录，记录将包含所有已连接的 WiFi 设定档的名称，以及它们是否安全。这部分客户不会给家里打电话，所以你的隐私保持不变。

## 警惕模式

另一个有趣但重要的设置是[警戒模式](https://www.tunnelbear.com/blog/vigilant/)。我在这里的批评是(可能是因为 UX 的原因)它默认是禁用的。

> 这是生活的现实，无线连接下降。[……]在 TunnelBear 连接时，Vigilant 会阻止任何不安全的流量，从而保护您的安全。

所以…怎么做？

义警防火墙，一个生活在 TBear 的类。Common 是一个薄薄的包装器，包裹着拼凑的外部实用程序(就像 Windows 上所有与网络相关的东西一样)。

为了阻止一切，它当然需要允许例外，这只是 TunnelBear 使用的程序文件。它使用 [Windows 防火墙 API](https://msdn.microsoft.com/en-us/library/windows/desktop/aa364695(v=vs.85).aspx) 来管理它的规则。所以，没有巫毒魔法或自制的废话。

## 硬件指纹

“等等。你说隐私！它到底想要一个硬件指纹做什么？!"，我听到你问。

每次创建 TBearClient 实例时，都会创建一个硬件指纹。再来剧透一下，*没错，是和他们的服务器共享的*。但是我们不要想太多。首先，我们需要剖析这个指纹是如何产生的。使用的数据是

*   BIOS 元数据(制造商、版本、ID 等。)
*   CPU(速度、名称、ID)
*   注册表中的计算机 GUID
*   MAC 地址
*   GPU ID
*   …

但实际上，这个文件的大部分都是死代码。我怀疑这是从别的地方复制粘贴来的。唯一相关的例程是这样的:

不过，这有点奇怪。它首先尝试从 32 位注册表中获取 GUID。返回的值可以是 null，在这种情况下，它会尝试 64 位注册表。但是，这个调用也可能失败，可能会将指纹设置为 null，并在其他地方导致异常。

无论如何，这个函数不会调用这个类中的任何其他方法。好的。现在，谁用这个指纹？

## 新用户注册

那个。注册是对 API 的一个较大的 POST 请求。头部包含语言，奇怪的是包含 OpenVPN 版本。帖子数据是:

*   用户
*   密码
*   程序集名称、版本和操作系统名称
*   国家
*   是否返回 JSON 的标志
*   指纹

所以很清楚为什么要在注册时发送指纹。如果将 cURL 与一个小脚本和一个垃圾邮件 API 结合起来，你可以在几秒钟内在同一台机器上创建新的 TunnelBear 帐户，完全避开付费模式。然而，它们都有相同的硬件指纹，你会很快被阻止。

但是，这是毫无意义的。因为现在你知道如何制造假指纹了。唯一的识别信息是你的 IP。但是，再次使用类似 TorGateway 的东西，并在每个帐户生成之间简单地轮换电路。无限自由隧道熊。这是 TunnelBear 需要考虑的事情，但无论如何不是一个严重的错误。

## 混杂的

其他事情都很平常。设置代理信息，启动 VPN 等。没什么特别的，也没什么打电话回家。证书是 JIT 创建和安装的。

## 智能熊

如果只是下载免费版的 TunnelBear，IntelliBear 是禁用的。它允许您在每个 URL 的基础上配置隧道。然而，IntelliBear 运行时是默认提供的，不受其他任何保护。

> 为了了解使用了哪些 URL，您需要代理 DNS。现在，你们中的许多人可能正在使用住宅互联网连接。这意味着您可能也在不知情的情况下使用 DNS 代理。我看到很多人把他们的域名换成谷歌的。事实是:没关系。无论如何。即使你认为你在使用那个 DNS，你的电脑也认为它在使用它。大多数 ISP 采用透明的 DNS 代理，MITM 您的 DNS 请求，并从本地 POP 为您提供服务，以提高性能。然而，这些 DNS 缓存经常遭受愚蠢的高缓存时间(你不能清除)，有时甚至服务广告。想知道自己有没有被影响，[看这个讨论](https://news.ycombinator.com/item?id=13037858)。

很高兴地报告，TunnelBear 有自己的功能来检测 DNS 泄漏，甚至 IPv6 泄漏。厉害！

我们来戳一下智能熊:

```
C:\Program Files\TunnelBear>intellibear32.exe -help
Usage of intellibear32.exe:
 -debug=”error”: Debug level, set between error, info or verbose
 -f=”domains.txt”: Domain list file path
 -intel=”none”: Is using Intellibear?
 -log=””: Log path
 -p=””: Which proto use for lookup domain
 -r=””: Regex pattern for match domain to use tcp proto
 -s=”scripts”: Script directory path
 -server=””: Server ip
 -t=0: How many seconds to timeout
 -tap=”utun0": Which Tap adapter to use
```

但是，IB 的实际包装器代码在程序集中是缺失的。

## 最后

在审查了数千行代码后，我可以非常自信地说，TunnelBear 没有隐私问题，没有收集不必要的遥测数据，是一个精简、快速和直观的应用程序。

所以，如果你正在看 TunnelBear 的特性集，并且你不需要更多，我可以从隐私的角度推荐它。如果你迫切需要绝对的隐私，Tor 将是更好的选择。

所有这些也意味着实现您自己的 TunnelBear 客户端相当容易！

## 接下来

您可能会注意到，我没有深入讨论更高级的特性。那是因为 V3 出来了，在那里这些大部分都彻底检修了(哈，懂了？).所以接下来是付费 V3 版本的补充审计。敬请关注。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)