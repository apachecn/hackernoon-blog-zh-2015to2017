# 中小企业可以在业务中使用机器智能吗？

> 原文：<https://medium.com/hackernoon/can-smes-use-machine-intelligence-for-their-business-b7b663c2415d>

![](img/6da654b01088798261091a1d999468fd.png)

有没有想过为什么埃隆·马斯克和斯蒂芬·霍金害怕人工智能？这可能是因为我们无法预测技术变化的速度。去年，我们看到机器在最抽象的棋盘游戏“围棋”中击败了人类。机器驾驶汽车已经成为现实。机器在大多数流程中取代人类的日子不远了。

那么计算机怎么可能比人类强呢？分析这一点的一个方法是逆向工程人脑的想法。让我们试着找出大脑做出决定的基础，并尝试用代码来表示这种逻辑。以医生为例。

一个病人去看医生诊断。医生按照下面的程序来治疗这个人。

1.  观察可见症状。
2.  确定具有相同症状的疾病列表。
3.  做个验血来进一步缩小疾病范围。
4.  缩小到最有可能的疾病。
5.  开始药物治疗并验证结果。
6.  如果结果不能证实给定的疾病，开始对下一个最可能的疾病进行药物治疗。

同样的工作流程可以很容易地用逻辑来表示:

```
input(symptoms)
If symptoms in disease[symptoms]:
	Add disease in [possible_diseases]Input(blood_test_result)
If blood_test_result not in disease in[possible_diseases]
	Remove disease from [possible_diseases]X = 0
Start medication(possible_diseases[x])
	Return result;while result = null:
	x++
Start medication(possible_diseases[x])
```

很明显，医生使用一个更复杂的工作流程来识别疾病。但是在商业中，我们雇佣人类来做决定，像项目管理、资源管理、库存管理等等。相对来说比较简单，涉及的利害关系可能不是生与死。市场上跑的是什么？什么时候应该订购多少库存？或者说，哪个员工应该做什么任务？这些决定是基于我们的大脑如何分析利用我们的感觉器官收集的数据而做出的。

随着员工变得疲惫、生病或擅离职守，分析数据的能力会下降。因此，效率降低。这在机器的情况下是不可能的。越来越多的软件被定期开发，以提供比以前更好和更一致的结果。它帮助我们更好地指导我们的业务，并做出战略决策。

有了人工智能，在短短半年内实现人类 50 万年的知识将成为可能。因此，根据其采用和实施方式，未来 6 个月可能会发生转变。

现在还为时尚早，但人工智能和机器学习对公司运营方式的影响将很快飙升，因为它们的所有者意识到了这种能力以及对它的需求。尤其是，害怕输给利用人工智能力量的竞争对手。对于博弈论爱好者来说，看到 AI 取代人类将是最佳结果纳什均衡并不太牵强。意，必然。

美国零售市场现在能够准确预测他们的顾客什么时候会有孩子。此外，[向电子商务网站上购买婴儿产品的顾客提供折扣](http://kupanzone.com/store/amazon-coupons/)。在机器智能的一个类似例子中，美国银行注意到，表现最好的员工是那些一起休息的人。最后，他们制定了集体休息政策，使员工的绩效提高了 23%。

![](img/0450e872e458e0d933045d5ea25e03d7.png)

你是否想知道你的企业是否大到真的需要机器智能？答案是:显然，是的。鉴于你已经在文章中讲到这里了。

即使是最小的食品供应商也可以使用基本算法，根据他的销售额来确定什么库存应该在什么时间间隔更新。另一个例子是，四个人的销售团队中有一个人超额完成任务。我们可以编写算法来识别他与客户交谈的内容，从而使他获得更好的转换，同样的方法也可以教给其他团队成员。

可以肯定的是，公司增加新工作的速度将大幅放缓。企业将不再把员工人数多视为一种资产。他们会认为这是一种负担。在这种情况下，新技能将变得非常有价值。云计算、数据分割、[移动解决方案](http://blog.tripin.co.in/app-development-cost/)，机器学习将成为未来技能需求的前沿。
经济的每个部门都将受到人工智能中断的影响。这是无法回避的事实。获得人工智能和机器学习的深度技能将是防范过时风险的保险。提供这类服务的代理机构会在市场排行榜上名列前茅。

> 技术是一股浪潮:你可以驾驭它，也可以消灭它

不管你的个人立场是勒德分子还是技术爱好者，技术的快速发展不会很快放缓。越来越多未能适应的小企业将发现自己落在了后面，而那些学会跟上的精明企业将获得回报。

ref:
[http://www . Business world . in/article/Smarter-Systems-WIll-Enable-Better-Business-and-Opportunities-Ramesh-Loganath-Prof-IIT-海德拉巴/15-11-2017-131761/](http://www.businessworld.in/article/Smarter-Systems-WIll-Enable-Better-Business-and-Opportunities-Ramesh-Loganath-Prof-IIT-Hyderabad/15-11-2017-131761/)
[https://app data room . com/eight-ways-technology-changing-Business/](https://appdataroom.com/eight-ways-technology-changing-business/)