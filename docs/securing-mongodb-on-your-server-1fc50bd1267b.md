# 使用 Docker 保护 mongodb 设置

> 原文：<https://medium.com/hackernoon/securing-mongodb-on-your-server-1fc50bd1267b>

![](img/6f0331be9f2d530dd99ec136aff1c0c1.png)

最近，在不安全的 mongodb 服务器上出现了许多勒索黑客。由于 mongodb 的不安全设置或设置时忽略了一些细节，许多人成为了这次黑客攻击的受害者。

即使在服务器上设置了安全认证设置后，由于黑客设置的自动化脚本，很少有人在几秒钟内升级或重新安装时被黑客攻击。

关于 mongodb ransom hack 的更多信息—[https://naked security . sophos . com/2017/01/11/数千个 MongoDB-databases-compromised-and-hold-to-ransom/](https://nakedsecurity.sophos.com/2017/01/11/thousands-of-mongodb-databases-compromised-and-held-to-ransom/)

# 让我们得到保护

为了解决这些问题，我和我的朋友 [Prady](https://medium.com/u/7baa042378b3?source=post_page-----1fc50bd1267b--------------------------------) 用 Docker 设计了一个简单的可扩展的 mongodb 设置。我们已经在 Digital Ocean 上运行 Ubuntu 14.04 的虚拟专用服务器(VPS)上完成了这个设置。

首先，我们需要在我们的服务器上创建一个目录，这是我们的数据库实际驻留的地方。即我们数据库的物理位置

假设我们的应用程序名为“Sample ”,并适当地命名我们的容器、数据库和空间。

```
mkdir ~/sample_db
```

在你选择的港口创建 mongodb 容器。

众所周知，27017 是 mongodb 的默认端口。让我们通过在自定义端口上创建 mongodb 容器来使它不那么明显。

```
sudo docker run -d -p **YOUR_PORT**:27017 -v ~/sample_db:/data/db mongo
```

列出所有 docker 容器

```
sudo docker ps
```

您可以根据 application_name 或您选择的名称来命名 docker 容器，以便于记忆。当我们不这样做时——docker 会随机赋值，比如“口渴 _ 伦琴”

重命名 docker 容器

```
sudo docker rename thirsty_roentgen sampledb
```

我们创建了一个 docker 容器，但是没有对它进行身份验证。如果没有身份验证，创建的 mongodb 容器只能通过本地访问，也就是说，当您使用 ssh 凭证进入您的服务器时

要向外界公开这一点，您需要创建启用了身份验证的 mongodb 容器

因为您已经在运行一个容器，所以只需添加— auth

```
docker run — name sampledb -d mongo — auth
```

将导致如下错误

```
The container name “/sampledb” is already in use by container a2ddcec52f17d95ba067ab4e4e52621b74f762a3a2e2024e1a7852d592192b5c. You have to remove (or rename) that container to be able to reuse that name..
```

我们需要停止当前的 mongodb 实例并删除它，然后在启用身份验证的情况下重新创建

```
docker stop sampledb
docker rm sampledb
```

创建名为 sampledb 并启用了身份验证的 mongodb 容器(— auth)

```
sudo docker run -d — name sampledb -p 29019:27017 -v ~/sample_db:/data/db mongo — auth
```

列出集装箱

```
sudo docker ps
```

使用管理数据库运行 mongodb 实例

```
docker exec -it sampledb mongo admin
```

进入 mongodb shell —现在创建管理员用户

正在为 mongodb 服务器创建管理员用户

```
db.createUser({ user: ‘sample_admin’, pwd: ‘p@ssword’, roles: [ { role: “userAdminAnyDatabase”, db: “admin” } ] });
```

使用指定的用户和密码运行 mongodb

```
docker exec -it sampledb mongo -u sample_admin -p p@ssword — authenticationDatabase admin
```

进入 mongodb shell

创建所需的数据库

```
use mydatabase
```

为数据库安全性创建管理应用程序用户

```
db.createUser({ user: ‘mydb_admin’, pwd: ‘myp@ss’, roles: [“readWrite”, “dbAdmin”] });
```

然后创建您需要的集合

在下一个系列中，我们将让您知道如何使用 robomongo 连接到 mongodb 的这个安全设置。