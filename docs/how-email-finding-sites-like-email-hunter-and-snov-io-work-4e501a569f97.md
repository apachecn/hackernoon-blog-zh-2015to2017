# 像 Email Hunter 和 Snov.io 这样的邮件查找网站是如何工作的

> 原文：<https://medium.com/hackernoon/how-email-finding-sites-like-email-hunter-and-snov-io-work-4e501a569f97>

## 了解如何创建自己的电子邮件查找服务。

![](img/f40a5fbeeb919e319add4f77f165564a.png)

How else do we ask Bill Gates to invest in our ill-conceived startups?

他们猜测并验证。他们通过询问和心理虐待邮件服务器来达到目的。大多数邮件服务器是无辜的，友好的生物。说`HELO`，他们会立即回复一个`Hello`，除非他们疯了，或者死了。

告诉他们你是谁，他们会兴奋地回应一句`OK`。

告诉他们你需要给谁发邮件，他们会告诉你收件人的地址是否正确(小心，有时狡猾的人会撒谎)。

电子邮件查找网站利用这种友好性来猜测和验证电子邮件地址。

他们需要知道联系人的全名和他们的公司域名。所以如果我们要在微软寻找比尔·盖茨，我们需要一台时间机器。但是如果我们需要他在微软的电子邮件地址，我们需要知道他的全名是比尔·盖茨，他公司的域名是 microsoft.com。

然后，我们使用这些信息生成一个电子邮件地址列表以供猜测。大多数大公司都有标准的电子邮件地址模式。以下是最常见的几种:

*   [billgates@microsoft.com](mailto:billgates@microsoft.com)
*   [bill.gates@microsoft.com](mailto:bill.gates@microsoft.com)
*   [bill_gates@microsoft.com](mailto:bill_gates@microsoft.com)
*   [bill-gates@microsoft.com](mailto:bill-gates@microsoft.com)
*   [gatesbill@microsoft.com](mailto:gatesbill@microsoft.com)
*   [gates.bill@microsoft.com](mailto:gates.bill@microsoft.com)
*   [gates_bill@microsoft.com](mailto:gates_bill@microsoft.com)
*   [gates-bill@microsoft.com](mailto:gates-bill@microsoft.com)
*   [bill@microsoft.com](mailto:bill@microsoft.com)
*   【gates@microsoft.com】
*   [billg@microsoft.com](mailto:billg@microsoft.com)
*   [bill.g@microsoft.com](mailto:bill.g@microsoft.com)
*   [bill_g@microsoft.com](mailto:bill_g@microsoft.com)
*   [bill-g@microsoft.com](mailto:bill-g@microsoft.com)
*   [gatesb@microsoft.com](mailto:gatesb@microsoft.com)
*   gates.b@microsoft.com
*   【gates_b@microsoft.com 
*   [gates-b@microsoft.com](mailto:gates-b@microsoft.com)
*   [bgates@microsoft.com](mailto:bgates@microsoft.com)
*   [b.gates@microsoft.com](mailto:b.gates@microsoft.com)
*   [b_gates@microsoft.com](mailto:b_gates@microsoft.com)
*   [b-gates@microsoft.com](mailto:b-gates@microsoft.com)
*   [bg@microsoft.com](mailto:bg@microsoft.com)
*   [gb@microsoft.com](mailto:gb@microsoft.com)

我们需要找出哪个是真正的比尔·盖茨。让我们给他们发电子邮件来找出答案。不是！

这正是我们要做的。给他们发邮件。不是！

我的意思是我们会假装给他们所有人发一封电子邮件——那就不要！

当您请求邮件服务器为您发送电子邮件时，邮件服务器会告诉您收件人的电子邮件地址是否正确。一旦开始，你就挂断。

很粗鲁，不是吗？！想象一下被那样对待。如果邮件服务器发现你试图利用他们，他们会停止与你交谈。甚至告诉其他邮件服务器不要理你。邮件服务器通常与同类服务器联系紧密。

简而言之，这就是如何使用 **TELNET** 来猜测电子邮件地址。TELNET 是一种与服务交互的方式，有点像电话。你给某人打电话；如果他们在那里，他们会拿起话筒回应。如果他们不想回答，他们会忽略你的电话。如果他们接电话，你可以开始和他们交流。要么让他们做点什么，要么去打听消息。但是你需要说他们能理解的语言。否则，他们将无法回应。邮件服务器理解 SMTP，这是一种通过邮件服务器发送和接收邮件的协议。SMTP 连接通常在端口 25 上进行。端口就像电话分机。

