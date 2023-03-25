# 公钥密码的衰落和证书透明度的提高

> 原文：<https://medium.com/hackernoon/on-guarding-against-certificate-mis-issuance-b968e61baf18>

周五，克里斯·帕尔默宣布打算在 Chrome 中弃用 HTTP 公钥密码(HPKP)，并在未来完全移除该功能。在本帖中，我们将看看公钥密码的问题，以及证书透明性如何成为避免恶意颁发证书的更受欢迎的方法。

[公钥锁定](https://developer.mozilla.org/en-US/docs/Web/HTTP/Public_Key_Pinning)允许 web 服务器向浏览器指定可接受的公钥，用于签署启动 TLS 连接所用的证书。这防止了攻击媒介，通过这种攻击媒介，认证机构(CA)被破坏并被恶意地用于为网站创建证书，使用与网站运营商指定的公钥不同的公钥。

![](img/3ab52a2893c251b0b283965275599f84.png)

[Ikhlasul Amal](https://flic.kr/p/8pb1rE)

TLS 旨在确保真实性和保密性。HPKP 提高了真实性，因为我们不再仅仅依赖 CA 来保证网站运营商的公钥。这样做可以防止网络上的中间人攻击(这会损害保密性)。有了 HPKP，恶意证书发行者也需要窃取网站运营商的私钥(当然，这不是不可能的)。

表面上看，这听起来是个好主意。但是如果你搞砸了，后果会很棘手。如果您在 HPKP 标头中指定了错误的密钥，用户将无法与您的网站建立新的安全连接，直到固定密钥过期。在下面的示例中，该密钥将被锁定 24 小时。

```
Public-Key-Pins: pin-sha256="key fingerprint"; max-age=86400,
```

搞砸的后果还不足以成为完全放弃 HPKP 的理由——毕竟，有很多方法可以通过错误配置拒绝为用户提供服务。但是更大的担忧来自于 ca 和浏览器厂商如何建立和验证信任链的复杂性。

# 中间密钥和根密钥

到目前为止，我们只讨论了固定网站运营商的密钥(存在于提交给浏览器的“叶子”证书中)，但 HPKP 允许您指定存在于信任链中任何位置的密钥，包括根和中间 ca 的密钥。

这为网站运营者提供了更强的保障。例如， [Github 锁定了两个根权限](https://report-uri.io/home/pkp_analyse/https%3A%2F%2Fgithub.com)(感谢 Scott Helme 提供的优秀分析工具)。一个可能是备用的。

关于备份的主题， [Scott 写了一篇很棒的文章](https://scotthelme.co.uk/guidance-on-setting-up-hpkp/)，讲述了如何在使用公钥锁定的同时管理撤销和过期。

验证从叶证书到根 CA 的信任链是复杂的。浏览器信任存储以及如何使用中间证书来建立信任链可能存在差异。正如克里斯·帕尔默在他对 blink-dev 的声明中指出的，这种复杂性转化为选择有效的键集来锁定的困难。

# 使用证书透明性来发现证书错误颁发

如果 HPKP 不是避免证书错误发布的可接受的解决方案，那么什么是呢？如果你一直关注这个话题，你可能听说过[证书透明](http://www.certificate-transparency.org/how-ct-works)，或 CT。

CT 背后的想法是，当证书被颁发时，它被提交到一个公共可审计的日志中(通常由 CA 自己来完成，但也可以由网站运营商来完成)。CT 日志是仅附加的，并且经过加密验证——使用 [Merkle 树](https://en.wikipedia.org/wiki/Merkle_tree)，就像你最喜欢的区块链一样。它们可以由任何人来运营——甚至是沙发后面的[——尽管实际上它们很可能由拥有运营资源的大公司来运营。](https://ct.filippo.io/)

在颁发期间向 CT 日志提交证书会返回一个签名的证书时间戳(SCT ),这基本上是一个承诺，即证书将被添加到公共日志中。在 TLS 握手期间，SCT 由 web 服务器提供，浏览器使用它来验证证书是否正确颁发。(这是对 CT 极度简化的解释；我强烈建议你阅读[设计文档](http://www.certificate-transparency.org/how-ct-works)。

与 HPKP 相比，CT 的一个关键优势(抱歉是双关语)是验证发生在证书(及其相关的信任链)的粒度级别，而不是公钥级别。虽然它比公钥锁定需要更多的支持基础设施，但 CT 增加的灵活性减轻了网站运营商详尽指定其信任链的负担。

为证书透明性做好准备，网站运营商可以使用 [Expect-CT 头](http://httpwg.org/http-extensions/expect-ct.html)来指定客户端应该从其网站获得 SCT，并在不影响使用的情况下初步报告任何错误。Chrome 采用[的建议日期是 2018 年 4 月](https://groups.google.com/a/chromium.org/forum/#!msg/ct-policy/sz_3W_xKBNY/6jq2ghJXBAAJ)。

希望这是对公钥锁定和证书透明性之间权衡的有用概述。我也在 twitter 上[，在那里我会时不时地尝试发布一些有智慧的东西。](https://twitter.com/mattrco)