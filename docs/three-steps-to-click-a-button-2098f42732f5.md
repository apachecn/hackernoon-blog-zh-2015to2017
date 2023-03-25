# 点击按钮的三个步骤

> 原文：<https://medium.com/hackernoon/three-steps-to-click-a-button-2098f42732f5>

![](img/cfe200942bac5aa588639406dfae7c1f.png)

Photo by [James Sutton](https://unsplash.com/photos/qXn5L9BqRbE?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/computer-button?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

我一直在帮助几个项目的测试自动化。每次我们重新开始，都有良好的意愿保持我们的回归套件干净。每次我们都要折腾东西，花时间调整我们的脚本，以避免每一个小的 UI 变化都带来大的变化。有没有降低开销的选择？我知道我的规格代表了最初的要求。我也注意到可用性工程师可以从中受益。

我花了一些时间来分析我们在自动化规范过程中的抽象层次。我研究了我们在每个层面上使用的语言类型，这是我的发现:

1.  用例的层次。其中定义了业务流程和参与者；
2.  屏幕和场景的层次。用例转换成屏幕和场景；
3.  控制和动作的级别。其中屏幕和场景被转换成低级 UI 特定的控件和动作。

让我们用一些或多或少的实际例子来看看每个层面。

# 用例的层次

我假设你了解行为驱动开发、[小黄瓜语言](https://github.com/cucumber/cucumber/wiki/Gherkin)和像 [SpecFlow](http://specflow.org/) 这样的工具。在这个层面上使用小黄瓜语言很有意义。这一级别与我们产品的商业意义密切相关，因此，如果商业人士能够理解这种测试，这将是很有价值的。因此，让我们的小黄瓜场景使用产品所有者能够理解并使用的语言:

```
**Feature**: Shopping
  **Scenario**: **I** want to buy a present for Christmas and I don't have any clue what to buy
    **Given** I know my budget
      **And** I know whom I buy a present
    **When** I ask for help to choose a present 
     **And** I tell about the person I buy a present 
     **And** I tell my budget 
    **Then** I'm offered with relevant options
```

我敢打赌，我的产品负责人可以阅读上面的脚本，也可以毫不费力地发现错误。我甚至希望他能为我创造一些场景，这样我们就可以互相帮助，有一个合作点。

# 屏幕和场景的级别

在这个层次上，我将参考马丁·福勒解释的[页面对象模式](https://martinfowler.com/bliki/PageObject.html)。页面对象模式有助于将低级的点击和对象从更有意义的屏幕和页面块中分离出来。这些屏幕和块是特定于产品的。一些团队甚至从这些块中创建一个目录，并在产品中重用它们。这些块通常与可用性工程师正在做的工作紧密相关，这可能是另一篇文章的主题——仍然不像输入、按钮或标签。为了展示一个例子，我使用了 [NOpenPage](https://github.com/corker/NOpenPage) 库 page 对象模式的一个轻量级实现，它扩展了 [Selenium](https://github.com/SeleniumHQ/selenium) 。该示例显示了 SpecFlow 步骤:

```
[Binding]
**public** **class** **MySteps**
{
    [**When**(@"I ask for help to choose a present")]
    **public** **void** **When_I_ask_for_help_to_choose_a_present**()
    {
        Browser.**Do**(TheShopPage.Navigate);
        Browser.On<TheShopPage>().**AskForHelpToChooseAPresent**();
    } [**Then**(@"I'm offered with relevant options")]
    **public** **void** **Then_Im_offered_with_relevant_options**()
    {
        Browser.On<TheShopPage>().**AssertRelevantOptionsOffered**();
    }
}
```

这个级别需要一个有开发技能的人的努力，尽管当我们谈论页面、页面上的块和场景时，语言对于可用性工程师来说可能是清楚的。让我们假设寻求帮助就像一个巫师。那么 AskForHelpToChooseAPresent 方法可能如下所示:

```
**public** **class** **TheShopPage** : Page
{
    **public** **TheShopPage**(IPageContext context) : **base**(context)
    {
    } **public** **static** **void** **Navigate**(IWebDriver driver)
    {
        driver.**Navigate**().**GoToUrl**("https://theshop.it");
    } **public** **void** **AskForHelpToChooseAPresent**(**string** gender, **int** age)
    {
        Control<Gender>().**Choose**(gender);
        Control<Age>().**Choose**(age);
    } **public** **void** **AssertRelevantOptionsOffered**()
    {
        Control<Offers>().**AssertRelevantOptionsOffered**();
    }
}
```

# 控制和行动水平

现在应该清楚了。我们到了底部。按钮、标签和输入都在这里。我们将所有与 Selenium 相关的代码放在用 NOpenPage 实现的 page 对象中:

```
**public** **class** **Gender** : PageControl
{
    **public** **Gender**(IPageControlContext context) : **base**(By.**ClassName**("gender"), context)
    {
    } **public** **void** **Choose**(**string** gender)
    {
        **var** input = Element.**FindElement**(By.**Id**(gender));
        input.**Click**();
    }
}
```

通过遵循这三个抽象层次:

*   我有业务人员和可用性工程师可以理解的清晰的规范；
*   我可以很容易地扩展我的眼镜。因为我知道我需要在什么层面上引入改变。

这里的例子非常抽象，虽然我只是想展示在顶部使用商业语言，在中间使用可用性语言，在底部保留底层技术细节的想法。这种方法对我为自动化测试编写干净的代码很有帮助。

快乐编码，
迈克尔·博里索夫

*原载于 2017 年 3 月 4 日*[*corker . github . io*](https://corker.github.io/2017/03/04/three-steps-to-click-a-button/)*。*