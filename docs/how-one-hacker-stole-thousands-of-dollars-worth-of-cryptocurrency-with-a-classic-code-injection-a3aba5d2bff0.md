# 一个黑客如何在 EtherDelta 上用经典的代码注入黑客窃取了价值数千美元的加密货币，你能从中学到什么

> 原文：<https://medium.com/hackernoon/how-one-hacker-stole-thousands-of-dollars-worth-of-cryptocurrency-with-a-classic-code-injection-a3aba5d2bff0>

![](img/9217c79a69a2f809f2f32ae299a362d0.png)

EtherDelta 团队已经修复了本帖中详述的攻击。我分享这个故事是为了给 Dapp 开发者和加密货币用户提个醒。

2017 年 9 月 24 日我了解到一个恶意代码注入，它允许黑客从多个受害者的钱包中窃取私钥，然后手动从这些钱包中取出资金。我将尝试描述这次攻击，使之成为可能的安全漏洞，以及尽可能多的关于攻击者的信息。

# 一些背景

对于那些不知道的人来说，EtherDelta 是一种用于以太坊和 ERC20 兼容令牌(已在以太坊区块链上部署的令牌)的加密货币交易所。这些令牌可以通过以太坊钱包和智能合约进行存储和转移，整个以太坊交易所运行在一个智能合约上，您可以在这里查看:

