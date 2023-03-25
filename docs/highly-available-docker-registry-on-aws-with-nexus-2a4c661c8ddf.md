# 具有 Nexus 的 AWS 高可用性码头工人登记处

> 原文：<https://medium.com/hackernoon/highly-available-docker-registry-on-aws-with-nexus-2a4c661c8ddf>

![](img/bb1ac3cba660270555bc85015b45a25a.png)

Containers … Everywhere !

您是否想过如何构建一个高可用性和弹性的码头工人存储库来存储您的**码头工人图像**？

![](img/dfb97b6195594008f710ce14ba4442c2.png)

在本文中，我们将在**安全组**内设置一个 **EC2** 实例，并创建一条指向服务器**弹性 IP** 地址的 **A 记录**如下:

![](img/d35b9793d3e0fcc6298ad59e51dc3d07.png)

为了提供基础设施，我们将使用 **Terraform** 作为 **IaC** (基础设施作为代码)工具。使用此类工具的优势在于，能够在发生事故(**灾难恢复**)时，在不同的 **AWS 区域**(或不同的 **IaaS** 提供商)快速构建新环境。

首先克隆以下 **Github** 存储库:

> git 克隆体[https://github.com/mlabouardy/terraform-aws-labs.git](https://github.com/mlabouardy/terraform-aws-labs.git)

在 *docker-registry* 文件夹中，用您自己的 AWS 凭据更新*vars*(确保您有正确的 **IAM** 策略)。

我指定了一个 shell 脚本，在启动实例时用作 *user_data* 。它只需安装最新版本的 **Docker CE** 并将实例转换为 **Docker Swarm 模式**(以受益于 Nexus container 的复制&高可用性)

注意:当然，您可以使用**配置管理工具**如 **Ansible** 或**大厨**来配置创建后的服务器。

然后，发出以下命令来创建基础架构:

> terra form apply-var-file = variables . tfvars

一旦创建，您应该会看到您的实例的**弹性 IP** :

![](img/05a7bb1e6fd984c55e895a16c01cd1db.png)

通过 SSH 连接到您的实例:

> 宋承宪 ec2-user@35.177.167.36

确认**码头工人引擎**正在**集群模式**下运行:

![](img/49bcd684ceaf9d22c255328febb9b9d2.png)

检查 **Nexus** 服务是否正在运行:

![](img/ee5948ff1d4c45fbcb82ebfa33df43b8.png)

如果您回到您的 **AWS 管理控制台**。然后，导航到 **Route53 仪表盘**，您会看到一条指向实例 IP 地址的新的 A 记录已经创建。

![](img/4372d8838538e6cc5764b57ef57453f2.png)

将您喜爱的浏览器指向 **Nexus 仪表盘 URL**(registry . slowcoder . com:8081)。登录并创建一个 Docker 托管的注册表，如下所示:

![](img/3fce7b19b0f3137c0084d9377f172dc0.png)

编辑*/etc/docker/daemon . JSON*文件，它应该有以下内容:

> {
> 
> "不安全-注册表":[" registry . slowcoder . com:5000 "]
> 
> }

注意:对于生产，强烈建议使用由已知 CA 颁发的 **TLS** 证书来保护您的注册表。

重新启动 Docker 以使更改生效:

> 服务 docker 重启

使用 Nexus 凭证登录您的注册中心( *admin/admin123* ):

![](img/673d80a0d6bd8b599e17c035823b24dc.png)

要将新映像推送到注册表:

> docker 推送 registry . slow coder . com:5000/mlabouardy/movies-API:1 . 0 . 0-beta

![](img/25c87b3f178334cec647e298fab3a40e.png)

验证映像已被推送到远程存储库:

![](img/9a741dfc133c19d25aada2af81e07f37.png)

要提取 Docker 图像:

> docker pull registry . slow coder . com:5000/mlabouardy/movies-API:1 . 0 . 0-beta

![](img/a18b6210ac8b8966b6f0a12e390713aa.png)

注意:有时你会得到许多未使用的悬挂图像，这些图像会很快占用大量磁盘空间:

![](img/c3a891dbcff9f031078733f4a576c375.png)

您可以使用 [Nexus CLI](https://github.com/mlabouardy/nexus-cli) 工具或创建 **Nexus 任务**来清理旧的 Docker 映像:

![](img/0364a871066a7ddaae7fbfaa3fd3bc92.png)

按如下方式填充表单:

![](img/9b5a4be59b69fe81a0b93c23a72d5768.png)

上述任务将在每天午夜运行，从“ **mlabouardy** ”注册表中清除未使用的 docker 图像。