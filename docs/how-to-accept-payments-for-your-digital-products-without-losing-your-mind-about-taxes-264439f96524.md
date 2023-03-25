# 如何接受为您的数字产品付款——而不至于忘记纳税💵 🖥

> 原文：<https://medium.com/hackernoon/how-to-accept-payments-for-your-digital-products-without-losing-your-mind-about-taxes-264439f96524>

![](img/ea466862c19a6cbb26736eee60862993.png)

在《核心直觉》的最新一集里，丹尼尔·贾库特谈到他的 Mac 应用程序 [MarsEdit](https://red-sweater.com/marsedit/) 的支付方式从 PayPal 转向 Stripe。这在相应的 Slack 团队中引发了一场[关于销售你的数字产品的不同方式的利弊的讨论。](http://chat.coreint.org/)

欧洲增值税规则，会计和其他干扰使这比你想象的更难！

在本文中，我将概述数字产品的常见支付方式及其各自的优缺点。这是从 Mac 开发者的角度来看，但除了许可方面，它也适用于所有其他数字产品，包括 SaaS 订阅和在线课程。

注意，我既不是律师，也不是会计；以上只是我个人的建议。

# 让人给你邮寄现金或支票

**费用:无(我猜)**

在 1992 年，这曾经是接受付款的最先进水平。
2017 年，是你创业最快的杀招。

## 优势

*   当有人真的为了买你的软件而经历了把现金放在有邮资的信封里然后去邮局的麻烦时，那种温暖而模糊的感觉。
*   没有第三方依赖(除了您的本地邮件提供商)。

## 不足之处

*   其他的一切。我们继续吧。

# 让人们通过 PayPal 或 Stripe 给你汇款

**手续费:2.9%+30/笔(** [**条纹**](https://stripe.com/us/pricing) **，** [**支付宝**](https://www.paypal.com/us/webapps/mpp/paypal-fees) **，** [**布伦特里**](https://www.braintreepayments.com/braintree-pricing) **)**

你可以在你的网站上有一个 PayPal 的“给我汇款”链接，或者创建一个支付表单，通过自动流程交付你的许可证代码。

这可能是自然而然想到的选项，但请记住，您仍有责任在收取增值税的国家/地区(包括所有欧盟国家/地区)开具适当的发票并收取(和扣除)增值税，这些国家/地区还收取不同的增值税税率。

如果你在美国，你可能会完全无视欧盟的增值税规定，但我不建议这样做。在那里你会有很好的公司——看起来[恐慌](https://panic.com/)和[Omni 集团](https://www.omnigroup.com/)就有这种方法——但我仍然不推荐它。(我是德国人。我们*真的*喜欢规避风险。)

## 优势

*   最便宜的在线支付方式。
*   只有一个依赖项，如果需要的话，也很容易切换出来。

## 不足之处

*   你负责妥善处理增值税，一塌糊涂。如果你想了解更多，请阅读这篇文章。
*   你也要负责创建适当的发票，并为你的付款记账。你可能只需要登记 Strip/Paypal/Braintree 每月寄给你的总额，但在某些情况下，你可能需要在你的账簿中记录每一笔交易。如果不确定，可以和会计谈谈。
*   你还需要自己实现整个许可系统。这可能是一项繁重的工作。(关于如何处理这个问题，请参见文章底部的一些提示。)
*   以 PayPal 为例:你要和 PayPal 打交道，这是一家很糟糕的公司([来源](https://gizmodo.com/i-cant-afford-not-to-have-that-money-the-worst-paypa-1705854399)，[来源](http://www.screw-paypal.com/horror_stories/horror_stories.html))。

# 让人们通过 Stripe 向你汇款，但让其他人处理整个增值税烂摊子

**费用:2.9%+30/交易(** [**条纹**](https://stripe.com/us/pricing) **)加 31 美元/月(**[**Taxamo**](https://www.taxamo.com/)**)、31 美元/月(**[**Octobat**](https://www.octobat.com/pricing)**)或€29/月(**[**Quaderno**](https://quaderno.io/pricing/)

**如果你问我，这是一个更好的方法。增值税是一件非常复杂的事情，很难弄清楚，就像所有的税收一样，你不会想搞砸的。有了一些服务(如 [Quaderno](https://quaderno.io/pricing/) ，你可能还会得到一些会计和发票。**

## **优势**

*   **增值税/发票/会计方面的工作要少得多。**

## **不足之处**

*   **你可能仍然需要注册一个欧盟增值税 ID，并自己[提交增值税申报表](https://help.sendowl.com/help/submitting-a-vat-moss-return)。(不过，对此我也不是 100%确定。)**
*   **您现在需要管理两个服务。**
*   **稍微贵一点。**
*   **还是要自己处理许可的问题。(同样，参见文章底部的一些关于如何处理这个问题的提示。)**

# **让经销商为您处理支付、发票和增值税，但使用您自己的许可系统**

****费用:5%+50/交易(** [**划桨**](https://timingapp.com/paddle) **)，略多的有**[**FastSpring**](https://fastspring.com/pricing/)**，10 美元/月加 3.5%+30/交易(**[**gum road**](https://gumroad.com/features/pricing)**)****

**在这种情况下，您的经销商充当您的[记录商家](https://paddle.com/taxes-fraud-compliance)。这意味着他们实际上是接受客户付款的企业，因此负责开具适当的发票、处理增值税等。在月底，你会得到一笔可观的钱转到你的银行账户，这也让你的账户更加容易。**

**这实际上是我使用的选项:我使用 [Paddle](https://timingapp.com/paddle) ，这让我可以避免处理所有的支付事务，并将其与完全自主开发的许可系统相结合。这样，我在定价、折扣和许可方面拥有充分的灵活性。我可以提供付费升级、限时许可以及企业需要的任何其他服务。如果一家经销商出现问题，换另一家还是相当容易的，因为受影响的只是支付处理部分(而不是许可)。**

**老实说，Paddle 也不怎么样。他们的结帐过程看起来很好，非常简单，但他们在实现新功能方面很慢，并且有一些边缘情况我希望以不同的方式处理。不过，我要说他们绝对是最不烂的服务；-) FastSpring 与他们的服务非常相似，但是费用要高一些。(他们的定价页面不再显示他们的定价，但以前是 8.9 %或 5.9%+95/交易左右)。**

**Gumroad 可能看起来是最便宜的选择，但请记住，他们的服务不是为了销售 Mac 应用程序或订阅而建立的。因此，虽然使用 Gumroad 可能会实现这些目标，但要实现相同的目标，您可能需要经历比使用 Paddle 或 FastSpring 更多的困难。**

## **优势**

*   **增值税，发票，会计都为你做了。**
*   **您仍然可以更换经销商，因为您没有被锁定在特定的许可系统中。**

## **不足之处**

*   **还是比较贵，不过我觉得还是值得的。**
*   ***还是*得自己处理发牌。(见下面的提示……)**

# **使用经销商及其现有的许可系统**

****费用:同上— 5 % + 50 (** [**桨**](https://timingapp.com/paddle) **)****

**这是最方便的选项，最类似于 Mac App Store。只需插入一个 SDK，让它处理所有的支付和许可问题。**

**例如，经销商 [Paddle](https://timingapp.com/paddle) 也为[提供一个 SDK 来为你处理许可](https://paddle.com/docs/introduction-mac)。老实说，我试图使用他们的系统，但发现他们的许可系统对我的需求来说太不灵活了(除非你用自己的定制解决方案替换大部分部件，在这种情况下，你可以自己构建整个系统)。**

**然而，你也可以使用第三方许可系统，比如 [DevMate](https://devmate.com/) 。其实 [DevMate 最近被 Paddle](https://devmate.com/faq) 收购了。鉴于 DevMate 最初是由 MacPaw 的优秀人员开发的， [Setapp](https://timingapp.com/setapp) 服务的开发者)，我可以想象他们的系统比最初的 Paddle SDK 要好得多。**

**然而，在这两种情况下，当您使用特定经销商的许可系统时，您会被他们牢牢地锁定。DevMate 可能仍然与 FastSpring 兼容，但我仍然有点担心，以防你想要更换经销商或迁移到不同的许可系统。**

## **优势**

*   **非常轻松省心，几乎是插队。**
*   **增值税，发票，会计都为你做了。**
*   **也不需要担心许可问题。**

## **不足之处**

*   **相当紧密的锁定。换一个不同的经销商(和不同的许可系统)会很痛苦。**

**这些是我能想到的付款方式。对于我的应用程序[计时](https://timingapp.com)，我使用了一个组合[桨](https://timingapp.com/paddle)用于支付、发票和增值税处理，加上我自己开发的许可系统。**

**对于大多数 Mac 开发者来说，明智的做法是将分销商如 [Paddle](https://timingapp.com/paddle) 或 [FastSpring](https://fastspring.com/pricing/) 与开源许可系统如 [CocoaFob](https://github.com/glebd/cocoafob) (也可参见[我的基于 Python 的 CocoaFob 许可生成器和服务器](https://github.com/MrMage/cocoafob/tree/python-cache-keys/python)或 [Aquatic Prime](https://github.com/bdrister/AquaticPrime) )结合起来。两者都很容易集成。这可以让您避免担心所有的支付问题，同时保持对许可系统的完全控制。(或者，我的朋友 [Christian Tietze](https://christiantietze.de/) 写了[一本关于如何用 FastSpring](https://christiantietze.de/books/make-money-outside-mac-app-store-fastspring/) 销售你的 Mac 应用的书。)**

**你使用什么样的许可系统？我错过了什么要点吗？用评论或者推文告诉我[**@ Daniel _ a _ a**](https://twitter.com/daniel_a_a)**！****