[https://ethers can . io/address/0x8d 12a 197 CB 00d 4747 a1 Fe 03395095 ce 2 a5 cc 6819 #代码](https://etherscan.io/address/0x8d12a197cb00d4747a1fe03395095ce2a5cc6819#code)

EtherDelta 是一个聪明的交换——它不需要传统的服务器架构，因为后端架构是部署在以太坊区块链上的智能合约。从加密货币这个词的意义上来说，这是一个真正的 Dapp 或分布式应用程序。当用户在 EtherDelta 上“交易”时，他们必须要么创建一个可以用来与智能合约交互的钱包，要么将他们现有的钱包连接到 EtherDelta 以与智能合约交互。EtherDelta 前端的功能与 MyEtherWallet.com 非常相似，因为您在浏览器中加载的网站是一个完整的钱包管理应用程序，它也公开了来自 EtherDelta 智能合约的方法。因此，当使用该网站时，EtherDelta 的用户必须输入他们的公共钱包地址和*私钥*，这意味着他们的私钥可能会被恶意代码注入从浏览器会话中捕获。

简而言之，当你将资金存入传统交易所时，你是在将资金托付给交易所的钱包或智能合约。如果交易所决定抢劫它的用户，或者因为非法行为被关闭，你会失去你的钱。当您使用 EtherDelta 时，您将钱包的私钥(该密钥可以让任何人从您的钱包中取走资金)“信任”到浏览器会话，并且您将您的资金“信任”到 EtherDelta 智能合约。对于 EtherDelta，您可以在 GitHub 上阅读该站点的完整源代码:

[https://github.com/etherdelta/etherdelta.github.io](https://github.com/etherdelta/etherdelta.github.io)

你可以通过上面的链接阅读智能合约的全部代码。因此，你可以验证该服务没有以任何方式将你的数据或资金转移到你的控制之外……但仍然存在风险。这些风险分为两类:

1.有人可能会欺骗你访问一个使用不同智能合约的 EtherDelta 的假克隆，当你向它转账时，它会窃取你的资金。
2。有人可以向真正的以太网注入代码，从浏览器会话中“嗅探”私钥，让他们无限制地访问你的钱包。*这就是本文详述的攻击类型。*

我想明确一点:我认为，从概念上讲，EtherDelta 比传统的交易所更安全，更“值得信赖”。关于 EtherDelta 如何工作的一切对用户来说都是透明和可验证的。该服务在执行买卖订单的用户之间创建了一个不可信的、纯粹基于软件的接口，并且除了记录在以太坊区块链上的转账之外，不保留这种行为的记录。这篇文章中详细描述的攻击可能在被利用之前就被任何人发现了，如果有一个安全审查协议，它就很容易被阻止。此外，一旦报告给 EtherDelta 团队，它会在几个小时内被修补。

# 漏洞

EtherDelta 允许用户交易任何 ERC20 令牌。平台上正式上市的代币有很多；这些令牌的 URL 如下所示:

[https://etherdelta.com/#LINK-ETH](https://etherdelta.com/#LINK-ETH)

对于网站没有正式列出的任何代币，您仍然可以使用 ERC20 代币合同(用于在以太坊区块链上创建代币并将其分发给用户的起源合同)的地址进行交易。为此，您只需修改 URL 以包含该地址，如下所示:

[https://ether delta . com/# 0x 514910771 af 9 ca 656 af 840 dff 83 e 8264 ECF 986 ca-ETH](https://etherdelta.com/#0x514910771af9ca656af840dff83e8264ecf986ca-ETH)

在这种情况下，上面的两个 URL 用于同一个令牌 ChainLink。您可以在此阅读 ChainLink 的 ERC20 令牌合同:

[https://ethers can . io/token/0x 514910771 af 9 ca 656 af 840 dff 83 e 8264 ECF 986 ca](https://etherscan.io/token/0x514910771af9ca656af840dff83e8264ecf986ca)

对于每个令牌，EtherDelta 界面在屏幕顶部显示令牌的名称。对于未列出的令牌，它将显示令牌合约的地址(以 0x514…开头的长字符串)。在某个时候，EtherDelta 团队决定取消令牌合同的名称并将其显示在 EtherDelta 界面中，因此页面显示“ChainLink Token”而不是“0x 514910771 af 9 ca 656 af 840 dff 83 e 8264 ECF 986 ca”。

从网页外部获取任何内容并将其显示给用户(无论该内容是用户输入的还是从其他来源复制的，如数据库、API 或其他网站),都有可能产生*注入漏洞*。Web 开发人员通常使用验证方法来确保显示的内容只是数字、字母或可接受的字符范围，或者会显式地剥离或修改某些类型的内容(如用于 HTML 标记的< >或用于 JavaScript 代码的【】),以防止显示的内容实际上作为实时代码执行。

我想你能看出这是怎么回事。

# 发生了什么

攻击者通过 Discord 和 Slack 上的加密货币聊天室获得了用户的信任，并向这些用户发送了一个 EtherDelta 上未列出令牌的链接。他还在 Gitter 支持的官方 EtherDelta 聊天中发布了这个链接。该链接的 URL 中的合同地址是攻击者部署的恶意合同，其中合同的名称包含一段 JavaScript 代码。当合同名称显示在页面上时，JavaScript 代码也被“显示”出来并被执行，可以完全访问用户在 EtherDelta 上的会话中的数据。以下是被执行的恶意合同的代码:

> f `[]数据

任何 web 开发人员都会立即看到这个脚本在做什么。对于那些只看到希腊语的人，代码从浏览器会话中读取用户钱包的私钥，然后将这些密钥发送到远程 PHP 脚本，攻击者可能使用该脚本收集这些密钥，然后手动加载钱包并将资金转移到其他钱包。受害者甚至没有意识到这种攻击正在发生(在 EtherDelta 接口中已经有许多 JavaScript 在运行，受害者不会想到寻找被传输到远程位置的数据)。此外，攻击者用来收集用户资金的钱包不同于用来将代码注入 EtherDelta 接口的恶意合同，因此当受害者跟踪交易以查看他们的资金去向时，他们无法确定最初是什么让攻击者获得了他们的资金。为了找到这次攻击的“确凿证据”，其中一名受害者不得不回到恶意链接，复制合同地址，粘贴到 Etherscan 中，并彻底阅读合同源代码，以找到这个代码块。这个受害者不知道这个代码是做什么的，只知道它看起来很可疑，所以他在我经常去的一个加密货币聊天室分享了它，我立即意识到这个代码的能力，并向他解释了它。此时，EtherDelta 团队已经在研究一个解决方案，他们在这里宣布:

哦，如果你想知道的话，这位受害者被盗走了价值约 6000 美元的加密货币。到目前为止，还没有为他追踪漏洞的努力提供 bug 赏金。

更新:我在后续的帖子中收集了更多关于恶意合同及其背后黑客的信息:“[追踪](/@decktonic/following-the-trail-what-we-know-about-the-hacker-behind-the-etherdelta-attack-9ac6015fc2e1)”

# 要吸取的教训

让这成为每个人的警示故事。

你是 Dapp 开发者吗？不可信的软件需要不可信的心态。牢记墨菲定律:*凡是可能出错的，都会出错。不要假设你所依赖的任何东西都是“安全的”采取必要的措施，尽可能“隔离”自己的软件。这包括验证和消毒所有输入以及无数的其他措施。这对于财务软件来说是势在必行的。加密货币服务的安全性和可靠性应该与用户对银行的期望相同。无论如何，用多一双眼睛来验证你的假设。雇佣某人(或多人)对你的软件进行安全审计，并测试每一个可能的场景。由于您自己的疏忽导致的恶意行为可能会导致客户流失，不值得冒这个风险。此外，请记住，这种攻击部分是由于试图使 EtherDelta 更方便(显示人类可读和可识别的令牌名称，而不是合同地址)。任何使产品更好或更方便的东西都有风险。在做出哪怕是最小的改变之前，确保你知道其中的风险。*

你是加密货币用户吗？

*   不要点击你不知道的链接。如有必要，自己在浏览器中键入链接。
*   对于敏感的用例，使用单独的浏览器会话。例如，你可以[在谷歌浏览器](https://support.google.com/chrome/answer/6130773?co=GENIE.Platform%3DDesktop&hl=en)中打开一个访客会话，该会话不会从你的常规浏览会话中访问你的任何用户数据。
*   使用单独的钱包在 EtherDelta 上进行交易，该钱包仅包含您交易所需的资金。理想情况下，您计划交易的每个 ERC20 代币使用不同的钱包。用一个“冷”钱包存放你打算长期储存的资金。这样，如果你的一个钱包受损，你不会一下子失去所有的资金。
*   利用 EtherDelta 的“忘记钱包”功能，经常使用。每次使用完 EtherDelta 后，请考虑“忘记”您的导入钱包。这样，如果你碰巧加载了一个受损版本的 EtherDelta，你就不会让浏览器中的数据随时被窃取。(这就是为什么 MyEtherWallet 不会在两次会话之间“记住”你的数据是件好事。)
*   尽你所知，确保你了解你使用的每一个软件。了解 MyEtherWallet 和 EtherDelta 这样的网站是如何工作的。了解自己的钱包是如何工作的。人们无时无刻不在被攻击者利用我们认为是“安全的”系统中的漏洞进行在线和离线攻击。这些问题不是新的，也绝对不是唯一的。你了解得越多，并采取必要措施防止自己成为受害者，就越好。

请与他人分享，这样他们也可以学习。我们都在一起！注意安全。

更新 9/27:我收到了一个请求，要求证明对 EtherDelta 代码库所做的修改已经修复了这个 bug。由于 EtherDelta 代码库是以缩小的格式发布到 GitHub 的(整个 JavaScript 代码库被混淆并压缩到一行)，我认为很难找到变化，但我想消除所有怀疑的可能性，所以我继续挖掘它。

首先，我必须从 9 月 24 日之前和期间发生的提交中删除`main.js`文件。这些是:

[](https://github.com/etherdelta/etherdelta.github.io/commit/2cfe201318fdcb3331cba9edd243d8d6248281ed) [## 更新 ether delta/ether delta . github . io @ 2 cfe 201

### 在 github 上创建一个帐户，为 etherdelta.github.io 开发做贡献。

github.com](https://github.com/etherdelta/etherdelta.github.io/commit/2cfe201318fdcb3331cba9edd243d8d6248281ed) 

和

[](https://github.com/etherdelta/etherdelta.github.io/commit/76df48903b41565837c41990b87598b9e970916e) [## 更新 ether delta/ether delta . github . io @ 76df 489

### 在 github 上创建一个帐户，为 etherdelta.github.io 开发做贡献。

github.com](https://github.com/etherdelta/etherdelta.github.io/commit/76df48903b41565837c41990b87598b9e970916e) 

为了缩小`main.js`，我在命令行上使用了[js-美化](https://www.npmjs.com/package/js-beautify)。我们只能说这花了一段时间，因为文件超过 2 MB。

然后，我不得不手动搜索关键字，这些关键字可能指向代码从自定义契约中获取信息的位置……比如令牌、地址、自定义等。在搜索了很多次“地址”之后，我终于找到了一个使用 web3 API(一个用于与 IIRC 区块链以太坊交互的 API)解析 ERC20 令牌契约的函数。这就是:

```
const result = JSON.parse(body);
const functionAbi = contract.abi.find(element => element.name === functionName);
const solidityFunction = new SolidityFunction(web3.Eth, functionAbi, address);
const resultUnpacked = xss(solidityFunction.unpackOutput(result.result));
```

在提交`76df489...`中，一个额外的函数调用`xss()`被添加到解包远程令牌契约的`abi`的步骤中。在前面的提交中，这一部分是如何编写的:

```
const result = JSON.parse(body);
const functionAbi = contract.abi.find(element => element.name === functionName);
const solidityFunction = new SolidityFunction(web3.Eth, functionAbi, address);
const resultUnpacked = solidityFunction.unpackOutput(result.result);
```

(我不会公布未缩小的文件，但欢迎您从 EtherDelta GitHub 资源库下载`main.js`并自行缩小来查看。)

在搜索这种新方法的存储库时，我还发现了一个报告给问题跟踪者的问题，这是基于另一种攻击:通过 URL 直接加载自定义 Javascript。我甚至不知道这是可能的，但这就是:

[](https://github.com/etherdelta/etherdelta.github.io/issues/143) [## 解析并执行 GET 查询参数中的标记问题# 143 ether delta/ether delta . github . io

### 当访问 EtherDelta 时，执行附加到查询参数的标签。结合私钥是…

github.com](https://github.com/etherdelta/etherdelta.github.io/issues/143) 

如前所述，用于防止这类攻击的新库是 js-xss:

[](https://github.com/leizongmin/js-xss) [## 雷宗敏/js-xss

### js-xss -用白名单指定的配置净化不可信的 HTML(防止 xss)

github.com](https://github.com/leizongmin/js-xss) 

在研究过程中，我意识到 EtherDelta 的基本运行方式是从远程位置加载定制代码(在本例中，是在区块链上发布的智能合约)。对于那些有 web 应用程序开发经验的人来说，您可能已经知道，任何涉及从远程位置加载和执行自定义代码的场景都被认为是非常危险的，尤其是当无法知道谁对正在加载的远程代码负责时。显然，对于 EtherDelta，最终用户有责任决定他们希望加载哪个远程智能契约(或者，避免来自恶意个人的可疑 URL)。EtherDelta 应用程序完全在用户的浏览器中运行，因此用户数据受损的唯一方式是他们自己提供数据(通过导入他们的钱包)以及他们通过自己的行为暴露自己(通过点击恶意链接)。也就是说，绝大多数用户永远不会想到使用像 EtherDelta 这样的网站会有这种风险(我有计算机工程学位，甚至我不知道这种漏洞是可能的，但我很高兴我了解了这一点)。

我还收到了一些用户的问题，他们很想知道这个漏洞是否会影响 Metamask 或 Ledger wallets。我可以毫无疑问地说，Metamask 和 Ledger 都是安全的，因为它们都只公开了 EtherDelta 用来与用户钱包交互的 API，而不是使用用户的私钥直接加载用户的钱包。基本上，如果你想绝对确定你的私钥没有被暴露的风险，要么使用安全的应用程序/设备，如 Metamask 或 Ledger，要么绝对确定你是以安全的方式访问 EtherDelta.com。

感谢所有分享这篇文章的人。至少，我希望这对每个人都是一个教育警示。

*p.s .据我所知，此次攻击的受害者没有一人能够从 ED 处追回资金或获得赔偿，ED 也没有向任何人发放 bug 赏金。*

这些内容有帮助吗？留下小费来表达你的感激之情:

BTC:16 phidsknyjccf 6d 4 sq BD 5 pmxpnxihts

ETH:0x 4 ebee 6 ba 2771 c 19 ADF 9 af 348985 BCF 06d 3270d 42

DOGE:dpwkqr 5 rhwcceceyadxvhnzwmamhbs 5 IP 5g

谢谢！