现在，我们需要使用 TELNET 连接到微软的邮件服务器，并通过向邮件服务器发送 SMTP 消息来验证比尔·盖茨可能的电子邮件地址。当我们使用 TELNET 连接到邮件服务器时，我们会收到一个提示，提示我们准备好接受 SMTP 消息。

好吧，让我们来看看实际情况。启动你的命令提示符(Windows) /终端(OS X / Linux)。大多数操作系统都内置了 TELNET，所以您可能不需要安装它。如果 TELNET 命令不起作用，请搜索您的操作系统的安装过程。TELNET 的语法很简单。

> 这些例子都运行在 Mac OS X 终端上。

```
**telnet HOST PORT**
```

我们的`HOST`这里是我们的邮件服务器，`PORT`是`25`(记住，邮件服务器可以连接到端口 25)。我们来和`microsoft.com`比一比。

```
**telnet microsoft.com 25** Trying 104.43.195.251...
```

它会像这样保持一段时间，什么也不做。点击`Ctrl+C`关闭连接。因为这是错误的。邮件服务器通常位于一个子域或另一个完全不同的地址。很多公司甚至没有自己的邮件服务器；他们订阅邮件提供商，如 GMail、Outlook365、Zoho、Yandex 等。

那么我们如何找出微软的邮件服务器呢？很简单。像其他网站一样，微软公开了它。每个网站都有自己的名为 **DNS** 的地址簿，上面列出了相关的地址和端口。他们的邮件服务器也列在 DNS 中。

为了找到它，我们需要使用另一个名为 **DIG** 的命令。把这个命令想象成一个地址簿查找。在 Windows 上，默认情况下不安装 DIG，您可以自己安装，或者使用 **NSLOOKUP** 命令。

邮件服务器被分类为称为 MX 记录的记录。MX 代表邮件交换器。因此，让我们看看如何使用 DIG 和 NSLOOKUP 命令来查找微软的 MX 记录，从而找到邮件服务器。

DIG 的语法:

```
**dig [CATEGORY] HOST**
```

我们这里的`CATEGORY`是`MX`，而我们的`HOST`是`microsoft.com`。`CATEGORY`是可选的，你可以省去它并获得所有列出的 DNS 记录。不过我们将使用`MX`来运行它。

```
**dig mx microsoft.com** ; <<>> DiG 9.9.5–3ubuntu0.15-Ubuntu <<>> mx microsoft.com
;; global options: +cmd;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 23550
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096;; QUESTION SECTION:
;microsoft.com. IN MX;; *ANSWER SECTION*:
microsoft.com. 60 IN MX 10 microsoft-com.mail.protection.outlook.com.;; Query time: 13 msec
;; SERVER: 172.31.0.2#53(172.31.0.2)
;; WHEN: Wed Nov 01 14:59:20 UTC 2017
;; MSG SIZE rcvd: 96
```

有很多奇怪的代码和数字。别担心，我们现在只对`ANSWER SECTION`感兴趣。邮件服务器就列在那里。

大多数网站通常都有不止一个邮件服务器，以防其中一个关闭或由于某种原因无法访问。所以不要惊讶在那里发现多个条目。出于我们的目的，您可以选择列表中的第一个。

我们可以在 DIG 命令中添加一些选项，只得到答案部分，而没有其他晦涩难懂的内容。

```
**dig +noall +answer mx microsoft.com** microsoft.com.    60   IN    MX   10 microsoft-com.mail.protection.outlook.com.
```

现在，我们有了答案。但看起来不像是有效地址。那是因为它提供的不仅仅是我们的地址。只有最后一部分`microsoft-com.mail.protection.outlook.com`是我们的邮件服务器地址(不包括尾随点)。

如果没有 DIG，可以使用 NSLOOKUP 命令:

```
**nslookup -q=mx microsoft.com** Server: 172.31.0.2
Address: 172.31.0.2#53Non-authoritative answer:
microsoft.com mail exchanger = 10 microsoft-com.mail.protection.outlook.com.Authoritative answers can be found from:
```

同样，只有答案的最后一部分没有尾随点是我们的邮件服务器— `microsoft-com.mail.protection.outlook.com`

好了，现在我们有了目标邮件服务器。让我们测试一下之前失败的 TELNET 命令。只是这一次，我们使用邮件服务器作为`HOST`，而不是网站地址。

