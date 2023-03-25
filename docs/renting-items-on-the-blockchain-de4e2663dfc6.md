# 在区块链上租赁物品

> 原文：<https://medium.com/hackernoon/renting-items-on-the-blockchain-de4e2663dfc6>

![](img/fde443af3968e1a391c8a7271a4db0a9.png)

Photo by [William Iven](https://unsplash.com/photos/OP2EQ5g-Zkw?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

[区块链](https://hackernoon.com/tagged/blockchain) [技术](https://hackernoon.com/tagged/technology)实现的最神奇的事情之一是无摩擦支付。只需点击几下鼠标，几乎没有任何麻烦，你就可以向其他人(或物)支付任何金额的钱，以非常低的费用购买产品或服务，无需中介。

当谈到支付时，区块链技术允许我们做一些用“传统”方法极其昂贵、难以实现或根本不可能做的事情。

例如，租一部电影看几分钟，并就你已经支付但没有使用的时间获得退款是极其困难的，如果不是不可能用信用卡支付的话。
如果你想租一个相当便宜的东西用几分钟呢？使用 Paypal 或信用卡，网络收取的佣金会比租金本身还贵。

# 在区块链上租赁物品

我整理了一个简单的合同，其他智能合同开发人员可以使用它来使他们自己的合同可以出租。

下面是代码以及如何实现和使用它的详细说明:[https://github.com/pabloruiz55/Rentable](https://github.com/pabloruiz55/Rentable)

**合同的几个关键特征:**

*   它非常容易实现和配置。开发商只需在他的基础合同上加几行，就可以“出租”了。
*   租赁合同允许设置租赁价格和最小/最大归还时间，以控制项目的可用性。
*   租赁项目的帐户可以这样做，只需向合同发送以太网，租赁期限将从中计算。
*   租借物品的帐户可以在任何时候归还物品，并获得剩余时间(秒)的退款。