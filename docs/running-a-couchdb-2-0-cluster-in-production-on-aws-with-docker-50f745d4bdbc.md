# 使用 Docker 在 AWS 上运行 CouchDB 2 集群

> 原文：<https://medium.com/hackernoon/running-a-couchdb-2-0-cluster-in-production-on-aws-with-docker-50f745d4bdbc>

![](img/2fcf8a97767b97c5bbb7b772be7287b5.png)

既然 CouchDB 2 是一个开箱即用的多主数据库，可以扩展以存储大量数据，那么 CouchDB 领域的事情正在升温！

不幸的是，当谈到如何在生产中使用 CouchDB 2 时，文档仍然有点不足。本教程的重点是带您一步步完成使用 AWS 和 Docker 在生产环境中设置 CouchDB 集群的过程。我们已经为数字投件箱和分级系统 [Quizster](https://quizster.co) 使用了类似的设置，它运行得非常好！

下面的设置使用开源软件，因此，它可以很容易地适用于谷歌云平台，Azure 或任何其他主机提供商，即没有供应商锁定。而且，因为我们使用的是开源软件，你也可以设置一个本地环境来进行开发！(VirtualBox 和游民在这方面很棒)

**为什么我们要使用 Docker？**

保持数据库的最新版本是一件非常麻烦的事情。一个最新的趋势是建立一个新的服务器，并在每次需要升级时迁移数据。在某些情况下，这是最好的选择，但是通过使用 Docker，我们还可以选择在发布新的 CouchDB docker 映像时发布一个 *docker 更新*。这样，我们就不需要担心我们的发行版是否有最新的 CouchDB 二进制文件，也不需要从依赖地狱中挣扎出来。此外，我们可以轻松地建立一个新的服务器，在这个服务器上安装 docker，然后为 CouchDB 运行 docker 映像！Docker 还有一些很好的内置功能，用于在服务器重启或 CouchDB 崩溃时处理重启。

我们的[初始设计](https://github.com/redgeoff/couchdb-docker-service)相当雄心勃勃，使用 Docker Swarm 和 AWS 的网络文件系统，名为 EFS。这种设计的优势在于，您可以建立一个 docker swarm 节点集群，然后只需使用 *docker 服务规模*来添加更多的 CouchDB 节点。**然而，最大的障碍是我们发现在 EFS 上运行 CouchDB 会使数据库慢 10 倍以上！**此外，Docker Swarm 似乎不允许基于任务槽路由到 Swarm 节点。因此，我们决定放弃 Docker Swarm，转而采用将 CouchDB 图像静态绑定到特定服务器的设计。(用 Docker Swarm 管理持久存储是一个众所周知的问题，目前还没有真正出现解决这个问题的方法)。

## 我们要做的是:

1.  在 AWS 上创建两个 EC2 实例，都运行 Docker。每个节点将位于不同的可用性区域(物理位置)。
2.  在每个 EC2 实例上运行 CouchDB 映像的一个实例
3.  运行简单的脚本来连接 CouchDB 节点
4.  使用负载平衡器根据负载和可用性将流量分配给每个节点。负载平衡器还将用于通过 SSL 为数据库流量提供服务。

注意:AWS 有一个免费层，但是它不会涵盖按照本教程中的步骤所产生的所有成本。幸运的是，AWS 按小时收费，所以你可以很容易地按照这个教程，然后销毁所有的碎片，而不会产生太多的成本。如果您继续在一个更便宜的地区使用这种设置，例如在美国西部地区，您将会看到每月大约 26 美元的账单(负载平衡器 16 美元 EC2 服务器 10 美元)。这对于一个生产就绪的 2 节点 CouchDB 集群来说是相当不错的！

我假设你没有 AWS 经验。如果这个假设是错误的，那么请随意跳过。

## 步骤 1-创建 AWS 帐户

[创建免费 AWS 账户](https://aws.amazon.com/free/)

![](img/30e41412893229c8341b6661afd1c2dc.png)

## 步骤 2 —导入您的公共 SSH 密钥

概述:像大多数现代主机提供商一样，AWS 鼓励用户通过 SSH 密钥连接到他们的服务器，而不是使用密码，因为密码更容易被破解。

搜索 EC2 服务

![](img/3c88b665bd5e1c6789df80eb902ff364.png)

选择*密钥对*

![](img/c799e1e39378f02e67a930db05b3f5d4.png)

点击*导入密钥对*。然后您需要粘贴您的公共 SSH 密钥并点击 *Import* 。在基于 Mac/Linux 的系统上，该文本位于~/中。ssh/id_rsa.pub

![](img/f981a64a71cf06138f3e223b28fc10bb.png)

## 步骤 3 —创建安全组

概述:安全组允许您的服务器在私有云中相互通信，同时向外界公开特定端口。我们将创建 2 个安全组，因为这种配置将为我们将来进行更改提供很大的灵活性。

从 EC2 仪表板中，单击*安全组*

![](img/73d97de1545497c3d8162cb890a94164.png)

单击创建安全组

![](img/b96b6443ca3f5ef3f23c480a163fdc63.png)

输入 *ssh* 的名称和描述，并从任意位置指定端口 22 上的入站规则。

![](img/42ca8441254a4b0aa8b0389912f08d0b.png)

添加这条规则简化了我们的设置，但是暴露了一个安全漏洞，任何机器都可以通过 SSH 进入我们的服务器(假设他们有我们的 SSH 密钥)。因此，在您完成本教程后，您应该删除端口 22 规则，并用[设置一个 VPN 来代替](/@redgeoff/using-a-vpn-server-to-connect-to-your-aws-vpc-for-just-the-cost-of-an-ec2-nano-instance-3c81269c71c2)。

重复上面的步骤来创建一个新的安全组，除了调用这个新组 *couchdb-load-balancer* 并创建一个规则来允许来自任何地方的端口 443 上的入站连接。

![](img/51546fa7dcf87f54476a7e2e240f72ed.png)

完成后，您应该有 3 个安全组:

![](img/7b55b1cfa1b492e162bca731ea34fb7b.png)

## 步骤 4 —创建第一个 EC2 实例

返回 EC2 仪表板，然后点击*启动实例*

![](img/c58fef2c1e53de452c004201c422119d.png)

选择 Ubuntu(你当然可以选择几乎任何其他运行 docker 的操作系统，但本教程是为 Ubuntu 量身定制的)

![](img/63d1be90f895ead87750622e6239c1a7.png)

选择 *t2.nano* 点击*查看并启动*

![](img/255603b2534bd056015e0e3deb3ebb16.png)

在下一个屏幕上，点击*编辑安全组*

![](img/afe3496d05f204bad8146df3e27c8927.png)

选择 *ssh* 和*默认*安全组，点击*查看并启动*

![](img/b59d29d6c324107ac9e70e72cc52be59.png)

然后点击*启动*

![](img/0e8332e7ab31c55b4975d131138d846d.png)

选择您在上面导入的密钥对，然后单击*启动实例*

![](img/3c0c97ec2248aab89132495a7013276d.png)

点击*查看实例*。选择实例，记下公共 DNS 和私有 IP。我们将这个公共 DNS 称为 DB1 公共 DNS，这个私有 IP 称为 DB1 私有 IP。

![](img/a44cbba1994ea162c43ea97e023dedfd.png)

注意:如果您停止然后启动此实例，公共 DNS 将会改变。

## 步骤 5—安装 Docker 并运行 CouchDB 容器

SSH 到 EC2 实例

```
$ ssh ubuntu@DB1-PUBLIC-DNS
```

下载并运行脚本来配置 Ubuntu 和 Docker

```
$ git clone [https://github.com/redgeoff/docker-ce-vagrant](https://github.com/redgeoff/docker-ce-vagrant)
$ cd [docker-ce-vagrant](https://github.com/redgeoff/docker-ce-vagrant)
$ sudo ./ubuntu.sh # Select "keep the local version ... "
$ sudo ./docker.sh
```

创建一个目录来存放您的数据库文件

```
$ mkdir /home/ubuntu/common
```

运行一个 CouchDB Docker 容器，并确保相应地替换 *DB1 私有 IP* 。

```
$ sudo docker run -d --name couchdb \
  --log-opt max-size=100m \
  --restart always \
  -p 5984:5984 \
  -p 5986:5986 \
  -p 4369:4369 \
  -p 9100-9200:9100-9200 \
  -v /home/ubuntu/common/data:/opt/couchdb/data \
  -e COUCHDB_USER='admin' \
  -e COUCHDB_PASSWORD='-pbkdf2-b1eb7a68b0778a529c68d30749954e9e430417fb,4da0f8f1d98ce649a9c5a3845241ae24,10' \
  -e COUCHDB_SECRET='mysecret' \
  -e NODENAME='DB1-PRIVATE-IP' \
  couchdb \
  -setcookie mycookie
```

注意事项:

1.  Docker 只需下载一次映像，然后在所有后续启动/重启时运行容器。
2.  *- restart always* 参数确保 CouchDB 节点在崩溃或服务器重启时会自动重启
3.  服务器中的所有节点必须使用相同的值。上面的值将产生密码 *admin。*您可以使用`[couch-hash-pwd](https://github.com/redgeoff/couch-hash-pwd)`实用程序来生成这个散列。例如，如果你的密码是`mypassword`，你可以使用`couchdb-hash-pwd -p mypassword`

启用 CORS，以便您的应用程序可以从另一个域/子域与数据库通信。

```
$ curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
$ sudo apt-get install -y nodejs build-essential
$ sudo npm install npm -g
$ sudo npm install -g add-cors-to-couchdb
$ add-cors-to-couchdb [http://localhost:5984](http://localhost:5984) -u admin -p admin
```

## 步骤 6—创建另一个 EC2 实例

概述:我们现在将创建另一个 EC2 实例，然后运行另一个 CouchDB docker 容器。大部分步骤和之前一样。(本教程没有介绍的另一种方法是，创建第一个 EC2 实例的 Amazon 机器映像(AMI ),然后使用这个 AMI 创建其他实例——如果您要启动许多节点，这是一个不错的选择)。

返回 EC2 仪表板，选择*实例*

![](img/2aad4df433c059949a1c1396f8a1d76f.png)

选择第一个实例，然后选择*像这样启动*

![](img/a7ab44d0367757aa98686884e88e7734.png)

单击页面顶部的*配置实例*选项卡，并确保选择不同的子网/区域。为什么？嗯，我们希望我们的两个 CouchDB 节点位于不同的物理位置，在 AWS 世界中也称为可用性区域。这样，如果一个区域发生自然灾害，我们不会丢失任何数据，因为我们的另一个节点将保持完整。(注意:AWS 发挥其魔力，确保在不同的可用性区域之间传输数据超快，但区域之间的数据传输要慢得多。因此，您不应该尝试跨不同的 AWS 区域运行节点集群。

![](img/ec787b93af1362aacadaacc2d2e260d1.png)

点击*查看并启动*、*启动、*选择你的 SSH 密钥，点击*启动实例*。

记下这个新实例的*公共 DNS* 和*私有 IP* 并重复步骤 5 来更新 Ubuntu，安装 docker 并运行 CouchDB 容器。在 *docker run* 命令中，确保使用第二个 EC2 实例的私有 IP。

## 步骤 7—创建集群

SSH 进入**或者** EC2 实例并运行以下命令。确保相应地替换 DB1 私有 IP 和 DB2 私有 IP。该脚本连接两个节点并创建系统数据库。

```
$ git clone [https://gist.github.com/redgeoff/5099f46ae63acbd8da1137e2ed436a7c](https://gist.github.com/redgeoff/5099f46ae63acbd8da1137e2ed436a7c) create-cluster
$ cd create-cluster
$ chmod +x ./create-cluster.sh
$ ./create-cluster.sh admin admin 5984 5986 "DB1-PRIVATE-IP DB2-PRIVATE-IP"
```

然后，您可以使用`curl [http://admin:admin@localhost:5984/_membership](http://admin:admin@localhost:5984/_membershipto)`来确保您的集群已经正确配置。在`all_nodes`条目中，您应该看到 DB1 私有 IP 和 DB2 私有 IP 的值。如果没有，请仔细检查 docker run 命令中的参数。注意:COUCHDB_USER、COUCHDB_PASSWORD、COUCHDB_SECRET 和 setcookie 后使用的值必须相同。参见[节点管理](http://docs.couchdb.org/en/stable/cluster/nodes.html)了解更多关于如何排除集群故障的信息。

## 步骤 8-导入 SSL 证书

如果您还没有 SSL 证书，我强烈建议您购买一个，因为通过不安全的连接传输数据库数据不会影响生产。如果你没有 SSL 证书，并希望购买一个，那么 42 美元/年的 [AlphaSSL 通配符证书](https://www.ssl2buy.com/alphassl-wildcard.php)很划算。如果您希望在没有 SSL 的情况下继续，请跳过这一步。

点击页面左上角的立方体，搜索*证书管理器*

![](img/8ed027a76658dc9d823649c67f84afed.png)

点击*开始使用*

![](img/662643372004a3a87bf20365b3e89330.png)

点击*导入证书*

![](img/f123d5f3d4bce53d56ab4cde717cc999.png)

输入证书详情，点击*审核导入*，然后点击*导入*。

![](img/f83fad209553b8474552446612ee94f2.png)

## 步骤 9-设置负载平衡器

在 EC2 仪表板上，选择*负载平衡器*。

![](img/7dcdd8afd7193ba6db12a8c5dab5c7b5.png)

点击*创建负载平衡器*

![](img/70a2e4348c448f9dcf7f5be2b52b8093.png)

选择*应用负载平衡器*

![](img/84fa659d032c7a6cf1b6058f58c4150c.png)

指定 *HTTPS* 和端口 *443* 。如果您希望不使用 SSL(不推荐)，那么您可以使用 *HTTP* 和端口 *80* 。

![](img/dad920061b315390de87f81a59964189.png)

选择所有可用区域并点击*下一步:配置安全设置*。

![](img/6d1e74ab7ffcad3c59e1b654fddd0fab.png)

选择现有证书，然后单击*下一步:配置安全组*。

![](img/da69bb3f537c131f4dbf60262bd76840.png)

选择 couchdb-load-balancer 和默认安全组，然后单击 *Next: Configure Routing* 。

![](img/d8ca68cb47418ecc36b4c37d362e2c93.png)

配置路由，点击*下一步:注册目标*

![](img/a9ea210faa39c32d14d27a40b34fed06.png)

选择您的两个 EC2 实例并点击*添加到注册的*。

![](img/c74e358e260ecb07f68220f42a846136.png)

然后点击*创建。*

## 步骤 10—配置 DNS

概述:我们将通过 AWS 的 awesome Route 53 服务设置 DNS 路由，因为它可以动态映射到我们的负载平衡器。

点击左上角的立方体，搜索*53 号公路*

![](img/84f111e830a906a1ba893f419514b103.png)

点击*立即开始*

![](img/8c4aca1651337d1ab1919f63a86209fb.png)

点击*创建托管区域*

![](img/987e973d0d1c5a33af0bd807a1e9a2db.png)

输入托管区域的详细信息

![](img/8c35da755901dec747c9fc0da2755b6a.png)

选中*别名*框，点击*别名目标*并选择您的负载平衡器。

![](img/4f5a66502ee316eaf802a6aa227ebf58.png)

记下您的托管区域中的名称服务器，例如

![](img/55bb10b17319a9f182e9eeffd18eecd5.png)

访问您注册域名的域名注册机构，例如 GoDaddy、Google Domains、AWS 等，并将您的域名指向这些域名服务器。你可能要等几分钟，直到 DNS 切换。

## 第十一步——放松

通过访问[https://db.mydomain.com/_utils](https://db.mydomain.com/_utils)启动 Fauxton，并使用 admin/admin 登录。是时候放松一下了！

(注意:如果 DNS 传播缓慢，您可以通过负载平衡器的公共 DNS 访问您的数据库，例如[https://LOAD-BALANCER-Public-DNS/_ utils。](https://LOAD-BALANCER-PUBLIC-DNS/_utils.)点击浏览器显示的 SSL 警告即可)

![](img/c808ac78246d3727d5584231fa12567e.png)

## 步骤 12 —当 CouchDB 的新版本可用时进行更新

这个设置最酷的一点是，只需在所有机器上运行以下命令，就可以更新到 CouchDB 的最新版本:

```
$ sudo docker pull couchdb
$ sudo docker rm couchdb --force
$ sudo docker run -d --name couchdb ... # See docker run above
```

而且，这可以一次完成一个节点，因为 CouchDB API 保持了向后兼容性。当然，有一个备份总是最好的做法，以防发生意外。

如果你喜欢这个教程，请喜欢并分享它。如果您有任何反馈，请在下面留下。

# 关于作者

Geoff Cox 是 MSON T21 的创造者，这是一种新的声明式编程语言，它将允许任何人可视化地开发软件。他喜欢承担雄心勃勃但令妻子抓狂的项目，比如创建数据库和分布式数据同步系统。你可以在 redgeoff.com[或联系他](https://redgeoff.com) [@CoxGeoffrey](https://twitter.com/coxgeoffrey) 或在 github 阅读他的更多帖子。