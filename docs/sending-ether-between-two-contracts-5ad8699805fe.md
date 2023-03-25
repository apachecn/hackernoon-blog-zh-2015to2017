# 在两个合同之间传递以太

> 原文：<https://medium.com/hackernoon/sending-ether-between-two-contracts-5ad8699805fe>

**什么是真实世界的应用？**
我们有向人们提供服务的提供商，比如互联网、电话、水、食物和住所。想要使用这些服务的人可以订阅这些服务。提供商可以收取用户因使用该服务而欠下的债务。用户可以通过向提供商发送以太网来清偿债务。

提供者有他们跟踪的工作，以便提供他们的服务并雇佣员工来完成这些工作。员工用户可以通过从提供商处提取乙醚来获得报酬。

这篇文章将介绍一名员工用户完成一项工作并从提供商合同中撤销 ether，以帮助澄清发送 ether 到合同的无效跳转错误。我们将以奇点厨房为例。我将首先介绍测试，然后介绍智能合约。

免责声明
**合同未经审核，包含安全漏洞，请勿用于学习以外的任何其他目的。**

试验

```
it('init', function() {
    return Kitchen.new("SingularityKitchen", "Healthy living for the next gen", {from:_owner})
      .then(function(kitchenContract) {
        if(kitchenContract.address){
          kitchen = kitchenContract;
        } else {
          throw new Error("no contract address");
        }
        return true;
      })
  })it('pay-out-job', function(){
    var jobName = "Single Customer Order";
    assert.equal(balance(kitchen.address), web3.fromWei(1100000000000000000,"ether"))
    return kitchen.payOutJob(staff.address, jobName, {from:_owner})
      .then(function(txHash){
        return kitchen.staffList(staff.address);
      })
      .then(function(staffInfo){
        //this is the payout stored in a struct
        assert.equal(staffInfo[2].toString(), 300000000000000000);
        return kitchen.collectPayout(staff.address, {from:_staff1})
      .then(function(txHash){
        return kitchen.staffList(staff.address);
      })
      .then(function(staffInfo){
        console.log(balance(kitchen.address))
        assert.equal(staffInfo[2].toString(), 0);
        assert.equal(balance(staff.address), web3.fromWei(300000000000000000, "ether"));
        assert.equal(balance(kitchen.address), web3.fromWei(800000000000000000, "ether"));
      })
    })
  }) 
```

