# 如何使用 Buddy 和 Azure 设置 CI/CD

> 原文：<https://medium.com/hackernoon/how-to-set-up-ci-cd-with-buddy-and-azure-b9ec91e8c4ff>

![](img/5a75b3755f72606f6dae601b974913e4.png)

**下面这篇文章将指导你如何使用 Buddy 设置持续部署到 Azure。**

**阅读本指南后，您将能够**:

*   在[微软](https://hackernoon.com/tagged/microsoft) [Azure](https://hackernoon.com/tagged/azure) 上初始化持续部署
*   在 Buddy 中创建一个渠道来传递您的。NET 应用程序到 Azure
*   将 Buddy 配置为在部署之前测试您的应用

# 为什么我需要持续部署？

通常，开发人员在本地编写、编译和测试代码，然后手动将包上传到服务器。然而，一旦项目变得更大，更多的开发人员开始参与其中，**问题开始出现**:

*   部署准备变得越来越耗时，因为准备工作需要更多的时间。
*   随着每个版本的发布，软件迭代越来越长，这导致来自客户的反馈越来越少。
*   测试更加困难，因为每个团队成员都有自己的开发环境。
*   团队成员需要更频繁地沟通，增加了沟通失败的风险。

![](img/3b7baab5301ac1b8212514f4e75cb635.png)

上述问题可以通过在工作流中实现构建服务器和部署自动化来成功解决。

**对开发过程意味着什么？**

*   该软件在一个一致的环境中构建和测试，避免了对本地开发环境的依赖。
*   您可以节省设置构建、安装所需软件等的时间。
*   拥有一个集中的构建服务器提供了对代码度量的简单访问，使得用测试覆盖您的代码更加容易。
*   自动部署允许您更频繁地发布软件和接收反馈，整个过程被简化到最低限度。

正如你所看到的，这些都是你不应该轻易拒绝的好处。下面的指南将告诉您如何使用 Buddy 将持续部署应用到您的项目中，这样您就可以利用以上所有的优势。

# 喜欢你读的吗？[点击此处查看全文](https://buddy.works/guides/how-deploy-to-azure?utm_source=medium&utm_medium=post&utm_campaign=how-to-set-up-ci-cd-with-buddy-and-azure&utm_content=link)。

![](img/237fecb458d889482966108f09dae68f.png)[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)