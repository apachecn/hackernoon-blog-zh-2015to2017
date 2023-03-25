# 智能汽车的智能合同

> 原文：<https://medium.com/hackernoon/a-smart-contract-for-a-smart-car-db08eda4bb4f>

![](img/f28a358ef741d6a84d5f92688007da03.png)

Photo by [Evgeny Tchebotarev](https://unsplash.com/photos/aiwuLjLPFnU?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

去年，当我参加一个金融科技会议时，我听到布雷特·金谈论未来的汽车不仅能够自动驾驶，而且由于 T2 区块链技术，它们能够为它们的主人工作。因为我听说在这方面已经有了一些进展，但是我们还远远没有能力拥有一辆可以开到优步的汽车，而不是在停车场积灰，在我在办公室的时候为我赚钱。

这个说法让我很困惑，但是尽管我很想造一辆这样的车，我甚至不知道一辆普通的*车是如何工作的。幸运的是，我确实对软件开发和智能合同有所了解，所以在这篇文章中，我想谈谈如何为无人驾驶汽车建立这样的合同。*

*在这篇文章中，我将展示几个人共享所有权的自动驾驶汽车的基础知识。这辆车将供任何第三方租用一天，然后，一旦租期结束，它将把收入分配给车主。(减去 eth gas 津贴和其他费用)。*

# *关于智能汽车及其合同的一些假设和考虑*

*在这个分析中，我将做一些假设来简化智能合约的逻辑。*

*首先，我将假设汽车以某种方式运行一个允许它签署事务的完整节点。汽车将拥有一个帐户/地址，该帐户/地址将成为与不同参与者交互的智能契约的“所有者”。*

*第二，我不会处理很多现实世界中的问题，比如租车的人偷了车，或者卷入了一场导致汽车无法使用的事故。*

*第三，汽车的所有权将非常简单。首先，车主从一开始就得到一辆车，假设付款是在经销商处进行的。然后，每个车主都有相等份额的车。最后，我们不会处理复杂的决策，因为这篇文章会变得非常广泛。*

*最后，我还假设汽车以某种方式为用户提供了一些能够与智能合约交互的界面。此外，汽车可以安排稍后调用某些功能，例如调用智能合同中在 24 小时后结束租赁期的功能。此外，我假设用户拥有能够与区块链交互的设备，比如支持 web3 的浏览器。*

*这些假设的目的是使智能合同尽可能简单，同时将尽可能多的决策留给汽车本身。我很确定接下来的事情在现实世界中是可能的，但可能不是最佳解决方案。无论如何，请随意讨论更好的方法，或者指出合同中您认为在现实世界中根本行不通的部分。*

# *智能合同*

*智能汽车合约(v0.1)的完整代码可以在下面的 Github repo 中找到:[https://Github . com/pabloruiz 55/smart car/blob/master/contracts/smart car . sol](https://github.com/pabloruiz55/SmartCar/blob/master/contracts/SmartCar.sol)
下面的内容不是完整的代码，而是对其中最重要部分的重点介绍。*

## *初始化汽车智能合约*

```
*//The address of the car, which will sign transactions made by this contract.
address public carSigner;// Value of the car, in wei
uint public carValue;bytes32 public licensePlate;// Owners of the car, they will be the ones that receive payments from the car.
// We assume each owner owns the car equally.address[] public owners;
uint constant MAX_OWNERS = 100;//Earning from driving will be distributed to each owner for them to withdraw
mapping (address => uint) public ownersBalance;
uint public balanceToDistribute;uint constant INITIAL_CAR_SHARES = 100;
mapping (address => uint) public carShares;DriverEntity currentDriverEntity;
DriveStatus currentDriveStatus;//To keep track of who's currently using the car
//If the owners are driving it, it will be their address.
//If someone rented it, it will be the renter address, so he can be held accountable.
//In this case, we could even ask for a warranty which will be sent back if the car is ok.address currentDriverAddress;
uint currentDriveStartTime = 0;
uint currentDriveRequiredEndTime = 0;//Rates
uint constant RATE_DAILYRENTAL = 1 ether; //1 ETHenum DriverEntity {
    None,
    Owner,
    Autopilot,
    Cab,
    Uber,
    DailyRental,
    Other
  }enum DriveStatus {
    Idle,
    Driving,
    TurnedOff,
    Unavailable
  }// Somehow, the car should be able to communicate its "internals" to the contract.
// These internals are the ones relevant to the functioning of the contract, such as it's fuel.
// We don't care about oil or coolant for example, at this point at least.struct CarInternals {
    uint fuel; //Measured in percentage
  }CarInternals carInternals;bool carIsReady = false;modifier onlyIfReady {
        require(carIsReady);
        _;
    }function SmartCar(bytes32 _licensePlate, uint _carValue){
    require(_licensePlate.length >0 && _carValue > 0);
    carSigner = msg.sender;
    carValue = _carValue;
    licensePlate = _licensePlate;
    carShares[address(this)] = INITIAL_CAR_SHARES; currentDriveStatus = DriveStatus.Idle;
    currentDriverEntity = DriverEntity.None; carInternals.fuel = 100;
  }*
```

*让我解释一下 SmarCar 契约的每个状态变量和构造函数:*

***carSigner:** 这是代表汽车的账户。正如我之前提到的，汽车应该运行自己的节点，该节点将签署它需要执行的事务。这是将部署汽车合同的帐户。在现实生活场景中，我想象制造商设置汽车节点和帐户。我们假设经销商和制造商都无权访问该帐户的私钥。我想说的是:汽车需要执行合同功能，唯一的方法是汽车本身是一个外部拥有的帐户，可以与智能合同进行通信，而不依赖于人的交互。*

***carValue / LicensePlate:** 这是在部署汽车合同时应该提供的值。LicensePlate 可用于识别汽车(我们不会进行任何检查以确保它是独一无二的)，carValue 设定车主支付的价格。我们没有使用 carValue，但我们可以有一个函数，允许所有者将他的汽车份额出售给其他人，carValue/owners 可用于建立基价。*

***OWNERS/MAX _ OWNERS = 100:**OWNERS 是一个数组，保存所有车主的地址。MAX_OWNERS 是一个常量，它将限制汽车可以拥有的车主数量。*

***owners balance/balanceToDistribute:**owners balance 将用于存储每个所有者可以提取的未决余额。当汽车开始付费乘坐时，它会将收到的乙醚存储在自己的余额中，然后当乘坐结束时，乙醚将被平均分配给每个车主，以便车主手动提取他的余额。*

***CAR SHARES/INITIAL _ CAR _ SHARES = 100:**CAR SHARES 用于存储每位车主拥有的汽车数量。就目前而言，我们这样做是为了让每个车主都拥有同等份额的汽车，但也可以这样做，即某个人可以拥有 50%的汽车，其他 10 个人各拥有 5%的汽车。这还会影响收益的分配，因此是按比例分配的。*

***currentDriverEntity/currentDriveStatus:**这些变量是状态机的一部分，它将控制汽车当前正在做什么。例如，如果它是被驾驶的，合同不应该允许其他人租用它。我们还使用 DriverEntity 来跟踪汽车目前正在做的事情——它对优步有用吗？已经租了一天了吗？是其中一个所有者在使用它吗？这也提出了一些关于汽车应该如何自动驾驶以及如何确定它应该做什么的问题，我将在文章的最后讨论这些问题，但这也超出了本文的范围。*

***currentDriverAddress/currentDriveStartTime/currentDriveRequiredEndTime:**这些变量跟踪当前谁租了车，租期何时开始以及预计何时结束(租期开始后 24 小时)。currentDriveRequiredEndTime 用于确定租车人是否在指定的租期内还车。目前我们只是记录这个，但是如果条件不满足，可以采取一些行动。*

*我们还没有对此做任何事情，但是这可以用来跟踪汽车的内部。它需要燃料吗？电池状态如何？它需要换油吗？冷却剂？如果这些内部构件中的任何一个处于临界水平，它应该做什么？*

*SmartCar 构造器:这个契约的构造器非常简单。我们只是设置一些变量的初始状态，最重要的是给 carSigner 赋值。*

## *分配所有者*

```
*//We will assume, for the time being, that the owners are set by the carSigner automatically,
//and that they can't be changed.
//We are basically doing the purchase of the car, off-chain.
//We also assume that each person payed the same amount for the car, thus owning equal shares.function setOwners(address[] _owners) public {
  require(msg.sender == carSigner);
  require(_owners.length > 0 && _owners.length <= MAX_OWNERS); //Can only set owners once.
  require(owners.length == 0); owners = _owners; //We take the total carShares the "car" owns and we distribute them equally among new owners
  //If the shares are not properly divisible (I.E: 100 shares / 3 owners) the remaining shares stay with the car

  uint sharesToDistribute = carShares[address(this)]/owners.length; for (uint8 i; i<owners.length;i++){
      carShares[owners[i]] = sharesToDistribute;
      carShares[address(this)] -= sharesToDistribute;
    } carIsReady = true;
}*
```

*setOwners 函数接收一个地址数组，使他们成为汽车的所有者。在这个函数被调用后，汽车将有一个或多个拥有者，他们将平分汽车。
该功能应该由 carSigner 调用，我还不知道这是否是最佳选择，但我们假设经销商已经设置了该功能。所以，你现在一定已经发现，这个系统有很大一部分仍然需要大量的人类互动和协调。在这种情况下，经销商将负责让这些车主支付他们的交易部分，然后以某种方式让汽车注册其全新的车主。我想象经销店的人用他的密码打开汽车中的某种界面，这将提示它在智能合同上执行这一功能。*

## *租一天车*

```
*// Anyone can rent the car for the day, as long as it is idle.
// In real life, the workflow could be as follows:
// 1\. User calls this function from his mobile device or browser web3 dapp, sending the correct amount of eth
// 2\. The system generates a PIN number (we are just using his address as PIN right now)
// 3\. User gets on the car and unlocks it using the pin.// As it stands, we assume that the car, somehow recognizes that the user
// that paid is actually in the car. We added a activateCar function that
// acts as if it was a PIN.function rentCarDaily() public onlyIfReady payable{
  //No one must be using the car
  require (currentDriveStatus == DriveStatus.Idle);
  require (msg.value == RATE_DAILYRENTAL); currentDriverAddress = msg.sender;
  currentDriveStatus = DriveStatus.Driving;
  currentDriverEntity = DriverEntity.DailyRental;
  currentDriveStartTime = now;
  currentDriveRequiredEndTime = now + 1 days; balanceToDistribute += msg.value; // ADD SafeMath Library E_RentCarDaily(currentDriverAddress,msg.value,   currentDriveStartTime,currentDriveRequiredEndTime);
}*
```

*一旦购买了汽车，就可以使用了。目前，我们只实施一种租赁选择:任何人都可以按天租赁，支付 1 eth(是的，因为这是一种非常新颖和令人惊叹的汽车，其价格令人发指)。这个功能的基本作用是设置它的新状态(作为当天的租赁被驾驶),并记录谁租了它，在什么时间和他应该归还它的时间。此外，我们将收到的乙醚添加到合同的内部余额中，这样它就知道以后应该向所有者分配多少钱。
再一次，这里有很多假设。我想象租车的人使用他支持 web3 的浏览器访问 dapp 来执行这个功能。但是汽车怎么知道发动汽车的人是这个人。嗯，我们可以让这个功能生成一个带有 PIN 码的收据，并让汽车在有人试图启动它时提示输入 PIN 码。另一种解决方案是，当有人试图驾驶汽车时，让汽车提示付款，但这需要高度的信任，因为司机必须使用他的私钥在一个相当公开的设备上签署交易。这将是一个非常冒险的提议…我不会尝试从公共图书馆的 pc 上访问我的家庭银行，为什么我要在“公共”汽车上解锁我的以太坊帐户？*

```
*// For the car to start it will ask the user for his PIN. Instead of generating a PIN
  // we are using his address as PIN, making sure they match.
  // This would be done in the car interface, of course it's a terrible user experience to as for an
  // address instead of a 4 digit PIN, but it will do for now.
  // We are not using this internally.function activateCar(address _user) public view onlyIfReady returns(bool){
    require (_user == currentDriverAddress);
    return true;
  }*
```

*这里有一个非常简单的解决方案。假设每次用户试图启动汽车时，汽车都会提示用户输入他的地址。它会检查输入的地址是否与租车的地址相同。简单但次优。首先，任何人都可以扫描最近的交易，以了解谁租了它，并使用他的地址从他们那里“偷”租金——尽管这可以通过散列地址来减轻。第二，很糟糕的用户体验！想象一下，每次启动汽车都必须在汽车触摸屏上输入 42 个字符。*

## *归还汽车和分配收入*

```
*// This should be called by the end of the rental period.
// Driver would tell the car to end the rental and the car would execute this function.
// Also, the car can call it if the rental period ended. (This would be scheduled car-side)
// Here, we distribute earnings and do the necessary cleanup such as
// issuing fuel recharge if needed.function endRentCarDaily () public onlyIfReady {
  // The person renting the car can end the rental anytime.
  // The carSigner can end the rental only after the renting period has ended
  // in order to "claim the car back".
  require ((msg.sender == carSigner && now > currentDriveRequiredEndTime)
            || msg.sender == currentDriverAddress); //To be called only if it is being rented for the day.
  require (currentDriveStatus == DriveStatus.Driving);
  require (currentDriverEntity == DriverEntity.DailyRental); bool endedWithinPeriod = now <= currentDriveRequiredEndTime; E_EndRentCarDaily(currentDriverAddress, now, endedWithinPeriod); currentDriverAddress = address(0);
  currentDriveStatus = DriveStatus.Idle;
  currentDriverEntity = DriverEntity.None;
  currentDriveStartTime = 0;
  currentDriveRequiredEndTime = 0; //Distribute earnings of the car rental
  distributeEarnings();
}*
```

*一旦顾客用完汽车，他可以通过调用这个函数来归还汽车。这将基本上结束租赁期，使汽车再次可供他人租用，并将收入分配给车主。如果 24 小时过去了，而司机没有这样做，汽车本身可以结束租赁期。为了使这成为可能，汽车必须在内部安排这个函数调用，因为目前还没有办法安排一个约定函数。
请注意，我们不会因为司机没有及时归还汽车而惩罚他，但是可以采取一些措施，比如要求提前支付保修金，并在归还汽车之前将其保留在代管账户中。如果他们在 24 小时租期结束后还车，他们可能会被收取额外的费用。
此外，我们没有考虑租车时的燃油量。司机应该把车归还到租的时候的样子吗？他应该被收取更多的燃料差价吗？—或者，如果汽油价格是 50%,而他们以 75%的价格退货，他们会退款吗？*

```
*//Distribute earnings to owners
function distributeEarnings() internal onlyIfReady {
  //If the carSigner is running out of eth for transactions, transfer before distribution
  transferEthForStipends(); //ETH should also be reserved for recharging fuel at a station.   Not considered yet.
  //refuelCar(); uint earningsPerOwner = balanceToDistribute / owners.length;
  for (uint8 i=0;i<owners.length;i++){
   ownersBalance[owners[i]] += earningsPerOwner; // ADD SafeMath Library
   balanceToDistribute -= earningsPerOwner; // ADD SafeMath Library
  }
}*
```

*一旦汽车被归还，它将计算并分配钱给每一个汽车股东。不过，在资金分配之前，它必须保留一些钱来支付自己的开支。目前，我们只是在考虑一天中给每个活动打电话的人需要支付的汽油津贴。汽车进行的每项交易都会消耗一些汽油，在大多数情况下，汽车设计者会支付这些费用，因此应该为汽车分配乙醚，以便汽车能够正常运行。
在这里，我们还可以计算出汽车在这次旅行中消耗的燃料和其他费用。通行费、公共停车场等。—在将余额分配给所有者之前，保留这些钱。*

```
*// carSigner will need eth to pay for gas stipends being used throughout the day.
// It should be able to get it from the car contract balance.
// This would be called by the car automatically each day, for example.function triggerTransferEthForStipends() public onlyIfReady{
  require(msg.sender == carSigner);
  transferEthForStipends();
}function transferEthForStipends() internal onlyIfReady { uint amount = 1 * (10 ** 17);  // 0.1 eth per day should be enough
  require (carSigner.balance < amount);
  require(balanceToDistribute >= amount); balanceToDistribute -= amount; // ADD SafeMath Library
  carSigner.transfer(amount);
  E_TransferEthForStipends(carSigner,amount, now);
}*
```

*这个函数确保 carSigner(汽车)有足够的 eth 来调用汽车合同函数。这在每次乘车后都会被调用，以防万一，但也可以由汽车触发，如果它检测到它没有足够的钱来正常运行。*

## *提取资金*

```
*//Each owner should call this function to withdraw the balance they have pending.
function withdrawEarnings() public onlyIfReady{ //Make sure the one calling the function is actually an owner
  bool isOwner = false;
    for (uint8 i=0;i<owners.length;i++){
      if (owners[i] == msg.sender){
        isOwner = true;
        break;
      }
    }
  require (isOwner); uint balanceToWithdraw = ownersBalance[msg.sender];
  require (balanceToWithdraw > 0); ownersBalance[msg.sender] =0;
  msg.sender.transfer(balanceToWithdraw);
}*
```

*最后，在任何时候，每个所有者都可以执行这个函数来提取他们所欠的任何未决余额。*

# *从这里去哪里？*

*这份合同可以改进的地方太多了，以至于不知道从哪里开始。我提出了一些问题，并指出了代码中可以改进的地方，例如:*

*   *有没有比让汽车执行事务更好的方法来构建它？汽车节点如何安全设置？制造商应该这样做吗？经销店？*
*   *我们没有使用 carValue。我们应该允许车主出售他们的汽车份额吗？我们应该使用 carValue 还是车主可以设定自己的要价？任何人都可以在任何时候出售他的汽车份额吗？是否应该有一个投票过程？有人能买吗？*
*   *我们没有考虑有人可以购买更大份额的汽车的情况。现在每个人都有相同的所有权百分比，我们假设所有人在经销商处支付了相同的金额。—嗯，我们还假设一家经销商准备让多人以法定货币购买一辆汽车的股份，不知何故，这无缝地转化为这种连锁场景，:D —
    所以，这可以以不同的方式完成，不一定每个车主都应该平等地拥有汽车。这也会影响收益分配和支出。*
*   *现在我们假设汽车是闲置的，任何人都可以来租一天(或者车主使用它，或者让它为优步工作，尽管这些功能还没有实现)。但是汽车应该如何决定它应该做什么呢？它应该允许某人在没有询问所有者他们今天是否需要它的情况下租它一天吗？如果几个车主都表示要用车，如何决定谁有优先权？如果汽车只用于出租，它如何决定什么时候最好为优步工作，什么时候最好被出租，什么时候最好当出租车？*
*   *归还汽车时，如果在 24 小时内没有归还，我们不会采取行动。租期到了，司机还没还车，该怎么办？汽车应该以某种方式停止工作吗？是否应该提示司机进行新的支付？我们是否应该在出租前要求保修，并把钱留在第三方保管处？我们是否应该将司机列入黑名单，让他们永远不允许租这辆车(或网络中的任何其他汽车)？*
*   *汽车内部:合同至少需要知道汽车是否需要加油。汽车传达这一信息的最有效方式是什么？我们假设汽车有各种传感器，允许它获得这些数据，并反过来将这些数据转发给智能合同。*
*   *我们分配所有者的方式是次优的。首先，我们可以有另一个实体，例如汽车经销商，它在收到钱后被授权调用汽车智能合同上的这个函数来注册车主。第二，在这个例子中，我们允许车主在买车时只设置一次。让现在的老板买下另一个怎么样？如果以后我们想让别人成为这辆车的主人呢？第三，这在一定程度上与我们不允许车主拥有比其他人更多的汽车份额有关，如果我们有 3 个车主，他们每个人获得汽车的 33%，剩下的 1%由汽车自己持有，我们该怎么办？最后，到目前为止，一个人拥有多少汽车并不重要，因为它并不被用来计算收入分配。还有一点，也许我们连车赚的钱都不用按所有权分配。*

*正如你所看到的，这篇文章提出了很多我还没有答案的问题，但我希望它能引发一场关于区块链以太坊应该如何与 IOT 项目互动的辩论，反之亦然。*

*欢迎在下面的评论区分享任何建议或批评！*