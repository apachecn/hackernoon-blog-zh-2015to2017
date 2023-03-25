# 下一个打电话的人:你的电话号码能说明什么？

> 原文：<https://medium.com/hackernoon/nextcaller-what-does-your-phone-number-say-about-you-1bc894d48c66>

我最近在黑客新闻上读到了关于电话初创公司 [NextCaller](https://nextcaller.com/) 的评论。什么是 NextCaller？根据他们的网站，他们提供“世界上最大的来电显示数据库。”

“来电显示，”你可能会问，“所以他们会返回我的全名，对应于我的电话号码？”实际上，NextCaller 将返回更多信息:家庭住址、房屋价值、薪水、婚姻状况、亲戚姓名、电子邮件地址、教育程度、脸书和 Twitter 链接，以及其他有用的信息。全都来自一个电话号码。

听起来有趣吗？让我们来看一个$0.10 NextCaller 查询的示例响应:

```
{
    "add_ons": {
        "code": null,
        "message": null,
        "results": {
            "nextcaller_advanced_caller_id": {
                "code": null,
                "message": null,
                "request_sid": "XXXXXXXXXXXXXXXX",
                "result": {
                    "records": [
                        {
                            "address": [
                                {
                                    "city": "XXXX",
                                    "country": "USA",
                                    "extended_zip": "XXXX",
                                    "line1": "XXX N Foo Bar Ave",
                                    "line2": "",
                                    "state": "AZ",
                                    "zip_code": "XXXXX"
                                }
                            ],
                            "age": "41",
                            "education": "Completed High School",
                            "email": "[XXX@gmail.com](mailto:XXX@gmail.com)",
                            "first_name": "John",
                            "first_pronounced": "JAHN",
                            "gender": "Male",
                            "high_net_worth": "",
                            "home_owner_status": "Own",
                            "household_income": "75k-100k",
                            "id": "XXX",
                            "last_name": "XXXX",
                            "length_of_residence": "11-15 years",
                            "linked_emails": [
                                "[XXX@gmail.com](mailto:XXX@gmail.com)"
                            ],
                            "marital_status": "Married",
                            "market_value": "200k-250k",
                            "middle_name": "D",
                            "name": "John XXX",
                            "occupation": "White Collar Worker",
                            "phone": [
                                {
                                    "carrier": "Bandwidth.com Clec Llc - Ca",
                                    "line_type": "Landline",
                                    "number": "XXXXXXXXXX"
                                }
                            ],
                            "presence_of_children": "No",
                            "relatives": [
                                {
                                    "id": "XXXX",
                                    "name": "Eric Doe"
                                },
                                {
                                    "id": "XXXX",
                                    "name": "Carie Doe"
                                },
                                {
                                    "id": "XXXX",
                                    "name": "Mark A Doe"
                                },
                                {
                                    "id": "XXXX",
                                    "name": "Frank G Doe"
                                }
                            ],
                            "social_links": [
                                {
                                    "type": "facebook",
                                    "url": "[https://www.facebook.com/XXXX](https://www.facebook.com/XXXX)"
                                }
                            ]
                        }
                    ]
                },
                "status": "successful"
            }
        },
        "status": "successful"
    },
    "caller_name": null,
    "carrier": null,
    "country_code": "US",
    "national_format": "(XXX) XXX-XXXX",
    "phone_number": "+XXXXXXXXXXX",
    "url": "[https://lookups.twilio.com/v1/PhoneNumbers/+XXXXXXXXXXX](https://lookups.twilio.com/v1/PhoneNumbers/+XXXXXXXXXXX)"
}
```

你有昨晚聚会上那个迷人女人的电话号码吗？不妨使用 Twilio API 在 NextCaller 上查找一下。

《福布斯》的一篇[文章](http://www.forbes.com/sites/tomgroenfeldt/2016/06/27/next-caller-delivers-detailed-information-on-inbound-calls/#3ce47d1c1ad1)解释了 NextCaller 如何试图推销他们的服务:

> “只需一个电话号码，我们的高级来电显示不仅可以告诉你客户的姓名、地址和电子邮件，甚至可以告诉你有多少人在 Twitter 上关注他们，以及他们的房子有什么样的屋顶，”Ronconi 说。“公司使用它进行营销、挖掘潜在客户和智能呼叫路由。我们的技术可以让您将正确的报价或代理与正确的客户配对，”他补充道。

听起来很棒。可以选择退出 NextCaller 吗？如果有，我在他们网站上找不到链接。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！