# 将我们的 Flask 后端从 Heroku 迁移到 AWS——指南

> 原文：<https://medium.com/hackernoon/moving-our-flask-backend-from-heroku-to-aws-the-guide-31b2b372291c>

最初的理由是纯粹的成本相关，但是性能的提高是巨大的。我们公司决定正式进入“蟑螂时代”，很遗憾，这意味着我们要将大量的服务器端实现移出 Heroku。Heroku 让事情变得简单，这是肯定的，但我们有大量的亚马逊信用，并感觉有点被利用了，因为纯粹的懒惰而付出更多，得到更少。

我不能说这很容易。我随便看了几十篇，都是提供部分信息或者过时/错误的信息。我在想“这太奇怪了，为什么没有简单明了的指南来告诉我们这些小而穷的初创公司如何远离 Heroku”。因此，经过大量的指导，堆栈溢出错误解决，AWS architect 1 对 1，以及一些处于放弃边缘的纯粹挫折，我在这里为您提供如何进行这一可怕举动的完整指导。

## 1.弹性豆茎

我们大多数人都已经使用过或者知道 EC2 实例，但是当对整个后端使用一个专用的 EC2 实例时，当工作负载发生变化时，它将变得更加难以扩展。因此我选择了使用 Elastic Beanstalk，这是一个易于使用的部署和扩展 web 应用程序的服务，而且是免费的。下面是如何为 Flask 设置的(*我用的是 EB CLI 界面，所以你需要事先下载:*[*http://docs . AWS . Amazon . com/elastic beanstalk/latest/DG/e b-CLI 3-install . html*](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3-install.html)*)*:

*   转到项目所在的文件夹
*   在命令行中键入: ***eb init***
*   选择您所在的地区，创建一个新的应用程序，输入它的名称，然后选择它的平台(在我们的例子中是 Python)。选择版本(最有可能是 2.7)，同意设置 SSH，选择已经在 AWS 中设置的现有密钥对，或者创建一个新的密钥对。

现在我们已经完成了 EB 的目录设置，我们可以创建我们的运行环境了。

*   在命令行中键入: ***eb create***

恭喜你。您刚刚创建了您的第一个电子商务环境。EB 现在已经创建了一个 EC2 实例，并为其附加了一个存储卷。现在，您可以进入 AWS EB 仪表板，查看新创建的环境。EB 非常智能，正如您所看到的，它的设置非常简单。它将您的文件夹压缩并上传到 AWS，并基于它对 Python 环境的了解构建服务器。它将查看您的 requirements.txt 文件以了解要下载什么库，并尝试找到您的 WSGI 配置以启动服务器。您可以进入“配置”选项卡，更改其扩展和网络。您还可以在仪表板中查看环境的健康状况，或者获取日志，如果您需要更多关于正在发生的事情的信息。

当然，我的执行带来了一些错误，重点是一种他们称之为“的生物”。ebextensions”。这是一个需要添加到项目中的文件夹，它由。配置文件。这些文件基本上允许您在设置环境时对其进行配置。

```
#my example.config
packages:
  yum:
    git: []
    libffi-devel: []
    libjpeg-turbo-devel: []

container_commands:
  01_wsgipass:
    command: 'echo "WSGIPassAuthorization On" >> ../wsgi.conf'
  AddGlobalWSGIGroupAccess:
    command: "if ! grep -q 'WSGIApplicationGroup %{GLOBAL}' ../wsgi.conf ; then echo 'WSGIApplicationGroup %{GLOBAL}' >> ../wsgi.conf; fi;"

option_settings:
  "aws:elasticbeanstalk:container:python":
    WSGIPath: application.py
    NumProcesses: 3
    NumThreads: 20
  "aws:elasticbeanstalk:application:environment":
    EXAMPLE_ENV_VAR: env_var_value
```

在我的例子中，我使用 ***yum*** 命令在机器上安装一些加密库和 git。我使用了***AWS:elastic beanstalk:application:environment***来添加我所有的环境变量。最重要的是，我有几个 WSGI 问题需要解决。告诉 EB 从哪个文件启动我的应用程序。 ***01_wsgipass*** 允许基本的 HTTP 认证头在传递给服务器时不被截断，以及***AddGlobalWSGIGroupAccess***由于我曾与本文相关的问题:[http://djm.io/deploying-scipy-into-aws-elastic-beanstalk/](http://djm.io/deploying-scipy-into-aws-elastic-beanstalk/)

好吧！现在，在您添加了您的。ebextensions 文件夹，只需返回到 CLI 并发出命令 ***eb deploy*** ，这基本上就是用新的更改重新部署您的服务器。

## 2.工人

我们还有一些事情要解决，因为我们还有一个工作进程在运行我们较长的任务，或者我们不希望阻塞服务器的任务。我们在 Heroku 上使用一个 Redis 队列和一个单独的 dyno 来生成我们当前的 worker 配置。幸运的是，Elastic Beanstalk 还附带了一个工作环境，设置起来非常简单。您需要做的是遵循与设置 web 环境相同的步骤，只是这一次选择您已经创建的现有应用程序，并在提示时选择创建一个工作环境。工人环境会提示你一个 SQS 的创作，我们现在就来谈谈。

EB 中工作环境的工作方式是通过使用 SQS 与 web env 通信。SQS 基本上是一个简单的队列服务，你可以发送信息。在这种情况下，我们的 web env 可以向 SQS 队列发送一条消息，工作线程安装了一个守护进程来监听该队列，并在消息到达时获取消息，执行所需的操作，然后返回 200 (ok)状态。如果它没有返回 200，消息将被发送到一个死队列，如果这些失败的请求需要重新发出，就可以使用这个死队列。

要实现这一点，需要设置什么:

*   在 flask 后端安装 [boto](https://aws.amazon.com/sdk-for-python/) (用于 Python 的 AWS SDK)并创建一个方法，将 SQS 消息发送到为 worker env *设置的所需队列(您可能需要为您的服务器设置适当的 AWS 权限，以允许您发送 SQS 消息)*。在我的例子中，我发送了一个由两个参数组成的 SQS 消息，一个函数名和一个参数列表。

```
def send_message_to_sqs(*data*):
    # Put the message in the queue
    m = boto.sqs.message.RawMessage()
    m.set_body(json.dumps(*data*))
    status = q.write(m)
    print statussend_message_to_sqs({'func':'save_to_s3_and_update_user', 'args':{'user_id':str(usr.id)}})
```

b.在 worker env 中有一个端点，以便将 SQS 消息发送到该端点。您可以在 Worker env 配置中配置端点名称。

c.我的 worker env 中的那个端点的方法基本上是根据函数名查看传入的消息，理解要调用什么函数，并给它一个参数列表。最后，如果一切顺利，它就返回 200 (ok)状态。

下面是亚马逊提供的一个例子 [web 环境](https://github.com/awslabs/eb-py-flask-signup)和 [worker 环境](https://github.com/awslabs/eb-py-flask-signup-worker)作为例子。

如果您能够达到这一点，那么您现在很可能已经配置了一个基本的弹性 Beanstalk web 应用程序，它带有一个与之交互的工作线程。万岁！

## 3.MongoDB

我们为 MongoDB 数据库使用了一个 Heroku 插件(mLab)。它使事情变得超级简单，他们为你提供全面的支持，为你设置和维护数据库。然而，我们决定给 AWS 一个公平的机会。这是一个相当长且详细的指南，所以我将把你链接到我在这上面找到的最好的指南:

[](https://docs.mongodb.org/ecosystem/platforms/amazon-ec2/) [## 亚马逊 EC2

### EC2 实例可以使用弹性块存储(EBS…)配置临时存储或永久存储

docs.mongodb.org](https://docs.mongodb.org/ecosystem/platforms/amazon-ec2/) 

如果您遇到问题或收到错误(如我们所做的)，只需提供一些提示:

1.  确保您有一个打开端口 22 和 27017 的安全组。您需要 SSH 到您的机器，因此您需要端口 22，27017 用于您的 MongoDB 连接。
2.  编辑/etc/mongod.conf，寻找 ***bind_ip*** 。如果它指向本地主机，您将无法从外部 ip 访问它。要么给它需要访问它的机器的 ip，要么完全注释掉这个命令。
3.  确保您为正在交互的数据库的 MongoDB 用户添加了一个 ***dbOwner*** 用户(或者符合您需求的合适角色)。通过连接到您的 mongo shell (SSHing 然后键入 ***mongo*** 或者从您的本地机器执行一个远程 ***mongo*** 命令)，然后发出一个***db . create user()***命令。我给新用户的角色是:

```
“roles” : [ {“role” : “dbOwner”, “db” : “my_database_name”} ]
```

如果在没有任何用户的情况下连接到数据库，您可能处于只读模式。确保您在 flask 后端使用正确的用户名和密码连接到数据库。

## 4.附加组件

Heroku 有许多简洁的插件，特别是在我的例子中，我发现它对 UI 日志和定期数据库备份有很大用处。没有人说你不能在 EB 上也这么做！

**伐木的书面记录**

 [## AWS 弹性豆茎

### AWS Elastic Beanstalk 是一种在 Amazon Web Services cloud 中快速部署和管理应用程序的简单方法…

help.papertrailapp.com](http://help.papertrailapp.com/kb/hosting-services/aws-elastic-beanstalk/) 

基本上就像将另一个配置文件添加到。ebextensions 并正确配置它:)

**定期备份您的 MongoDB**

[](https://github.com/theycallmeswift/node-mongodb-s3-backup) [## 他们称之为 meswift/node-mongodb-s3-backup

### node-mongodb-s3-backup——一个 node.js 包，使 MongoDB 数据库与 S3 同步变得简单。

github.com](https://github.com/theycallmeswift/node-mongodb-s3-backup) 

简单易用的节点包，每天将数据库备份到 S3。

## 最后的话

我希望这篇指南能够帮助你完成从 Heroku 到 AWS 的*旅程，或者至少向你展示了我个人对它的处理。自从我们搬迁后，我们肯定更具成本效益，我们的应用程序有更好的响应时间，并且非常满意:)*

> *[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。*
> 
> *要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)*
> 
> *如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！*