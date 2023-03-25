# 麦当劳印度泄露了 220 万用户数据

> 原文：<https://medium.com/hackernoon/mcdonalds-india-is-leaking-2-2-million-users-data-d5758b2eb3f8>

## 这是根据我们负责任的披露政策发布的

> **UPDATE2:** 麦当劳的修复不完整，端点仍在泄漏数据。我们已就此再次与他们沟通，并正在等待他们的回应。
> 
> **更新 1:** 麦当劳印度已经回复我们，他们已经修复了这个问题，并将发布一份官方声明，敦促他们的用户升级应用程序。

麦当劳印度应用 McDelivery 泄露了超过 220 万用户的个人数据，包括姓名、电子邮件地址、电话号码、家庭地址、准确的家庭坐标和社交档案链接。我们在 2 月 7 日联系了 McDelivery，并在 2 月 13 日(33 天前)收到了一位高级 IT 经理的确认。该问题尚未修复，在初始确认失败后，我们继续努力获取修复更新。

一个未受保护的可公开访问的 API 端点(用于获取用户详细信息以及作为客户 id 的可连续枚举的整数)可用于获取对所有用户个人信息的访问。

与[欧盟](http://ec.europa.eu/justice/data-protection/)，美国[新加坡](http://www.pwc.com/sg/en/personal-data-protection.html)不同，印度缺乏强有力的数据保护和隐私法律或处罚，这导致印度公司忽视用户数据保护。非政府组织也同样缺乏改善这种状况的动力。过去，我们已经在几个印度组织中发现了 50 多起数据泄露事件。事实上，当我们发现印度公司的 API 中没有个人或支付数据泄露漏洞时，我们感到惊喜。

**API 端点:**[http://services . MC delivery . co . in/processuser . SVC/get user profile](http://services.mcdelivery.co.in/ProcessUser.svc/GetUserProfile)

对 curl 请求的示例响应:

![](img/7c8cd92bcbda29641ec9e8e425e23f83.png)

McDeliver API response

**披露时间表:**

*   2017 年 2 月 4 日— Fallible 向 McDelivery 报告了该问题
*   2017 年 2 月 13 日 McDelivery IT 经理承认了该问题。
*   2017 年 3 月 7 日——Fallible 发送了一封询问状态的电子邮件，但没有收到 McDelivery 的回复。
*   2017 年 3 月 17 日— Fallible 发送了另一封电子邮件；没有来自 McDelivery 的响应；
*   2017 年 3 月 18 日—还没有回应。McDelivery 用户仍然容易受到攻击。公开披露。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)