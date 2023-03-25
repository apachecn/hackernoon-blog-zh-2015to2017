# 使用 Terraform 将 AWS VPC 作为基础架构和代码进行管理

> 原文：<https://medium.com/hackernoon/manage-aws-vpc-as-infrastructure-as-code-with-terraform-55f2bdb3de2a>

![](img/40663ecc019fcd13d04b71fdfa845c69.png)

在本教程中，我将向您展示如何使用**地形**在不到 **1 分钟**的时间内设置一个 **VPC** ，如下图所示:

![](img/9702d61098bcc6f7777365d61dcb0703.png)

上面的 **VPC** 拓扑是将要实现的内容的最佳演示:

*   互联网无法访问**私有子网**(包括进出)
*   **公共子网**是可访问的，所有流量(0.0.0.0/0)被直接路由到**互联网网关**

在我们开始之前，本演示中使用的所有代码都可以在我的 [**Github**](https://github.com/mlabouardy/terraform-aws-labs) 中找到。

注意:我已经做了一个 [**教程**](http://www.blog.labouardy.com/manage-aws-infrastracture-as-code-with-terraform/) 关于如何开始使用 **Terraform** 所以确保阅读它以获得更多细节。

**1 —全局变量**

该文件包含特定于环境的配置，如区域名称、CIDR 块和 AWS 凭证…

**2 —配置 AWS 提供者**

**3 —创建一个 VPC**

**4 —创建子网**

为了使**公共子网**可由**互联网**寻址，我们需要一个**互联网网关**:

**5 —互联网网关**

为了允许流量从**公共子网**通过 **NAT 网关**到达互联网，我们需要创建一个新的**路由表**。

**6 —路由表**

接下来，我们将为每个子网创建一个安全组。

**7 —安全组**

**7 .1 —网络服务器 SG**

这个**安全组**允许 **HTTP/HTTPS** 和 **SSH** 从**任何地方**连接。

**7.2 —数据库 SG**

这个**安全组**只从**公共子网**启用 **MySQL 3306** 端口、 **ping** 和 **SSH** 。

现在我们将部署 **EC2** 实例，但在此之前，我们需要创建一个**密钥对**，以便稍后通过 **SSH** 连接到实例。

**8 —密钥对**

**9 — EC2 实例**

**9.1 —网络服务器实例**

这个实例将扮演一个**网络服务器的角色。**因此，我们向实例**用户数据**传递一个 shell 脚本 **install.sh** ，它包含安装 **Apache 服务器**的命令:

**9.2 —数据库实例**

一旦定义了所有需要的模板，确保将 **AWS** 凭证**变量设置为**环境变量**:**

|导出 AWS_ACCESS_KEY_ID= "您的访问密钥 ID "
|导出 AWS_SECRET_ACCESS_KEY= "您的秘密访问密钥"

注意:您可以始终使用您的根用户**对任何事情都有访问权限，但是出于安全考虑，建议您只使用有限权限的用户帐户。所以使用 **AWS IAM** 创建一个新的。**

要查看 terraform 计划如何创建资源类型“ **terraform 计划**”。要创建基础设施类型" **terraform 应用**":

[![](img/3c4c9b9369492dbd61351d6e16584935.png)](https://asciinema.org/a/9wqCgDworzVqM4jDSs6cIHCWJ)

这将调出 **VPC** ，以及所有必要的资源。现在，在您的 [**AWS 管理控制台**](https://console.aws.amazon.com/) 中，您应该看到创建的资源:

![](img/40223551e155b0f48a23b973fa920635.png)

如果你点击**子网**菜单，你应该看到**公共** & **私有子网**:

![](img/78ae96b9c1c275f4feb7641a03dc1c88.png)

同样适用于**路由表**:

![](img/ac0a1b1b42f04ca49a950f52eb11cb30.png)

以及**互联网网关**:

![](img/6205cbd2b096ae051b53c83a97b8c760.png)

**安全组**也:

![](img/4a2a580d6c892e2c331dbf63d68551c7.png)

**web 服务器安全组:**

![](img/53970b903b3610b506a4607fa9d38ef2.png)

**数据库安全组:**

![](img/bb616eb8641fa6a6b1ac7cc29f787645.png)

最后是 **EC2 实例:**

![](img/e30a12c062d6c8ca41733feb7b0847c8.png)

**网络服务器实例:**

![](img/20d844be0cf2f1c6b9b971f69a2cab8f.png)

**数据库实例:**

![](img/72974f183e4e24407a9ca5c3759327f7.png)

不要忘记通过键入" **terraform destroy** "来销毁不需要的资源:

[![](img/c917cbac7c4903dca61511fcdef29759.png)](https://asciinema.org/a/0VRbQdMmqVa7E6HhLK0bqTrat)