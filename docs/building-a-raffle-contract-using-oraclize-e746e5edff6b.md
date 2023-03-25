# 使用 Oraclize 构建抽奖智能合同

> 原文：<https://medium.com/hackernoon/building-a-raffle-contract-using-oraclize-e746e5edff6b>

![](img/2bc5c2b7a88062cae7156f35fbb46d0b.png)

在[实度](https://hackernoon.com/tagged/solidity)中生成随机数并不简单。首先，由于系统的确定性，坚固性并不是天生的随机函数。

> 在数学和物理学中，**确定性系统**是**系统**，其中**系统**的未来状态的发展不涉及随机性。一个**确定性**模型因此将总是从给定的起始条件或初始状态产生相同的输出。—[https://en.wikipedia.org/wiki/Deterministic_system](https://en.wikipedia.org/wiki/Deterministic_system)

有许多服务和 API 允许我们生成随机数，但问题是它们不是分散的，你必须相信别人生成的数是真正随机的。

此外，您不能相信契约中看似随机的值，比如 blockhash、timestamp 或其他 miner 定义的值。矿工可以在这样的程度上操纵这些值，并且事件选择不发布块。对于一个赌场应用程序，你不能只相信矿工，但有很多其他的用例，你可以依靠 [blockhash](https://hackernoon.com/tagged/blockhash) 来生成随机结果，只要依赖于这种随机性的质量的总价值低于矿工通过开采单个区块获得的价值。*更多相关信息:*[*https://ether eum . stack exchange . com/questions/419/when-can-block hash-be-safely-used-for-a-random-number-when-when-be-it-be-unsafe*](https://ethereum.stackexchange.com/questions/419/when-can-blockhash-be-safely-used-for-a-random-number-when-would-it-be-unsafe)

那么，我们如何在一个可靠的智能契约中安全地生成一个随机数呢？

有一些解决方案可以帮助我们实现这一点，比如 Randao(Randao:[https://github.com/randao/randao](https://github.com/randao/randao))。)和 Oraclize([http://www.oraclize.it/#services](http://www.oraclize.it/#services))。在本文中，我将重点介绍 Oraclize，主要是因为它非常容易上手，其次是因为除了生成随机数之外，它还可以用来做更多的事情，比如从 Solidity smart contract 内部访问任何其他 API。

# 建立抽奖合同

在本教程中，我们将构建一个非常简单的抽奖智能合约，以展示如何使用 Oraclize 来生成一个随机数。合同将按如下方式工作:
我们将部署合同，并设置抽奖将接受的最小和最大参与者数量。一旦有足够多的参与者，抽彩组织者(或任何人)可以执行函数，让 Oraclize 生成一个随机数。有了这个随机数，我们将从参与者的数组中选择获胜者。
*在这个第一版中，我们不会包括所有的支付逻辑，所以任何人都可以自由参与，但他们不会获得任何 ETH 奖，当然。

## 项目设置

对于本教程，我们将在 Remix 上部署所有东西，使用 Metamask 连接到 Ropsten，因为 Oraclize 不能在本地或 JavaScript VM 上工作。我假设你对如何使用 Remix 和 Metamask 有一些基本的了解。此外，请确保您至少有 3 或 4 个帐户有一些余额，因为我们将需要它来支付参加抽奖的汽油费用以及支付 Oraclize 以生成随机数(稍后将详细介绍)。

## 为合同编码

在 Remix 中，创建一个名为 Raffle.sol 的新文件，这将是我们包含所有抽奖逻辑的合同。

```
pragma solidity ^0.4.4;contract Raffle { uint private chosenNumber;
  address private winnerParticipant;
  uint8 maxParticipants;
  uint8 minParticipants;
  uint8 joinedParticipants;
  address organizer; bool raffleFinished = false; address[] participants;
  mapping (address => bool) participantsMapping; event ChooseWinner(uint _chosenNumber,address winner);
  event RandomNumberGenerated(uint); function Raffle(){
    address _org = msg.sender; 
    uint8 _min = 2; 
    uint8 _max = 10;    require(_min < _max && _min >=2 && _max <=50); organizer = _org;
    chosenNumber = 999;
    maxParticipants = _max;
    minParticipants = _min;
  }function() payable {}function joinraffle(){
    require(!raffleFinished);
    require(msg.sender != organizer);
    require(joinedParticipants + 1 < maxParticipants);
    require(!participantsMapping[msg.sender]); participants.push(msg.sender);
    participantsMapping[msg.sender] = true; joinedParticipants ++;
  }function chooseWinner(uint _chosenNum) internal{
    chosenNumber = _chosenNum;
    winnerParticipant = participants[chosenNumber];
    ChooseWinner(chosenNumber,participants[chosenNumber]);
}function generateRandomNum(){
    require(!raffleFinished);
    require(joinedParticipants >=minParticipants && joinedParticipants<=maxParticipants); raffleFinished=true;

    chooseWinner(0); //We'll replace this with a call to Oraclize service later on.}function getChosenNumber() constant returns (uint) {
    return chosenNumber;
  }function getWinnerAddress() constant returns (address) {
    return winnerParticipant;
  }function getParticipants() constant returns (address[]) {
    return participants;
  }}
```

我将介绍代码中最重要的部分:

*   **函数 Raffle():** 合约的构造者设置合约的配置状态变量，比如所需的最小和最大参与者，以及抽奖的组织者是谁(目前是合约的创建者)。注意，我们硬编码了这些变量，只是为了使例子更简单，但是您可以让构造函数接收这些参数。此外，我们要求至少有 2 名参与者，不超过 50 人。如果您愿意，这个最大值也可以更改。
*   **回退功能:**我添加了回退功能，因为我们需要向合同发送 Ether，这样它就可以为 Oraclize 的服务付费。你认为他们会免费提供服务吗？:)—稍后会有更多相关内容。
*   **函数 joinRaffle():** 该函数允许呼叫者加入抽彩。首先，我们进行一些检查，以确保我们没有向已经结束或已经达到参与者最大数量的抽奖活动添加新的参与者。我们还会检查组织者是否试图加入，以及此人是否已经加入。然后，我们将新的参与者添加到参与者数组中(这样我们以后可以随机选择其中一个参与者)，并且我们还将它添加到参与者映射中(这允许我们容易地检查该参与者是否已经加入)。让两个变量保存相同的信息对我来说有点奇怪，但是鉴于这个例子相当简单，我觉得我们可以摆脱它。
*   **函数 generateRandomNumber():** 这个函数将被调用，让 Oraclize 生成随机数。请注意，我们将它公开，并不要求组织者是调用它的人。这是故意的，所以如果组织者，出于某种原因，消失了，参与者可以选择一个赢家。这也可以被改变，例如，允许这种机制，但是只有在一段时间过去之后。目前，该函数所做的只是将 raffleFinished 标志设置为 true(因此该函数只能调用一次),并调用参数为 0 的 chooseWinner。当我们实现 Oraclize 时，我们将生成一个随机数，而不是发送一个硬编码的 0(这将自动使第一个参加抽奖的人成为赢家)。
*   **函数 chooseWinner():** 这是一个内部函数，它接收一个指示获胜者在参与者数组中的位置的 uint。有了这个号码，我们还可以设置获胜者的地址，并触发一个事件来记录获胜者是谁，这样我们以后就可以检索并显示它。
*   剩下的函数只是稍后在前端用来显示获胜者。

继续尝试[混音](https://remix.ethereum.org/)中的合约。只需复制并粘贴上面的代码，然后部署契约。您应该能够使用不同的帐户加入抽彩，然后，在有足够的参与者后，您可以调用 generateRandomNumber()来选择一个获胜者(它将始终是参与者数组中位置 0 的地址)。

现在我们已经确保了抽奖的逻辑是正确的，是时候使用 Oraclize 为我们生成一个随机数了。

## 使用 Oraclize 生成随机数

将 Oraclize 集成到我们的智能合约中非常简单直接。第一步是在我们的合同中导入 Oraclize API。在抽奖合同声明前添加以下行。

```
import "github.com/oraclize/ethereum-api/oraclizeAPI.sol";contract Raffle is usingOraclize{
...
}
```

请注意，我们还将 Raffle 更改为从 usingOraclize 继承。

既然我们导入了 Oraclize，我们就可以访问它的功能了。你应该知道的一件事是，以这种方式导入 Oraclize 将会失败，例如，如果你使用 Truffle 来编译你的合同。如果您使用 Truffle，您将必须制作 Oraclize 文件的本地副本，并将其重命名为“usingOraclize.sol ”,因为 Truffle 需要该文件与合同同名。

我们要做的第一件事是添加 Oraclize 回调函数。Oraclize 的工作方式是首先进行查询——我们可以查询外部 API、Wolfram 的 Mathematica，或者像我们的情况一样，查询内置的随机数生成器——它将向 Oraclize 契约触发一个事务，在该事务得到处理后，它将触发我们在契约中定义的回调函数，并产生相应的结果。

这就是回调函数的样子:

```
// the callback function is called by Oraclize when the result is ready
// the oraclize_randomDS_proofVerify modifier prevents an invalid proof to execute this function code:
// the proof validity is fully verified on-chainfunction __callback(bytes32 _queryId, string _result, bytes _proof)
{
  // If we already generated a random number, we can't generate a new one.
  require(!raffleFinished);
  // if we reach this point successfully, it means that the attached authenticity proof has passed!
  require (msg.sender == oraclize_cbAddress()); if (oraclize_randomDS_proofVerify__returnCode(_queryId, _result, _proof) != 0) {
    // the proof verification has failed, do we need to take any action here? (depends on the use case)
  } else {
    // the proof verification has passed
    raffleFinished=true; // for simplicity of use, let's also convert the random bytes to uint if we need
    uint maxRange = joinedParticipants; // this is the highest uint we want to get. It should never be greater than 2^(8*N), where N is the number of random bytes we had asked the datasource to return
    uint randomNumber = uint(sha3(_result)) % maxRange; // this is an efficient way to get the uint out in the [0, maxRange] range

    chooseWinner(randomNumber); RandomNumberGenerated(randomNumber); // this is the resulting random number (uint)
  }
}
```

首先，我们将确保 raffleFinished 为 false，所以我们不允许这个函数被调用多次。

然后，我们检查我们获得的数据在交付给智能合约时没有被篡改。你可以在这里了解更多信息:[https://blog . oraclize . it/the-random-data source-chapter-2-779946 e54f 49](https://blog.oraclize.it/the-random-datasource-chapter-2-779946e54f49)

如果验证已经通过，意味着数据没有被篡改，那么我们继续使用随机数。我们将调用之前使用的 chooseWinner()函数，但是我们将传递我们生成的随机数，而不是硬编码的 0 作为参数。
如您所见，生成的随机数使用 joinedPaticipants 状态变量来确定其最大范围。我们还触发了一个事件来记录我们生成的随机数。

我们要做的最后一件事是修改我们的 generateRandomNum()函数，这样当有人执行它时，它会使查询变成 Oraclize。

```
function generateRandomNum(){
    require(!raffleFinished);
    require(joinedParticipants >=minParticipants && joinedParticipants<=maxParticipants); oraclize_setProof(proofType_Ledger); // sets the Ledger authenticity proof
    uint N = 4; // number of random bytes we want the datasource to return
    uint delay = 0; // number of seconds to wait before the execution takes place
    uint callbackGas = 200000; // amount of gas we want Oraclize to set for the callback function
    bytes32 queryId = oraclize_newRandomDSQuery(delay, N, callbackGas); // this function internally generates the correct oraclize_query and returns its queryId}
```

通过调用 oraclize _ newRandomDSQuery(delay，N，callbackGas)我们将让 Oraclize 为我们生成一个随机数，当它准备好了，我们前面定义的回调函数将被触发。oraclize_newRandomDSQuery 接收 3 个参数。

*   delay:这是在执行发生之前等待的秒数。我们将它设置为 0，这样一旦有结果，它就会执行回调。
*   n:这是我们希望数据源返回的字节数。在我们的例子中，如果组织者愿意的话，我们的抽彩最多可以接受 50 个参与者，4 个字节就足够了。
*   callbackGas:我们将转发或调整多少 Gas，以便它可以执行回调函数。我们将其设置为 200.000，这足以执行该功能。(在我的测试中，它消耗了大约 125.000 汽油)。

就是这样！你可以在 Remix 里试一试，看看效果如何。一些注意事项和建议:

*   **Oraclize 的定价模式:**正如我之前提到的，Oraclize 不是免费的。你可以在这里查看他们的定价:[http://docs.oraclize.it/#pricing](http://docs.oraclize.it/#pricing)。请注意，您的合同进行的第一次查询是免费的，然后您将不得不为交易付费。怎么会？执行查询的契约从其余额中支付费用。因此，在调用 generateRandomNum()函数之前，请确保向契约发送一些以太网。—这就是我们包含回退功能的原因。
*   测试你的合同可能会很乏味。首先，由于我们使用的是 Oraclize，您不能在本地测试它，我们需要连接到一个 testnet(我使用的是 Ropsten)。你应该记住，当你调用 generateRandomNum()时，需要 30-50 秒来挖掘，然后，需要一分钟来实际处理回调函数。还有，在 Metamask + Remix 里切换账号也要费点功夫。用耐心武装自己。
*   **执行这个需要多少汽油？我仍在研究执行这份合同所需的必要气体。在 Remix 上，我不得不在部署合同时将限制提高到 5.000.000。确保你在生成随机数时也发送了足够的气体，否则回调函数将失败。**

我们今天建立的合同允许我们运行一个简单的抽奖活动，人们可以加入，然后通过使用 Oraclize 生成一个随机数来选择一个获胜者，我们可以肯定这个随机数没有被篡改。有很多改进和新功能的空间，例如允许人们用乙醚参加抽奖并赢得奖励。

如果你有任何建议或者你在代码中遇到任何问题，请在下面的评论部分告诉我。