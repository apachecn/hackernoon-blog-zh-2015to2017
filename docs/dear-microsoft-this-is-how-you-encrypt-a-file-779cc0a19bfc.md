# 亲爱的微软，这是你加密文件的方式

> 原文：<https://medium.com/hackernoon/dear-microsoft-this-is-how-you-encrypt-a-file-779cc0a19bfc>

在我的上一篇文章中，我对一篇 13 年前的 MSDN 文章感到惊讶，这篇文章竟然出自一个不熟悉加密初始化向量正确用法的人之手。我的一些粉丝很友好地指出，在我戏剧性的咆哮和胡言乱语中，我忘了说任何有用的东西，比如你如何*实际上*应该加密一个文件。

所以，废话不多说，下面是你如何加密和解密一个文件:

```
$ gpg -c --cipher-algo AES128 [file]  # Encrypt
$ gpg -d [file].gpg  # Decrypt
```

虎头蛇尾，对吧？它甚至没有提到静脉注射，但这是关键。包含手动处理 IVs 的文件加密教程有点像包含重新实现 TCP 的设置 web 服务器的教程。这样做毫无益处，而且你几乎肯定会做错。这就是*加密文件*和*实现文件加密*的区别。第一个是程序员的常见任务，第二个不应该由非密码专家来尝试。微软应该知道不要把这两者混为一谈，尤其是在面向外行读者的教程中。

## GPG？

这篇文章不应该是关于 GPG 的教程，但是如果有人真的想接受我的建议，请查看文档。如果你想用[target[programming](https://hackernoon.com/tagged/programming)language]进行文件加密，只需用该语言的 OpenPGP 实现进行相同的操作。

## 哇所以 PGP 是万无一失的？

哦，上帝，不。它有许多缺陷，其中最大的可能是可怕的密钥分发。它恰好有一个体面的文件加密实现，可以在几乎每种平台和几乎每种编程语言上使用。如果你想彻底搞清楚这个话题，这里有[马修·格林](https://blog.cryptographyengineering.com/2014/08/13/whats-matter-with-pgp/)和[菲利普·瓦尔索达](https://arstechnica.co.uk/security/2016/12/op-ed-im-giving-up-on-pgp/)拉屎满 PGP。

## 嘿，我是来了解加密细节的！

好了，现在我们都清楚了，当你想加密一个文件时，你应该只使用一个主流的、高级的加密库，而不要自己接触原语，这里有一个实现文件加密的基本方法，它并不完全糟糕:

1.  选择安全的现代分组密码。我会坚持使用 AES128，除非你有真正令人信服的理由使用其他东西。如果你愿意，你可以使用 AES256，但实际上它不会增加[安全性](https://hackernoon.com/tagged/security)。你的密码没有接近 128 位的熵，所以它是这个链条中最薄弱的一环。
2.  获取加密安全的随机值以用作初始化向量。(提示:使用 [/dev/urandom](https://security.stackexchange.com/a/3939) )
3.  获取高熵密钥。在这种情况下，我会说选择一个强密码(完全是另一个话题)，然后通过 [bcrypt](https://en.wikipedia.org/wiki/Bcrypt) 运行它以进行[键拉伸](https://en.wikipedia.org/wiki/Key_stretching)。为了简单起见，你可以用 IV 作为盐。
4.  使用您的块密码、通过 bcrypt 发送密码创建的密钥和/dev/urandom 生成的 IV，执行[密码块链接](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher_Block_Chaining_.28CBC.29)来加密您的明文。
5.  将 IV 附加到密文的开头，然后通过 [HMAC](https://en.wikipedia.org/wiki/Hash-based_message_authentication_code) 运行整个过程，最好使用 SHA2 或 SHA3。请注意，我们在加密后进行认证，避免了[毁灭法则](https://moxie.org/blog/the-cryptographic-doom-principle/)。
6.  把 HMAC 的输出附加到 IV 和密文上，并输出结果。所以你的输出应该是 IV || CT || HMAC(IV || CT)

这就对了，用 6 个简单的步骤实现文件加密。如果您需要一个包含这里提到的原语的库，OpenSSL 是一个不错的选择。如果您想解密，请通过从 IV || CT 重新计算 HMAC 并与给定的 HMAC 进行比较来验证它，然后只需撤消 CBC 加密。

## 太酷了…但是我们为什么要做那些事情呢？

随机化的 IV 和密码块链接实现了语义安全，这样我们就不会泄露信息。HMAC 实现[数据完整性](https://en.wikipedia.org/wiki/Data_integrity)和[认证](https://en.wikipedia.org/wiki/Authentication)。使用 bcrypt 进行密钥拉伸会使从您的密码生成的密钥不那么可怕。

如果你想通过编程来学习这些东西(你可以容忍一个由黑客设计 UI 的 webapp ),去看看 https://id0-rsa.pub/吧。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)，[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！