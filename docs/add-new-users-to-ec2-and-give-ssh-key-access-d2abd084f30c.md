# 向 EC2 添加新用户，并授予 SSH 密钥访问权限

> 原文：<https://medium.com/hackernoon/add-new-users-to-ec2-and-give-ssh-key-access-d2abd084f30c>

![](img/7cec94716b9da03a17fe7e9eac8ce6e1.png)

在这篇快速的帖子中，我将向您展示如何使用您自己的**私钥**向 **EC2 实例**和 **SSH** 添加新用户，而不是使用由 **AWS** 生成的私钥进行认证。

![](img/47357809b8fca7d248fc9a12d10fd62a.png)

使用其**公共 IP** 通过 **SSH** 连接到您的实例:

![](img/c7df795da920b4f7bdaa272a19c3e8c0.png)

接下来，使用以下命令创建一个新用户:

sudo adduser labouardy

![](img/01c822f35cbce0e6f0f8163d2798578e.png)

接下来，我们将 shell 会话切换到新帐户:

sudo su labouardy

创建**。ssh** 目录，并将目录权限改为 **700** (只有文件所有者可以读取、写入或打开目录):

|mkdir。嘘

|chmod 700。嘘

注意:确保您位于新用户的主目录中(例如: **/home/labouardy** )

在**中创建一个名为 **authorized_keys** 的空文件。ssh** 目录，并将其权限更改为 **600** (只有文件所有者可以读取或写入该文件)

|触摸授权键

|chmod 600 授权密钥

![](img/daca524e8eb01d9e915552fc7e5646cb.png)

最后，编辑 *authorized_keys* 文件并在你的公钥中粘贴:

![](img/41a890eb879f788b6156d364f5bd17c5.png)

一旦你完成了这些，回到你的机器，然后尝试使用你创建的新凭证和用户帐号进行 SSH:

![](img/c59e5a0ab7e2cf8ac27b29ab4ccbe59c.png)

我们现在以用户 **labouardy** 的身份登录