# SSH 密钥:入门

> 原文：<https://medium.com/hackernoon/ssh-keys-a-primer-7ac8b790e849>

身份验证、授权以及两者之间的一切

SSH 密钥很复杂，我明白。但这并不是因为它实际上很复杂…而是因为它们背后的基本原理并不清楚。为了理解 SSH [键](https://hackernoon.com/tagged/keys)，我们将首先定义这个问题:

*   **主机密钥验证** —客户端应该知道他试图连接的服务器确实是其声称的服务器
*   **基于密钥的认证**——服务器必须知道试图连接的用户是谁，以及他是否被允许这样做

# **主机密钥验证**

你记得收到这条信息吗？

```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that the RSA host key has just been changed.
The fingerprint for the RSA key sent by the remote host is
3f:1b:f4:bd:c5:aa:c1:1f:bf:4e:2e:cf:53:fa:d8:59.
Please contact your system administrator.
Add correct host key in /home/ron/.ssh/known_hosts to get rid of this message.
Offending key in /home/ron/.ssh/known_hosts:3
RSA host key for 192.168.0.100 has changed and you have requested strict checking.
Host key verification failed.$
```

SSH 提供了一种机制来验证您试图连接的服务器实际上是它所声称的主机。这是通过以下[算法](https://hackernoon.com/tagged/algorithm)完成的:

1.  当 SSH 服务器初始化时，它会创建一个主机密钥，这是一个公钥/私钥对。SSH 服务器向任何连接到它的人分发公钥
2.  您的 SSH 客户机检查您试图连接的主机是否在~/中有一个主机密钥。ssh/已知主机文件
3.  如果条目不存在，请将主机密钥添加到~/中。ssh/已知主机文件
4.  如果条目存在，使用主机密钥(这是一个公钥)加密消息，并期望服务器解密它。如果服务器已经成功地解密了消息，那么这意味着服务器持有与给定主机密钥相匹配的私钥，这意味着它就是它所声称的那个人

主机密钥验证机制向客户端承诺，一旦连接到特定的服务器，每次连接到同一个主机名时，都会检查该服务器是否就是您第一次连接的服务器。

如果您要替换服务器，主机密钥将会更改，所有拥有以前密钥的客户端将开始接收此消息，直到它们替换存储的密钥。

# 基于密钥的认证

这是真正的奇迹发生的地方。就像客户机想知道主机是否是他真正声称的那样，这次服务器想知道正在连接的用户是否确实是它声称的那个用户。

基于密钥的身份验证是最好的身份验证方法，因为它不需要密码。但是，如果您丢失了密钥，那么您将无法连接到服务器。

要开始使用基于密钥的身份验证，客户端必须生成一个 SSH 密钥对。密钥不会自动生成，您需要自己生成。要生成密钥，只需运行 OpenSSH 实用程序 ssh-keygen。

在客户端拥有 SSH 密钥对(通常驻留在~/中)之后。ssh/id_rsa 用于 rsa 密钥和~/。ssh/id_rsa.pub)然后，您可以使用您的密钥配置服务器，以允许连接到特定用户。这是通过将客户机的公钥附加到用户的~/上来实现的。服务器上的 ssh/authorized_keys 文件。

**我们来看一个例子:**假设您想使用用户“ron”登录 ssh.foo.com。这意味着您有某种修改/home/ron/的方法。ssh/authorized _ key。您需要获取文件的内容~/。ssh/id_rsa.pub，并将其附加到/home/ron/中。服务器上的 ssh/authorized_keys。

身份验证流程如下所示:

1.  客户端连接到 ssh.foo.com，并通过前面讨论的主机密钥验证过程。
2.  服务器要求输入用户名，客户机用一个用户名作为响应。(“罗恩”)
3.  客户端(我的 MacBook)提供/Users/ron/的公钥。ssh/id_rsa(存储在/Users/ron/中。ssh/id_rsa.pub)
4.  服务器检查公钥是否在允许我们连接到该服务器上的用户帐户的授权密钥列表中(授权密钥在/home/ron/中)。ssh/授权密钥)
5.  如果密钥存在，服务器将使用授权密钥列表中的公钥加密消息，并期望客户端能够解密它，因为它应该有私钥
6.  客户端将从服务器获得加密的令牌，对其进行解密，然后将其发送回去(它实际上发送回了令牌的散列和会话密钥，但这对于本说明并不重要)
7.  服务器将看到令牌被成功解密，并允许用户通过模拟用户以“ron”的身份登录

我希望这篇文章能让你更容易理解 SSH 密钥实际上是如何工作的。尽情享受吧！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)，[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！