# 这么多的测试术语，这么少的时间！

> 原文：<https://medium.com/hackernoon/boiling-down-series-test-terms-c80fdb8bc10b>

人们真的被 [web 开发](https://hackernoon.com/tagged/web-development)测试术语搞糊涂了。让我们试着解决这个问题:

**单元**——[测试](https://hackernoon.com/tagged/test)每一段单独的代码。

**集成** —一起测试多段代码。

**功能** —关注业务需求的集成测试。

**回归** —测试一个变化是否没有破坏任何之前工作的东西。

**冒烟/神智不清** —测试它不会立即爆炸。

**验收** —由实际最终用户完成的测试。

以上所有测试都可以手动*完成*，但只有一部分可以用*自动化工具完成。*

构建测试是上面任何一种已经被自动化并集成到你的构建系统中的测试类型。

# 一个不那么疯狂的故事:

全栈开发人员鲍勃·沙加特负责一个新项目。他的经理告诉他要好好测试一下。这是一个节点/反应面板。以下是他未来 6 个月的发展:

## 阶段 1:初始开发阶段

**节点 API:**
使用 mocha 为控制器、模型、服务等编写 ***单元测试*** 。
使用 mocha 编写 ***集成测试*** ，调用端点并断言响应。

**React :**
使用 jest 为 React 组件编写 ***单元测试*** 。
使用 selenium 进行 ***集成/功能*** *测试* 打开浏览器并伪装成用户。

## 第二阶段:即将发布！

QA 团队开始研究这个项目，发现了许多 bug。随着 Bob 修复它们，他当前的测试变成了**回归**测试**，**确保没有其他问题被破坏。

项目经理给一些最终用户(和 CEO)一个候选版本。这些是用户**验收**测试(UAT)。

## 第三阶段:发布！

这种产品是野生的。Bob 为每天午夜运行的 **Smoke/Sanity** check 站点写了一些硒测试。

就是这样！希望有帮助。

像往常一样，看看我的新工具，在 https://www.snaptest.io/[帮助你生成/组织硒测试](https://www.snaptest.io/)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！