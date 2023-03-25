# 如何用 Python 写的机器学习系统预测 NBA

> 原文：<https://medium.com/hackernoon/how-to-create-your-own-machine-learning-predictive-system-in-the-nba-using-python-7189d964a371>

如果你想打赌或者只是出于求知欲，哪个体育极客不想创建自己的预测比赛的系统？如今，在篮球参考和 awesome [机器学习](https://hackernoon.com/tagged/machine-learning)等网站上可以获得高级统计数据，这些库可以用于每一种编程语言。这不会是一个全面的 DIY 指南，我只是想谈谈几个月来我在使用它时的发现，并分享一些对启动非常有用的代码。

## 我从哪里开始？

机器学习通过建立模型来工作，这些模型从历史数据中捕捉权重和特征之间的关系，然后使用这些模型来预测未来的结果。因此，你需要了解这项运动，思考哪些变量代表未来的表现，建立一个包含这些信息的数据库，并对历史数据运行机器学习算法，以分析方式为这些变量分配权重。

## 建立自己的数据库

我花了相当长的时间来建立一个 NBA 和 NCAA 刮板下载完整的赛季(逐场比赛)从篮球参考。关于关系数据库，你可能遇到的所有问题都在我的 scraper 中得到了解决，并且保证你能够唯一地关联信息。
[我的刮刀](https://github.com/FranGoitia/basketball-reference)以复杂的 json 格式模拟比赛，捕捉篮球比赛中发生的高级事情。为了在您自己的数据库中表示这些信息，您需要定义一个模式并插入这些信息。我使用 SQLAlchemy 编写模型，这些模型可用于创建数据库和构建分析系统。我的 [github repo](https://github.com/FranGoitia/basketball-analytics) 上都有。

## 预测比赛

Scikit-Learn 是用 [Python](https://hackernoon.com/tagged/python) 构建机器学习系统的途径。您需要找出哪些属性最适合根据历史表现来预测未来的匹配。如前所述，了解这项运动可以让你选择更高级的指标，如迪安·奥利弗的四个因素。这些，结合其他人类分析(如拉斯维加斯线)效果最好。

## 结果

如果你建立自己的机器学习模型，你会发现你可以以大约 70%的正确率预测赢家。虽然不足以通过赌博赢钱，但仍然比 Espn 专家和许多学术论文要好。你还会学到很多关于这项运动、数据库、机器学习和 Python 的知识。

[第二部分](/@frgoitia/how-to-predict-the-nba-with-a-machine-learning-system-written-in-python-part-ii-f276b19520b9)