```
**telnet microsoft-com.mail.protection.outlook.com 25** Trying 23.103.156.42…
*Connected* to microsoft-com.mail.protection.outlook.com.
Escape character is ‘^]’.
220 BL2NAM06FT016.mail.protection.outlook.com Microsoft ESMTP MAIL Service ready at Wed, 1 Nov 2017 15:13:16 +0000
```

有用！

我们看到…一些胡言乱语。但是我们可以看到它是连接的，它在等待我们写点什么。邮件服务器收到了我们的电话。它已经准备好和我们说话了。但是我们说什么呢？记住…邮件服务器理解 SMTP 语言。让我们用 SMTP 问候他们。说`HELO`。

```
**HELO**
250 BL2NAM06FT016.mail.protection.outlook.com *Hello* [54.149.XXX.XXX]
```

上面写着`Hello`。它喜欢我们…到目前为止。

是时候变得刻薄了！让我们假设通过邮件服务器发送一封电子邮件。看看它能否告诉我们比尔·盖茨的电子邮件地址。首先，我们需要给它我们自己的电子邮件地址。如果我们真的想发送电子邮件，这将是来自电子邮件地址的*。要指定来自*的*电子邮件地址，您需要使用以下语法:*

```
**MAIL FROM: <EMAIL@DOMAIN>**
```

邮件服务器不读新闻。他们不知道史蒂夫·乔布斯已经去世了。所以我们现在可以做史蒂夫·乔布斯。

```
**MAIL FROM: <**[**stevejobs@apple.com**](mailto:stevejobs@apple.com)**>** 250 2.1.0 *Sender OK*
```

发送方正常。看，它不知道。现在，我们终于可以开始猜测…

下面是描述收件人地址的语法:

```
**RCPT TO: <EMAIL@DOMAIN>**
```

我们将根据最常见的电子邮件模式列表(如上所列)测试各种地址。

```
**RCPT TO: <**[**billgates@microsoft.com**](mailto:billgates@microsoft.com)**>** 550 5.4.1 [[billgates@microsoft.com](mailto:billgates@microsoft.com)]: Recipient address rejected: Access denied [BL2NAM06FT016.Eop-nam06.prod.protection.outlook.com]
```

收件人地址被拒绝。看起来那不是比尔·盖茨的电子邮件地址。让我们多试几个…

```
**RCPT TO: <**[**bill.gates@microsoft.com**](mailto:bill.gates@microsoft.com)**>** 550 5.4.1 [[bill.gates@microsoft.com](mailto:bill.gates@microsoft.com)]: Recipient address rejected: Access denied [DM3NAM06FT009.Eop-nam06.prod.protection.outlook.com]
**RCPT TO: <**[**bill_gates@microsoft.com**](mailto:bill_gates@microsoft.com)**>** 550 5.4.1 [[bill_gates@microsoft.com](mailto:bill_gates@microsoft.com)]: Recipient address rejected: Access denied [DM3NAM06FT009.Eop-nam06.prod.protection.outlook.com]
**RCPT TO: <**[**bill-gates@microsoft.com**](mailto:bill-gates@microsoft.com)**>** 550 5.4.1 [[bill-gates@microsoft.com](mailto:bill-gates@microsoft.com)]: Recipient address rejected: Access denied [DM3NAM06FT009.Eop-nam06.prod.protection.outlook.com]
**RCPT TO: <**[**bill-gates@microsoft.com**](mailto:bill-gates@microsoft.com)**>** 550 5.4.1 [[bill-gates@microsoft.com](mailto:bill-gates@microsoft.com)]: Recipient address rejected: Access denied [DM3NAM06FT009.Eop-nam06.prod.protection.outlook.com]
**RCPT TO: <**[**billg@microsoft.com**](mailto:billg@microsoft.com)**>** 250 2.1.5 *Recipient OK*
```

这是一个*宾果*！我们在微软找到了比尔·盖茨的电子邮件地址:[billg@microsoft.com](mailto:billg@microsoft.com)。

你现在可以找到任何人的电子邮件地址，以及主要的电子邮件查找服务。但是正如你所看到的，这个过程有点复杂。大量的打字和中间的等待。

所以请继续关注下一篇文章，在那里我将解释如何完全自动化这个过程(包括访问完整的代码)。

然后在之后的文章中，我将描述如何扩展服务来处理成千上万的邮件猜测，以及在这个过程中你将面临什么样的挑战。

*免责声明:本文所表达的观点均为本人观点。没有人付钱给我写这篇文章。据我所知，我与文章中提到的任何服务、商标或公司没有任何关联或竞争关系。*