我将在这里介绍这两个测试，合同的其余测试可以在 [github repo](https://github.com/qjflores/singularity/tree/master/test) 上找到。

在“init”中，我们使用 new 方法创建了一个厨房合同的新实例，该方法带有一些参数和关于谁在发送该事务的数据。创建智能合约的新实例会返回智能合约，我们可以检查智能合约是否有地址，如果有，我们就将其设置为全局变量‘kitchen’。如果契约没有地址，那么我们抛出一个新的错误。

在“支付工作”中，我们首先检查厨房合同是否有要支付给员工的费用。然后我们尝试从厨房契约中调用 payOutJob 函数。它接受用户合同的地址和完成的作业名。这种事务由某种所有者调用，比如一个授权用户，他被允许根据合同向完成工作的人支付报酬。一旦 payoutjob 函数将返回一个 txHash，我们不打算对 txHash 做任何事情，但是我们可以控制台记录它并查看它包含的数据。我们将调用契约的公共属性“staffList ”,这是一个映射数据类型，因此我们将传入用户契约的地址。将用户合同的地址传入 staffList 将返回一个 struct 数据类型，该数据类型包含一个用于存储向 Staff 用户支付的信息的字段。我们可以像调用数组中的元素一样调用结构的内容。StaffInfo[2]是一个 uint256 数据类型，我们可以调用一个 toString 方法来获取要支付给用户合同的金额。这里我们断言它是厨房合同中设定的 300000000000000 卫或“单个顾客订单”工作的比率。当员工用户准备好收取他们的支出时，他们可以从厨房合同中调用 collectPayout 函数。我们得到一个事务散列并调用 staffList，这样我们就可以检查结构中是否有变化。我们检查支出是否已设置为 0，厨房和员工合同的余额是否已适当更改。

智能合同

我们需要两个智能契约来为这个约定提供自治，它们是用户契约和提供者契约。我正在从 Shlomi Zeltsinger 的这个 [YouTube 教程系列](https://www.youtube.com/watch?v=m9Zb49RNGis)中构建这些合同。

```
contract User {
  string public userName;
  address public owner;mapping (address => Service) public services;struct Service{
    bool active;
    uint lastUpdated;
    uint debt;
  }function User(string _name) payable {
    userName = _name;
    owner = msg.sender;
  }function payable {
  }function registerToProvider(address _providerAddress){
    services[_providerAddress] = Service({
      active:true,
      lastUpdated: now,
      debt: 0
      });
  }function setDebt(uint256 _debt){
    if(services[msg.sender].active){
      services[msg.sender].lastUpdated = now;
      services[msg.sender].debt += _debt;
      } else {
        throw;
      }
  }function clearDebt() returns (bool result){
    if (services[msg.sender].active){
      services[msg.sender].lastUpdated = now;
      services[msg.sender].debt = 0;
    } else {
      throw;
    }
  }function unsubcribe(address _providerAddress){
    if(services[_providerAddress].debt == 0){
      services[_providerAddress].active = false;
    } else {
      throw;
    }
  }contract Provider {
  string public providerName;
  string public description;mapping (address => staffUser) public staffList;
  mapping (bytes32 => Job) public jobs;struct staffUser{
    bool active;
    uint lastUpdated;
    uint256 payout;
  }struct Job {
    bytes32 name;
    uint256 rate;
  }function Provider(string _name, string _description) {
    providerName = _name;
    description = _description;
  }function setDebt(uint256 _debt, address _userAddress) {
    User person = User(_userAddress);
    person.setDebt(_debt);
  }function recievePayment(address _userAddress) payable returns (bool result) {
    User person = User(_userAddress);
    person.clearDebt();
    return true;
  }function addStaff(address _userAddress) {
    staffList[_userAddress] = staffUser({
      active:true,
      lastUpdated: now,
      payout: 0
      });
  }function addJob(bytes32 _name, uint256 _rate) {
    jobs[_name] = Job({name:_name,rate:_rate});
  }function updateJobRate(bytes32 _name, uint256 _rate){
    jobs[_name].rate = _rate; 
  }function payOutJob(address _userContractAddress, bytes32 _jobName){
    staffList[_userContractAddress].payout += jobs[_jobName].rate;
  }function collectPayout(address _userContractAddress) {
    uint256 _amount = staffList[_userContractAddress].payout;
    staffList[_userContractAddress].payout = 0; 
    if(!_userContractAddress.send(_amount)){
      throw;
    }
  }}
```

我将在合同中强调一些与这个职位相关的职能。这里有一个真正[有用的发送以太的例子](https://github.com/raineorshine/solidity-by-example/blob/master/send-eth.sol)。

在我们的测试中，“staff”变量是用户契约的一个实例。要强调的大函数和编写这个代码评审的主要原因是 payable 函数。这是一个后备功能。回退功能是不运行任何东西的功能。更多关于回退功能的信息可以在[这里](https://github.com/ConsenSys/Ethereum-Development-Best-Practices/wiki/Fallback-functions-and-the-fundamental-limitations-of-using-send()-in-Ethereum-&-Solidity)找到。

如果一个合同正在接收以太网，它需要有这个回退功能，否则你会收到一个无效的跳转错误。

> 无效跳转意味着您调用了不存在的代码，或者数组越界。调用不存在的代码可能以多种形式出现:1)由于某种原因，您的契约地址中没有代码，2)您正在调用实际上不可用的契约函数，3)您的契约正在调用另一个不可用的契约或函数。现在最简单的方法是注释掉代码，直到错误消失，然后慢慢地让它返回，直到你找到正确的行。Truffle 的下一个重要特性是获取这些消息的堆栈跟踪。—蒂姆

```
function payable {
  }
```

payOutJob 函数获取用户合同地址和作业名称，该函数所做的就是通过使用用户合同地址调用 staffList 映射从 staffUser 结构获取支出字段，然后通过使用 jobName 调用 jobs 映射从 Job 结构获取作业费率字段来更新支出。

collectPayout 函数获取一个用户协定地址，并通过使用用户协定地址调用 staffList 映射，将变量 _amount 设置为 staffList 结构中的支出字段。然后，我们将支出字段更新为 0，并尝试使用“_ usercontracaddress . send(_ amount)”将乙醚量发送给用户合同。如果我们不能发送以太网，那么 throw 将恢复我们交易中的所有内容，并且支付字段将保持不变。

感谢 Tim Coulter 和 Nikita Fuchs 对无效跳转的澄清。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)，[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！