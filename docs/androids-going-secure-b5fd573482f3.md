# 安卓变得安全了

> 原文：<https://medium.com/hackernoon/androids-going-secure-b5fd573482f3>

好吧，也许这有点言过其实，但是他们正在增加他们的手机[互联网](https://hackernoon.com/tagged/internet) [安全](https://hackernoon.com/tagged/security)一点点。Android 将在 Android 8.1 的操作系统中加入一个新的特性，叫做 DNS over TLS。这将做的是加密你的手机和 DNS 服务器之间的所有通信。正如我在[中谈到的，什么是 DNS，它是如何工作的。当你访问互联网上的一个网站时，你会使用 google.com 或 Amazon.com 这样的域名。问题是计算机不理解这些名称，它们使用 IP 地址相互通信，所以我们需要一种服务，可以将域名转换为服务器的 IP 地址，这就是 DNS 发挥作用的地方。然而，目前的设置方式是，所有这些信息都以纯文本(可读)形式传输。这使您很容易受到恶意人员的攻击。这项功能的一些好处包括更私密的互联网连接和免受黑客攻击。第一个更像是信任的过渡，因为你的 ISP 看不到你的 DNS 流量，但是 DNS 服务可以。如果你担心网络隐私，这是一件非常重要的事情。您使用的任何 DNS 服务器都会知道您正在访问什么站点，您何时访问它们，以及您在访问这些站点时的位置。这就是为什么确保您指定您的手机在访问互联网时使用您信任的安全 DNS 服务器非常重要。然而，这一功能确实能让您更好地控制您的互联网隐私，并让它远离您的 ISP。这个新功能的第二个好处是你所有的 DNS 流量都是加密的。那意味着没人能读它。黑客用来获取您的机密信息的一种常见攻击是 DNS 欺骗，其中一种方法是充当中间人。这意味着黑客以某种方式将自己置于你的手机和 DNS 服务器之间，拦截你所有的 DNS 流量。一旦他收到你的 DNS 请求，他就可以把你发送到伪造的网站，这些网站旨在窃取你的信息或感染你的设备。因为你的 DNS 流量现在是加密的，如果黑客拦截了你的 DNS 请求，他将无法读取它，也无法将你发送到假冒的网站。这是对 Android 操作系统的一大改进，也是互联网隐私和安全的一大进步。](https://www.prevsec.org/single-post/2017/03/04/What-is-DNS-and-How-Does-it-Work)

[查看原创故事](https://www.prevsec.org/single-post/2017/11/04/Androids-Going-Secure)

# 推荐文章

## [为什么永远不要相信域名！！](https://l.facebook.com/l.php?u=https%3A%2F%2Fmedium.com%2F%40Jekyl%2Fyou-should-never-trust-a-domain-name-26f6d374e571%23.vh879r47m&h=ATPZ2sQ-oOUn6AXUYp_84cvHvrklBlHeUVAz2EHviTexcMdI-PMCbjHLQT4yowkgChfVuMeFb_6U8YIHlUaKxL3BCma-ldHBz_cMUODvSiTa7FbVQaowmFq7j0KJZrDEnkGj5Kyy-CQBzaP6Keov8l6LEYTyFXO1k1PuldZO5bDnCg1LUBdBvtDZgzBGEFLUTNeChv-uZaYDKS_p0YfTtP2aVu6IOPW9lM2TbyFmcVRFgMwkZGGL53HtSor_dl5-19OEqYSWQBwmXHAKAnnnE0cyZQkgKmTFfTsPEeV9pA)

解释黑客如何利用您的 DNS 流量窃取您的信息

## [用 BGP MITM 偷互联网](https://www.prevsec.org/single-post/2017/03/04/Stealing-the-Internet-with-BGP-MITM)

解释黑客如何利用互联网中的漏洞远程拦截互联网流量

## [什么是智能域名系统，我为什么要问这个问题？](https://www.prevsec.org/single-post/2017/03/04/What-is-SmartDNS-and-Why-Should-I-Ask-About-It)

解释什么是 SmartDNS、它如何工作以及使用它的好处。