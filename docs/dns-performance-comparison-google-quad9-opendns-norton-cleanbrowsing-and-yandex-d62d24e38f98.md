# DNS 性能比较:Google、Quad9、OpenDNS、Norton、CleanBrowsing 和 Yandex

> 原文：<https://medium.com/hackernoon/dns-performance-comparison-google-quad9-opendns-norton-cleanbrowsing-and-yandex-d62d24e38f98>

由于我的 ISP 认为修改他们的解析器响应以将我推至他们自己的“搜索引擎”是一个好主意，我意识到了 Google、OpenDNS 和其他公司提供的免费 DNS 项目的重要性。

他们给你一个未修改的 DNS 体验，你可能不会从你的 ISP 那里得到。除此之外，一些玩家还阻止恶意域名，并为家庭和孩子提供阻止某些域名类别(主要是成人内容)的选项。

这太棒了，因为现在我们有多种选择。

然而，DNS 性能对于您自己的互联网体验至关重要，因此在您选择提供商之前，我们建议您测试一下，看看它从您的位置加载的速度有多快。在本文中，我们将比较来自世界各地的几个受欢迎的提供商的原始性能，并了解它们之间的差异。

# 测试过的玩家

**未过滤的 DNS +可选的恶意域名拦截:**

1.  谷歌:8.8.8.8(*只过滤*
2.  第九季度:9.9.9.9
3.  OpenDNS:208.67.222.222

**过滤/安全 DNS —阻止家庭/儿童的成人内容:**

1.  OpenDNS 家庭盾:208.67.222.123
2.  清洁浏览:185.228.168.168
3.  诺顿[连接安全](https://dns.norton.com/):199.85.126.20
4.  [Yandex 域名系统](https://dns.yandex.com/):77.88.8.7

# 位置

至于地点，我们在全球 14 个地点进行了测试:

*   **北美**:纽约、多伦多、蒙特利尔、亚特兰大、达拉斯、旧金山
*   欧洲:伦敦、巴黎、阿姆斯特丹、法兰克福
*   **亚洲**:东京、新加坡、澳大利亚
*   **南美**:圣保罗

# 结果摘要

我将测试分为两个阶段，以观察它们之间的差异。首先，我做了基本的 ping 测试。Ping 帮助我们测量测试服务器与其 DNS 服务器之间的延迟和距离。其次，我们实际测量了几个域名的缓存 DNS 查询:Google.com、Yahoo.com 和 Medium.com，并比较了所有的参与者。

***TLDR:谷歌最稳定，在所有地区的平均表现最好。Quad9 相当强大，在多个位置比谷歌更快。OpenDNS 在美国仍然相当不错，但在亚洲和南美就没那么快了。CleanBrowsing 和 Norton 相当一致，在所有位置都有良好的性能。Yandex 仅适用于俄罗斯。但是你自己看吧。***

# 测试 1: DNS 查找

使用 *dig* 实用程序并从结果中提取*“查询时间”*来完成 DNS 查找测试。对于每个域，我们测试了 5 次，每次用几分钟。我们删除了最差的响应，并对其他响应进行了平均(以减少测试位置的延迟影响结果的可能性)。

## 摘要

*   **在我们测试的大多数地点中，Quad9 获得了最快的**响应。在 8 个不同的地点，时间都不到 2 毫秒，这太神奇了。
*   在比较平均值(9.5 毫秒)时，谷歌排名第一。
*   Quad9 在一些城市(特别是圣保罗和东京)表现不佳，平均响应时间为 26 毫秒，高于谷歌(9 毫秒)。
*   Yandex 是所有地点中最差的。
*   [CleanBrowsing](https://cleanbrowsing.org) 在提供成人内容过滤时速度最快。

## 平均结果(去除每个最差位置时)

> #1 **Google** : 9.538 毫秒
> # 2**clean browsing**:9.923 毫秒
> #3 **Quad9** : 26.07 毫秒
> #4 **诺顿** : 32.76 毫秒
> #5 **OpenDNS** : 38.10 毫秒
> #6 **OpenDNS**

## 结果

> **纽约**
> #1 谷歌:1 毫秒
> #1 Quad9: 1 毫秒
> #3 干净浏览:4 毫秒
> #4 诺顿:6 毫秒
> #5 OpenDNS: 17 毫秒
> #6 OpenDNS_Family: 21 毫秒
> #7 Yandex: 104 毫秒
> 
> **多伦多**
> #1 Quad9: 1 毫秒
> #2 谷歌:10 毫秒
> #3 OpenDNS: 25 毫秒
> #4 OpenDNS_Family: 28 毫秒
> #4 CleanBrowsing: 28 毫秒
> #6 诺顿:29 毫秒
> #7 Yandex: 123 毫秒
> 
> **蒙特利尔**
> #1 清洁浏览:16 毫秒
> #2 Quad9: 17 毫秒
> #3 谷歌:18 毫秒
> #4 诺顿:25 毫秒
> #5 OpenDNS: 27 毫秒
> #6 OpenDNS_Family: 27 毫秒
> #7 Yandex: 116 毫秒
> 
> **亚特兰大**
> #1 Quad9: 1 毫秒
> #2 谷歌:2 毫秒
> #3 CleanBrowsing: 19 毫秒
> #4 OpenDNS: 21 毫秒
> #5 OpenDNS_Family: 30 毫秒
> #6 诺顿:24 毫秒
> #7 Yandex: 133 毫秒
> 
> **达拉斯**
> #1 谷歌:2 毫秒
> #2 干净浏览:2 毫秒
> #3 诺顿:3 毫秒
> #4 OpenDNS_Family: 7 毫秒
> #5 OpenDNS: 16 毫秒
> #6 Quad9: 30 毫秒
> #7 Yandex: 147 毫秒
> 
> **旧金山**
> #1 诺顿:2 毫秒
> #2 CleanBrowsing: 9 毫秒
> #3 谷歌:10 毫秒
> #4 Quad9: 26 毫秒
> #5 OpenDNS: 40 毫秒
> #6 OpenDNS_Family: 40 毫秒
> #7 Yandex: 175 毫秒
> 
> **伦敦**
> #1 Quad9: 1 毫秒
> #2 干净浏览:3 毫秒
> #3 诺顿:4 毫秒
> #4 谷歌:8 毫秒
> #5 OpenDNS_Family: 15 毫秒
> #6 Yandex: 34 毫秒
> #7 OpenDNS: 42 毫秒
> 
> **Paris**
> # 1 clean browsing:6 毫秒
> #2 Quad9: 7 毫秒
> #3 OpenDNS_Family: 10 毫秒
> #4 Google: 11 毫秒
> #5 诺顿:20 毫秒
> #6 OpenDNS: 23 毫秒
> #7 Yandex: 39 毫秒
> 
> **阿姆斯特丹**
> #1 谷歌:1 毫秒
> #1 Quad9: 1 毫秒
> #3 干净浏览:9 毫秒
> #4 诺顿:13 毫秒
> #5 OpenDNS: 14 毫秒
> #6 OpenDNS_Family: 14 毫秒
> #7 Yandex: 29 毫秒
> 
> **法兰克福**
> #1 Quad9: 1 毫秒
> #2 OpenDNS: 9 毫秒
> #3 谷歌:10 毫秒
> #4 诺顿:11 毫秒
> #4 CleanBrowsing: 14 毫秒
> #6 OpenDNS_Family: 20 毫秒
> #7 Yandex: 35 毫秒
> 
> **萨保罗**
> #1 干净浏览:5 毫秒
> #2 谷歌:15 毫秒
> #3 诺顿:113 毫秒
> #4 OpenDNS: 126 毫秒
> #5 OpenDNS_Family: 127 毫秒
> #6 Quad9: 179 毫秒
> #7 Yandex: 238 毫秒
> 
> **东京**
> #1 OpenDNS: 1 毫秒
> #2 CleanBrowsing: 5 毫秒
> #3 诺顿:8 毫秒
> #4 谷歌:18 毫秒
> #5 OpenDNS_Family: 36 毫秒
> #6 Quad9: 65 毫秒
> #7 Yandex: 309 毫秒
> 
> **新加坡**
> #1 OpenDNS_Family: 1 毫秒
> #2 谷歌:2 毫秒
> #2 Quad9: 2 毫秒
> #2 OpenDNS: 2 毫秒
> #5 CleanBrowsing: 4 毫秒
> #6 诺顿:9 毫秒
> #7 Yandex: 269 毫秒
> 
> **悉尼**
> #1 干净浏览:5 毫秒
> #2 Quad9: 7 毫秒
> #3 谷歌:16 毫秒
> #4 OpenDNS: 131 毫秒
> #5 OpenDNS_Family: 153 毫秒
> #6 诺顿:159 毫秒
> #7 Yandex: 339 毫秒

# 测试 2: Ping

除了 Yandex 之外，所有这些提供商都有一个选播网络，所以他们表现非常好。Yandex 似乎仅限于俄罗斯，因此与其他公司相比，ping 次数非常高。我们从结果中删除了他们，因为他们的 ping 测试在所有地点都超过了 100 毫秒。

诺顿不允许 ICMP，所以无法测试。

我们认为任何低于 5 毫秒的响应都是非常强的，并且彼此之间是平等的，因为它们对于最终用户来说是不可区分的。5-20 毫秒之间的响应是可以接受的(OK ), 20 毫秒-100 毫秒之间的任何响应我们都认为是慢的。高于 100 毫秒的 Ping 响应会失败，因为这意味着在该位置(或附近的某个地方)不存在。

**获奖者**

所有的提供商都表现得很好，但是谷歌和 Quad9 都在 14 个位置中获得了 5 次第一。CleanBrowsing 排名第三，是 3 个地区中速度最快的。

但是，当我们考虑 allow 所有位置的平均值时，Quad9 的表现并不理想，因为它从圣保罗、东京和达拉斯的 ping 时间非常长。考虑到平均值，这些是赢家:

> 谷歌:2.332 毫秒
> 干净浏览:3.545 毫秒
> OpenDNS: 11.93 毫秒
> OpenDNS_Family: 13.34 毫秒
> Quad9: 21.73 毫秒

这些数字对 OpenDNS 来说并不公平，因为他们表现很好，除了圣保罗，这扭曲了结果。如果我们去掉圣保罗，数字变为:

> 谷歌:2.318
> 干净浏览:3.473
> OpenDNS:4.233
> OpenDNS _ Family:5.675
> quad 9:9.941

在任何情况下，继续自己检查:

> **纽约**
> #1 Quad9: 0.900 毫秒
> #2 谷歌:1.59 毫秒
> #3 CleanBrowsing: 2.98 毫秒
> #OK OpenDNS: 9.11 毫秒
> #OK OpenDNS_Family: 9.01 毫秒
> 
> **多伦多**
> #1 Quad9: 0.467 毫秒
> #2 OpenDNS: 0.378 毫秒
> #3 OpenDNS_Family: 0.379 毫秒
> #4 Google: 0.749 毫秒
> #5 CleanBrowsing: 1.78 毫秒
> 
> **蒙特利尔**
> #1 干净浏览:9.21 毫秒
> #2 谷歌:9.91 毫秒
> #OK OpenDNS: 16.5 毫秒
> #OK OpenDNS_Family: 16.5 毫秒
> #OK Quad9: 16.6 毫秒
> 
> **亚特兰大**
> #1 Quad9: 0.585 毫秒
> #2 谷歌:0.947 毫秒
> #OK CleanBrowsing: 11.2 毫秒
> #OK OpenDNS: 12.3 毫秒
> #慢速 OpenDNS_Family: 24.2 毫秒
> 
> **达拉斯**
> # 1 OpenDNS:0.961 ms
> # 2 OpenDNS _ Family:0.905 ms
> # 3 Google:1.09 ms
> # 4 clean browsing:1.19 ms
> # SLOW quad 9:29.9 ms
> 
> **旧金山**
> #1 干净浏览:2.13 毫秒
> #2 OpenDNS: 2.34 毫秒
> #3 OpenDNS_Family: 2.37 毫秒
> #4 Quad9: 2.70 毫秒
> #5 谷歌:2.92 毫秒
> 
> **伦敦**
> #1 Quad9: 0.936 毫秒
> #2 OpenDNS: 1.54 毫秒
> #3 CleanBrowsing: 2.18 毫秒
> #4 谷歌:2.91 毫秒
> #5 OpenDNS_Family: 8.12 毫秒
> 
> **巴黎**
> #1 谷歌:4.51 毫秒
> #2 CleanBrowsing: 4.53 毫秒
> #3 OpenDNS_Family: 4.57 毫秒
> #4 OpenDNS: 4.69 毫秒
> #5 Quad9: 7.05 毫秒
> 
> **阿姆斯特丹**
> #1 谷歌:0.317 毫秒
> #2 OpenDNS: 0.852 毫秒
> #3 Quad9: 0.906 毫秒
> #4 OpenDNS_Family: 1.62 毫秒
> #5 CleanBrowsing: 4.85 毫秒
> 
> **法兰克福**
> #1 谷歌:1.07 毫秒
> #2 CleanBrowsing: 1.29 毫秒
> #3 Quad9: 1.37 毫秒
> #4 OpenDNS: 1.41 毫秒
> #5 OpenDNS_Family: 1.59 毫秒
> 
> **萨保罗**
> #1 谷歌:2.51 毫秒
> #2 干净浏览:4.49 毫秒
> #失败 OpenDNS: 112 毫秒
> #失败 OpenDNS_Family: 113 毫秒
> #失败 Quad9: 175 毫秒
> 
> **东京**
> # 1 clean browsing:1.15 ms
> # 2 OpenDNS:1.23 ms
> # 3 OpenDNS _ Family:1.27 ms
> # 4 Google:1.81 ms
> # SLOW quad 9:65.4 ms
> 
> **新加坡**
> #1 Quad9: 1.25 毫秒
> #2 CleanBrowsing: 1.57 毫秒
> #3 Google: 1.58 毫秒
> #4 OpenDNS_Family: 1.61 毫秒
> #5 OpenDNS: 1.92 毫秒
> 
> **悉尼**
> #1 谷歌:0.738 毫秒
> #2 CleanBrowsing: 0.992 毫秒
> #3 Quad9: 1.18 毫秒
> #4 OpenDNS_Family: 1.64 毫秒
> #5 OpenDNS: 1.81 毫秒