# 加泰罗尼亚政府对选民数据使用密码朋克技术

> 原文：<https://medium.com/hackernoon/is-sensitive-voter-data-being-exposed-by-the-catalan-government-af9d8a909482>

![](img/ad3d828095bbc4e07f735b2a38f5bcb2.png)

你可能知道，加泰罗尼亚正在为自决而斗争。加泰罗尼亚政府计划在 2017 年 10 月 1 日举行独立公投。另一方面，西班牙政府决心阻止这次公投，因为他们认为这是非法的。因此，它采取了以下措施来阻挠投票:[它审查网站，没收数百万张选票，使用军警拦截载有选民可以在哪里投票的信息的邮件，把人关进监狱，等等。](https://www.nytimes.com/2017/09/20/world/europe/catalonia-referendum-spain.html?mcubz=0)

然而，在这篇文章中，我想强调的不是政治，而是加泰罗尼亚政府如何使用一种非常规但有趣的方法来通知公民去哪里投票。我将详细介绍选民需要提供的信息、如何处理这些信息以便将相关数据反馈给选民，以及这种方法的利弊。

## 用户需要哪些数据？

加泰罗尼亚政府推出了一个网站，这样选民就可以查询 10 月 1 日去哪里投票。在该网站中，要求用户输入以下信息:

*   国民身份证号码(DNI)
*   出生日期
*   邮递区号

## **这些数据会发生什么？**

在用户输入这些数据后，会发生一个异步请求，当请求完成时，如果有匹配，就会向用户提供他们应该在哪里投票的信息。

很标准的东西，对吧？但是，有一个重要的细节:**用户输入的数据从不离开浏览器**。当用户点击提交按钮时发生的请求是一个类似下面的`fetch`请求:

```
https://onvotar.garantiespelreferendum.com/db/XX/XX.db
```

其中`X`是一个十六进制数字。响应包含 70 到 100 行似乎是加密的记录。

## **数据库是否被暴露？**

我承认当我第一次看到它的时候，我有点害怕。看起来很明显，整个数据库是以小块的形式暴露的。是的，我看了 JS 代码，这就是正在发生的事情。有 65，536 个(`0x0000`到`0xFFFF`)不同的数据库块可供任何人公开访问。

我关心的是用户是否能够解密他们的数据或整个数据库。当我深入研究代码时，我意识到事实上事情并不像最初看起来那么糟糕。相反，这是当用户点击提交按钮时实际发生的情况:

*   国民身份证号码的前 3 个字符被删除。根本用不到。
*   修剪后的 ID 号与 DOB 和邮政编码连接在一起。
*   SHA256 散列被应用于该键 1715 次。让我们称这个散列循环的结果为$HASH1。
*   另一个 SHA256 散列被应用于$HASH1。让我们把这个结果称为$HASH2。
*   $HASH1 和$HASH2 都是由 64 个十六进制字符组成的字符串。
*   $HASH2 的前 4 个字符用于确定需要获取的 DB 块。(例如:`d4f3`->-T1)
*   $HASH2 的下 60 个字符用于查找包含与用户相关的数据的记录。如果响应中某一行的前 60 个字符与$HASH2 的后 60 个字符匹配，则该行包含该用户的投票数据。
*   如果匹配，则使用$HASH1 作为密码解密前 60 个字符之后的内容。结果是该用户的投票地点信息。

## 他们为什么选择这种方法？

我的假设是他们这样做有两个原因:

1.  避免审查。一旦这些数据库块公开，几乎不可能阻止人们知道他们应该去哪里投票。只要你有办法让这些文件可以被访问，任何能够提供静态内容的网站都可以用于这个目的(比如 IPFS)。
2.  通过让客户端做所有繁重的工作(计算散列和解密)来防止 DDOS 攻击。服务器只需要能够高效地提供静态文件。

## 那么…可以吗？

让我们看看:

首先，即使恶意用户能够“解密”整个数据库，他们能够获得的唯一信息是身份证的最后 6 个字符，以及一个出生日期和一个邮政编码。我很难想到这些信息可能会被恶意利用。没有名字，没有地址，没有社会保险号，等等。

唯一有点敏感的数据是国民身份证号码，前 3 个数字被删除了。因此，如果恶意用户可以通过暴力攻击获得一些记录，他们也不会得到多少。有必要指出这种情况，因为国家 Id 的最后一位是一个字母，它是使用数字的`mod(23)`计算出来的。因此，攻击者能够将范围缩小到大约 43 个可能的候选对象，而不是 1000 个。

## “解密”所有数据需要什么？

如果攻击者想通过暴力攻击获得全部或大部分数据，该怎么办？他们需要多少样本来比对？

*   国家 ID 的 5 个数字= 100000
*   国家 ID 的一个字母= 23
*   邮政编码:我查过，在加泰罗尼亚有 1146 个不同的邮政编码。
*   出生日期:选民必须在 2017 年 10 月 1 日年满 18 岁。假设攻击者想要将 18 岁到 75 岁之间的所有人作为目标:`(75 – 18) * 365 = ~20,805`

因此，为了“解密”大部分数据，攻击者需要对大约 54，837，819，000，000 个密钥进行暴力攻击。我现在的电脑需要大约 850 年才能处理所有这些密钥。

当然，可以尝试对“低挂果实”进行暴力攻击:人口较多的邮政编码，同时缩小 30-50 岁之间的年龄范围。所以，是的，有可能得到一些记录。虽然，很可能用处不大。

## 我为什么要发表这个？

主要是因为我觉得这很有意思，想知道大家对这个方法的看法。

完全披露:我不是民族主义者，但我支持独立。我希望公投能够举行，但我确实重视隐私(T2)和安全(T4)。因此，对我来说，确保这种方法是安全的是最重要的。