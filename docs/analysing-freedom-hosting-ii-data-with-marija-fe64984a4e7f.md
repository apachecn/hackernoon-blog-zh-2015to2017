# 用 Marija 分析 Freedom Hosting II 数据

> 原文：<https://medium.com/hackernoon/analysing-freedom-hosting-ii-data-with-marija-fe64984a4e7f>

或许你听说过自由主机 II 被黑的事。匿名入侵服务器，在最初以 0.1 比特币的价格出售数据后，他们很快免费发布了数据库。

The Verge 发表了这篇关于黑客的文章:

> 超过 10，000 个基于 Tor 的网站的访问者今天早上看到了一个令人震惊的公告:“你好，自由主机 II，你被黑客攻击了。”一个隶属于 Anonymous 的组织已经入侵了 Freedom Hosting II 的服务器，这是一个很受欢迎的托管网站的服务，只能通过 Tor 访问。在最初的声明发布大约六个小时后，该服务托管的所有网站仍然处于离线状态。

更多阅读请访问:[http://www . the verge . com/2017/2/3/14497992/freedom-hosting-ii-hacked-anonymous-dark-web-tor](http://www.theverge.com/2017/2/3/14497992/freedom-hosting-ii-hacked-anonymous-dark-web-tor)

该数据库包含所有类型的 Wordpress 网站，phpbb 和其他论坛。在数据库中有很多丑陋的数据，但是因为完整的数据库无论如何都是可用的，我们已经把它放到网上用于研究和分析。研究时要小心，因为数据库是未经审查和过滤的。

使用工具 DB2ES(【https://github.com/dutchcoders/db2es】T2)我们已经将所有 MySQL 数据库导出到 Elasticsearch。这使我们能够快速查询和搜索所有数据。现在我们可以使用 Marija(也是开源的，可以在 https://github.com/dutchcoders/marija 的[买到)来可视化数据。为了方便起见，我们在演示环境中提供了数据源。见](https://github.com/dutchcoders/marija)[http://demo.marija.io/](http://demo.marija.io/)。

Marija 使用起来还是有点粗糙，所以这里有一个关于如何使用它的小介绍。通过点击右边的眼睛启用 **freedomhosting2** 数据源。首先，您需要使用刷新图标刷新字段。所有字段名称都以表格名称为前缀。现在您可以添加一些字段。例如，如果你想看看有哪些用户在多个隐藏的 Wordpress 网站上使用不同的账户或不同的别名。添加以下字段:

*   (计划或理论的)纲要
*   wp_users.user_email
*   wp _ 用户.显示名称
*   phpbb_users.user_email
*   mybb_users.email

模式将安排所有用户链接到原始数据库。wp_users、phpbb_users 和 mybb_users 字段将在电子邮件地址上将不同的数据库链接在一起。

当您在“_exists_:wp_users.user_email”中键入以下查询时，您将获得包含 wp_users.user_email 字段的所有返回记录。现在你会看到一些 wordpress 站点有多少用户，以及这些用户之间的关系。你会看到两个节点连接着两个不同的站点，分别是“pedobear066@mail2tor.com”和“herpderp420”。两者都在两个不同的网站上为人所知，一个有不同的别名但相同的电子邮件，另一个有不同的电子邮件但相同的别名。

当您打开右侧的表格面板时，您可以深入查看内容。为了您的安全，所有的 html 标签(如图像和脚本)都被过滤。单击加号会将字段添加为一列。

![](img/eed8005ee5b4f28b89ab41be01348a74.png)

Visualisation of the wp_users

因为我们使用 Elasticsearch 作为数据源，你可以使用你能想到的所有 Elasticsearch 查询。比如:

*   架构:anonysfmoe4pgsth
*   使用关键字
*   特定字段的存在:**_ exists _:WP _ users . user _ email**

我们已经在 Github 中发布了所有表名(和字段前缀)的完整列表。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)