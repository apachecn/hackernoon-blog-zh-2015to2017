# 以太坊 Web 开发人员入门

> 原文：<https://medium.com/hackernoon/getting-started-as-an-ethereum-web-developer-9a2a4ab47baf>

![](img/4d73160e8a337b0e9fc60bb06b2fe09a.png)

***更新:*** *我创建了一个* [*回购*](https://github.com/alex-miller-0/eth-dev-101) *供您使用，它展示了本文中涉及的大部分内容。*

我经常构建使用以太坊的 web 应用程序，我认为我把每天使用的令人惊奇的工具集视为理所当然。我们的生态系统正在快速发展，我认为许多新来者感到不知所措。以太坊是一项令人惊叹的技术，但它还处于萌芽阶段，专业知识还没有足够的时间充分渗透。我希望人们知道以太坊开发实际上与现代 web 开发人员的工作流程非常兼容— **将以太坊功能集成到任何 web 应用程序中相对容易，您可以从今天开始**。

因为我幻想自己是以太坊的冠军，肩负着向主流开发者展示光明的使命，所以我决定将一堆分散的知识放在一个地方(我知道不是很分散)。当然，在每一步中，您都需要查阅适当的文档，但是我希望这篇文章将向您展示每一件事情或多或少是如何组合在一起的。

如果你准备好学习，请让我成为你的精神向导。来加入以太坊生态系统，帮助我们征服世界。

# 买一件区块链

有很多客户端可供选择，但是我建议不要担心 [geth](https://github.com/ethereum/go-ethereum/wiki/geth) vs [奇偶校验](https://github.com/paritytech/parity) vs [pyethapp](https://github.com/ethereum/pyethapp) (即将到来的 python 客户端——represent！).对于那些想要一个怪异的[区块链](https://hackernoon.com/tagged/blockchain)来开始构建东西的人(比如你)，我建议 [testrpc](https://github.com/ethereumjs/testrpc) 来满足你所有的开发需求。安装后，您可以通过以下方式启动它:

```
testrpc
```

恭喜你，你现在有一个区块链了。注意，默认情况下 testrpc 不挖掘块，但是`-b`标志允许您指定块间隔(例如 1 秒)。我喜欢这种配置的原因有很多，但我不会深入讨论，但请记住，它是可用的。

# 第二步:与区块链交谈

一旦你让你的区块链运转起来，你需要一个和它交流的方式。你可能已经下载了 [web3.js](https://github.com/ethereum/web3.js/) 。如果你没有，你一定是*真的*新来的。好吧，继续并确保你已经安装了 web3，然后打开一个`config.js`文件并把它放进去:

```
var web3 = require('web3');
var web3_provider = '[http://localhost:8545'](http://localhost');
var _web3 = new web3();
_web3.setProvider(new web3.providers.HttpProvider(web3_provider));
exports.web3 = _web3;
```

任何时候你想和后端服务器上的区块链对话，只要这样做:

```
var config = require('./config.js');config.web3.eth.X
```

`X`(即你想要的任何 web3 API 函数)可以在[这里](https://github.com/ethereum/wiki/wiki/JavaScript-API)找到。

# 第三步:写一些智能合同

我将在这里为您节省一些时间:您将使用 [solidity](http://solidity.readthedocs.io/en/develop/) 来编写智能合同。如果你认为智能承包是可怕的，不要。对于许多应用程序来说，只要你遵循一条规则，它实际上真的很容易:**保持你的合同简单**。

有两个原因让你想让你的合同尽可能的简单明了:

1.  每一次计算/存储操作都要消耗天然气，这就相当于消耗乙醚，这就相当于消耗金钱。我们正在谈论支付 0.05 美元和 1.50 美元来调用您的合同之间的差异。以太坊的重点不是取代你的数据库(至少在我看来不是)，所以保持逻辑简短，存储最小。
2.  越复杂=出错的地方越多。当你的代码要对人们的钱负责，并且不能回滚的时候，这就不好了。请花一分钟让最后一句话被理解。

好的，简单的合同——明白了。我们继续吧。

# **步骤 4:部署那些智能合约**

![](img/4c057fd7641dabd47ec9ad48490e3396.png)

如果你没有听说过松露，你现在绝对应该去看看。我喜欢在一个目录中管理我的测试合同。关于这一点的巧妙之处在于，您可以很容易地将其应用到您的测试框架中。考虑 package.json 中的这个脚本:

```
"scripts": {
  "test": "cd truffle && truffle deploy && truffle test ./myTruffleTest.js && cd .. && npm run myOtherTests"
}
```

这就是 1。部署您的合同，2。运行您的松露测试，3。运行您的常规测试——全部在同一个脚本中！

请注意，您的松露测试是“特殊的”,因为它们将一堆很酷的块状东西注入到您的测试范围中。有多种方法可以将这些信息传递给测试套件的其他部分。我个人使用 truffle 测试将合同地址保存到一个配置文件中，然后将该配置导入到我的常规 mocha 测试中。只要我有正确的地址，我就可以通过 web3.js 在任何测试中与我的合同进行交互。总之，你会找到最适合你的。

回到主秀。您可以通过转到 truffle 目录并键入以下内容来部署您的智能合约:

```
truffle deploy
```

*注意 testrpc 必须在另一个窗口中运行！*

这将打印出您新部署的契约的地址，您稍后会需要它。正如我提到的，您总是可以在 truffle 测试中以编程的方式保存这个地址，但是现在您可以将它复制并粘贴到您的`config.js`文件中:

```
exports.contract_addr = '0xe73e8e0a4442e140aea87a4b150ef07b82492500'
```

# 步骤 5:打一个智能合同电话

现在我们有了合同，我们需要取消它。好吧，这个看起来有点滑稽——我们将用纯十六进制字符串调用契约。当然，有[库](https://github.com/ethereumjs/ethereumjs-abi)可以使这变得更容易，但是当涉及到契约调用时，我就用老方法了。记住，我是你的老师。

首先要注意的是**所有东西都必须是十六进制的**(详见附录)。数字、字符串等。第二个要注意的是以太坊的**字是 256 位**。这意味着您需要用 0 到 64 个字符左填充所有内容。第三件要注意的事情是**类型必须在函数定义中规范化地声明**。

好吧，这越来越像脖子了。让我们看一个例子:

```
function add(uint x, uint y) public constant returns (uint) {
  return x + y;
}
```

假设您想要添加`1`和`2`。下面是调用这个函数的方法:

*1:取紧凑的、规范的函数定义的 keccak 256 散列的前 4 个字节。*

说什么？嗯，这个不是我编的，不过你只要把你的函数声明输入[这个网站](https://emn178.github.io/online-tools/keccak_256.html)就可以了，取前 8 个字符。我说的规范是什么意思？嗯，在以太坊有规范类型和速记类型(例如`uint256`是`uint`的规范类型)。实际上我不知道它们都是在哪里定义的，但是看看[以太坊 ABI 定义](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI)的例子以及[这篇文章](http://ethereum.stackexchange.com/questions/234/what-is-an-abi-and-why-is-it-needed-to-interact-with-contracts)。

无论如何，这就是我们的声明:

```
add(uint256,uint256)
```

它返回 keccak256 哈希:

```
771602f7f25ce61b0d4f2430f7e4789bfd9e6e4029613fda01b7f2c89fbf44ad
```

其中前 4 个字节(8 个字符)是:

```
771602f7
```

*2:将您的参数填充到 256 位*

这个比较容易理解:

x=1 是:

```
0000000000000000000000000000000000000000000000000000000000000001
```

y=2 是:

```
0000000000000000000000000000000000000000000000000000000000000002
```

他们一起是:

```
00000000000000000000000000000000000000000000000000000000000000010000000000000000000000000000000000000000000000000000000000000002
```

*3:把所有东西打包在一起，加上一个* `*0x*` *前缀*

不言自明:

```
0x771602f700000000000000000000000000000000000000000000000000000000000000010000000000000000000000000000000000000000000000000000000000000002
```

—

现在我们有了有效载荷，我们可以调用 web3:

```
var config = require('./config.js');var call = '0x771602f700000000000000000000000000000000000000000000000000000000000000010000000000000000000000000000000000000000000000000000000000000002'var to = config.contract_addr;var res = config.web3.eth.call({ to: to, data: call });
```

之后要拿回`res`的`3`。实际上，你会得到一个`BigNumber`对象:

```
res.toString()
>'3'
```

你可能应该[阅读这个](/@asmiller1989/a-note-on-numbers-in-ethereum-and-javascript-3e6ac3b2fad9)来了解为什么你应该在你的应用中使用`BigNumber` s。

好吧，你可以使用我之前提到的[库](https://github.com/ethereumjs/ethereumjs-abi)。

等等，我们还没说完呢！我刚刚向你展示了如何称之为合同。但是如果你想写它(即更新状态)呢？以上都不行！你需要用你的私钥签署一个交易，但在此之前，你需要一些乙醚。

# 第六步:设置你的账户

让我们回到松露。在我们的测试中，我们需要添加如下内容:

```
var keys = require(`${process.cwd()}/../test/keys.json`);it('Should send me some ether.', function() {
  assert.notEqual(keys.me.addr, null); var eth = 1*Math.pow(10, 18);
  var sendObj = {
    from: accounts[0],
    value: eth,
    to: keys.me.addr
  }

  Promise.resolve(web3.eth.sendTransaction(sendObj))
  .then(function(txHash) {
    assert.notEqual(txHash, null);
    return web3.eth.getBalance(keys.me.addr)
  })
  .then(function(balance) {
    assert.notEqual(balance.toNumber(), 0);
  })
})
```

*重要提示:我们实际上是在发* `*1 ether*` *和* `*10^18 wei*` *一样的。我们的* ***总是*** *用* ***魏*** *的值进行通话/交易。*

现在，我跳过了一个步骤。您需要首先获得一个以太坊帐户，该帐户来自您生成的私有/公共密钥对。我喜欢在后端使用 [eth-lightwallet](https://github.com/ConsenSys/eth-lightwallet) 进行密钥管理。如果你想知道更多的细节/说明，它们在附录里。

为了简单起见，让我们假设我在不断增长的`config.js`中硬编码了这个变量:

```
exports.me = {
  addr: "0x29f2f6405e6a307baded0b0672745691358e3ee6",
  pkey: "8c2bcfce3d9c4f215fcae9b215eb7c95831da0219ebfe0bb909eb951c3134515"
}
```

*义务提醒:* ***千万不要*** *分享你的私钥，上传到 github，或者发布在介质上，如果上面有或将会有任何钱的话。*

回到测试，您可以看到 ether 从默认情况下有一堆 ether 的`accounts[0]`移动到配置文件中的`me.addr`。

# 步骤 7:使用智能合约进行交易

现在你的账户有一些乙醚，是时候花掉了。有三种方式消耗乙醚:

1.  作为`value`发送到另一个地址
2.  调用更新网络状态的契约函数，这需要 gas 来激励矿工处理您的更新。
3.  调用一个更新状态的合同，但也接受乙醚作为付款(仅供参考，solidity 中有一个`payable`修饰符)——`value`将被发送，您还必须支付汽油费。

我们接下来要做的属于第二类。假设我们有下面的函数来跟踪用户的余额:

```
function addUserBalance(uint balance)
public returns (bool) {
  if (!accounts[msg.sender]) { throw; }
  if (accounts[msg.sender].balance + balance < accounts[msg.sender].balance) { throw; }
  accounts[msg.sender].balance += balance;
  return true;
}
```

注意第二个 `*if*` *语句，这是必要的，因为实度中的加减会导致数值上溢和下溢——要小心！还要注意未声明的* `*msg*` *对象存在于您的函数范围内。这有各种各样的好东西，你可以在函数中引用。*

当我们通过发送一个事务来调用这个函数时，我们要求更新网络的全局状态，如下所示:

> 在本合同范围内，`msg.sender`的账户余额增加了`balance`。

我们没有能力自己更新状态，所以我们需要一个挖掘器来帮我们做这件事。我们用`gas`支付他或她这项服务的费用，翻译过来就是以太。

为了恰当地调用这个函数，我们需要再次使用 ABI:

```
addUserBalance(uint256) --> 22526328 --> 0x225263280000000000000000000000000000000000000000000000000000000000000001
```

我们使用这些数据形成一个**未签名的**交易:

```
var data = '0x225263280000000000000000000000000000000000000000000000000000000000000001';
var nonce = config.web3.eth.getTransactionCount(keys.me.addr);
var gasPrice = 20 * Math.pow(10, 9);
var gasLimit = 100000;var txn = {
  from: config.me.addr,
  to: config.contract_address,
  gas: `0x${gasLimit.toString(16)}`,
  gasPrice: `0x${gasPrice.toString(16)}`,
  data: data,
  nonce: `0x${nonce.toString(16)}`,
  value: '0x0'
}
```

如上所述，`gas`是进行交易(即更新状态)所必需的。`gas * gasPrice`是矿工执行您的交易可能花费的金额。如果运营成本高于您提供的成本，交易将不会更新状态，矿工将保留您所有的汽油钱。如果少于所提供的气体使用，你会退还余额。

如果我们向网络提交这个对象，它将失败，因为没有证据证明我实际上授权了这个交易。谁知道呢，某个陌生人可能会把我的余额更新到 10 亿(尽管不清楚为什么有人会这么做)。

无论如何，我需要做的是**用我的私钥签署**交易。还记得我告诉你不要和任何人分享的那个挂在你的配置文件里的旧东西吗？用它做这个:

```
var Tx = require('ethereumjs-tx');var privateKey = Buffer.from(config.me.pkey, 'hex')
var tx = new Tx(txn);
tx.sign(privateKey);
var serializedTx = tx.serialize();
```

在这里，我们使用我最喜欢的[库之一](https://github.com/ethereumjs/ethereumjs-tx)给你的私钥签署一个事务对象。这应该会返回如下内容:

```
0xf8aa808504a817c800830f424094a0f68379088f9aee95ba5c9d178693b874c4cd6880b844a9059cbb000000000000000000000000053b2188b0b100e68299708864e2ccecb62cdf0d000000000000000000000000000000000000000000000000000000746a5288001ca01f683f083c2d7c741a1218efc0144adc1749125a9ca53134b06353a8e4ef72afa07c50fb59647ff8b8895b75795b0f51de745fa5987b985f7d1025eb346755bca0
```

现在，最后，我们可以通过 web3 把它提交给区块链。它将返回一个事务散列，该散列只是所提供的事务的散列(*非常重要的是，* ***而不是*** *证明事务成功！*)

```
var txHash = config.web3.eth.sendRawTransaction(raw_txn);
```

大概是这样的:

```
0xac8914ecb06b333a9e655a85a0cd0cccddb8ac627098e7c40877d27a130a7293
```

现在还有最后一个步骤，严格来说是可选的，但对于验证您的交易是否被接受和处理非常重要:获取您的交易收据。

```
var txReceipt = config.web3.eth.getTransactionReceipt(txHash);
```

如果这返回`null`您的交易没有被提取(也许您用错误的私钥签名？).如果不是`null`，可能还有我现在不深究的你交易失败的各种其他线索。

*好吧，就一个线索——如果你的* `*gasUsed*` *等于总的* `*gas*` *发送，就说明你的函数调用失败了。这意味着要么 1。您没有提供足够的气体和/或 2。你的合同遇到了一个* `*throw*` *。*

就这样结束了！

# 结论

我知道，那是很多东西。

如果你感觉被淹没了，我建议你慢慢来，把这篇文章作为参考。您可能需要在每一步花费大量时间阅读文档和进行试验。

也就是说，我上面描述的是 80%的战斗。一旦你掌握了这些东西，我个人会认为你是一个有能力的以太坊开发者。

如果你有兴趣，开始修补！工具正变得越来越好，投入其中从未如此容易。欢迎加入。

—

*如果你喜欢这篇文章，* [*在 twitter 上关注我*](https://twitter.com/ethereum_alex) *或者来* [*加入社区*](https://www.reddit.com/r/ethereum/) *。也看看我的公司，*[*ConsenSys*](https://consensys.net/)*，因为我们做的东西和我的项目一样牛逼，*[*【Grid+*](https://gridplus.io)*，这是用以太坊来改变能源的未来。如果你想加入我们的邮件列表，你可以在这里订阅:*

—

## 附录:

***六角字符串:***

我意识到我没有选择十六进制数的最佳例子(因为`1`在十六进制和十进制记数法中是相同的值)。如果您要调用我们的`add`函数，并将此作为参数之一:

```
0000000000000000000000000000000000000000000000000000000000000100
```

你实际上是在添加`256`。因此，对于任何函数调用来说，最好的做法是执行以下操作:

```
var _x = 100;
var x = `${x}.toString(16)`;
```

这将确保您总是使用十六进制值进行调用。

***按键和配置文件***

如果你不知道如何生成密钥，你可以看看我在应用程序中使用的这个文件:

```
/**
 * Generate a test keypair
 */var keystore = require('eth-lightwallet').keystore;
var Promise = require('bluebird').Promise;
var crypto = require('crypto');
var fs = Promise.promisifyAll(require('fs'));
var jsonfile = Promise.promisifyAll(require('jsonfile'));var KEYSTORE_DIRECTORY = `${process.cwd()}/test`;
var name = process.argv[2];
var password = "test";/**
  * Run the generator
  */
createKeystore(password)
.then(function(ks) {
  return saveProfile(name, ks.keystore, ks.privateKey, ks.address); })
.then(function(saved) { console.log('saved', saved); })
.catch(function(error) { console.log(error); });/**
  * Utility Functions
  */
function createKeystore(_password) {
  return new Promise(function(resolve, reject) {
    var password = Buffer(_password).toString('hex');
    keystore.createVault({ password: password }, function(error, ks) {
      if (error) { reject(error); }
      ks.keyFromPassword(password, function(error, dKey) {
        if (error) { reject(error); }
        ks.generateNewAddress(dKey, 1);
        var address = `0x${ks.getAddresses()[0]}`;
        var privateKey = ks.exportPrivateKey(address, dKey);
        var keystore = JSON.parse(ks.serialize());
        resolve({ address, privateKey, keystore });
      });
    });
  });
}function saveProfile(name, keystore, privateKey, address) {
  return new Promise((resolve, reject) => {
    jsonfile.readFileAsync(`${KEYSTORE_DIRECTORY}/keys.json`, {throws: false})
    .then(function(PROFILES) {
      var profiles = PROFILES || {};
      profiles[`${name}`] = {
        keystore,
        privateKey,
        address
      };
      console.log('profiles', profiles)
      return profiles;
    })
    .then(function(_profiles) {
      return jsonfile.writeFileAsync(`${KEYSTORE_DIRECTORY}/keys.json`, _profiles, {spaces: 2});
    })
    .then(function() { resolve(true); })
    .catch(function(error) { reject(error); });
  })
}
```

这将在名为`test/keys.json`的文件中保存您命名的密钥库。你可以用普通的老`node keygen.js <account_name>`来运行它，你应该很棒。我把它用于测试账户，但是如果你需要的话，你可以重复使用这些功能，比如自动生成账户。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)