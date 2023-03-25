# 将 react . js(create-react-app)node . js 部署到数字海洋云主机

> 原文：<https://medium.com/hackernoon/deploy-react-js-create-react-app-node-js-to-digitalocean-cloud-hosting-72206310017b>

React.js & Express.js ES6 构建和部署

![](img/38c436c29b673d2ea8faad3f4f127538.png)

## 获取数字海洋帐户

我一直在为我自己和我的客户使用[数字海洋](https://m.do.co/c/bb792e37b9dd)，所以建议为你的项目使用它，只需根据你的项目大小选择 vps，起价为 5 美元或 10 美元，20 美元，价格非常灵活。提供 SSD 云托管，但价格不错，我不认为你可以在其他供应商那里得到相同质量的价格。除了支持非常快和良好的文件，友好的用户界面为最终用户。因此，让我们开始在[DigitalOcean.com](https://m.do.co/c/bb792e37b9dd)注册账户并部署你的应用吧

## 在数字海洋云 VPS 上安装 Ubuntu

*   这个教程我将使用 Ubuntu，我也推荐这个操作系统给你的 vps。但这是你的选择。我用的是 Ubuntu 16.04 版本。对于价格，这取决于你的项目有多大，有多少流量。对我来说，我开始每月 20 美元是一个好的开始，然后我们可以升级。
*   选择数据中心区域:DigitalOcean 有许多数据中心，这意味着如果您的访问者几乎来自美国纽约，您可以为您的项目选择一个数据中心，例如，如果我为一个项目选择所有来自越南的访问者，那么我可以选择一个靠近越南的数据中心，即新加坡。
*   选择附加选项:如果你想有额外的备份服务，或私人网络。
*   添加您的 ssh 密钥:您可以在您的计算机上生成您的 ssh 密钥，并将其复制到您的 vps，这意味着当您从 ssh 登录时，不必再输入 root 帐户的密码，节省您的时间和更多的安全性，如果您想知道如何生成 SSH 密钥并在 DigitalOcean 主机上使用它，请查看此[文章](https://www.digitalocean.com/community/tutorials/how-to-use-ssh-keys-with-digitalocean-droplets)
*   如果你想一次设置多个液滴，液滴默认值是 1。
*   你的水滴的名字，点击提交，就可以得到一杯咖啡，并等待一会数字海洋为你设置。直到你看到“编码快乐！”您的云 vps 已经可以使用了。
*   检查你在数字海洋注册的电子邮件地址，你会收到一封通知你的 VPS IP，root 帐号和密码的邮件。以下是您的电子邮件 Droplet 名称的格式:[您的 Droplet 名称] IP 地址:[您的-vps-IP]用户名:root 密码:[您的-root-Password-generated-by-robot]
*   使用以下命令
    `ssh root@YOUR-IP-ADDREESS`登录到您的 vps
*   现在输入你的超级用户密码。您将在第一时间要求更改新密码。
*   服务器将再次询问您当前的密码(这是您电子邮件中的密码)
*   输入新口令
*   确认密码，并记住它以备后用。
*   完成并愉快地设置您的云。

## 在您的云上配置防火墙

这是我们需要做的非常重要的一步。因此，我们需要重新配置我们的防火墙软件，以允许访问服务

我建议只为 80、443、ssh(端口 22)开放端口，但这取决于您的项目可能需要为其他服务开放更多的端口。在这个项目中，我们需要开放端口 80 用于 http 访问，443 https (ssl)和端口 22(用于 ssh 登录)这就足够了。

## 安装 Nginx

```
apt-get updatesudo apt-get install nginx
```

默认情况下，防火墙是不活动的，您可以通过运行命令`sudo ufw status`来检查它

```
sudo ufw app list
```

因此，让配置防火墙允许这些端口

```
sudo ufw allow 'Nginx Full'sudo ufw allow 'OpenSSH'sudo ufw enable
```

## 在 DigitalOcean Ubuntu 16.04 上设置节点

我们使用 Node.js 作为后端，并将提供 react 应用程序构建的静态文件。所以 Node.js 是必需的

访问 https://nodejs.org/en/download/package-manager/[查看文档](https://nodejs.org/en/download/package-manager/)

我们使用包管理来安装，下面是安装 Node.js v9 的命令

```
curl -sL https://deb.nodesource.com/setup_9.x | sudo -E bash -sudo apt-get install -y nodejs
```

成功安装 Node.js 后，我们可以通过在命令行中键入`node -v`来检查版本，您会看到“v9.3.0”

## 在 digital ocean Ubuntu 16.04 Cloud VPS 上安装 MongoDB v3.6

我们在后端 restful 服务中使用 Mongodb 作为数据库，所以让我们按照文档[https://docs . MongoDB . com/manual/tutorial/install-MongoDB-on-Ubuntu/](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/)安装 MongoDB

导入软件包管理系统使用的公钥

```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 2930ADAE8CAF5059EE73BB4B58712A2291FA4AD5
```

为 MongoDB (Ubuntu 16.04)创建列表文件

```
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.6 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.6.list
```

重新加载本地包数据库

```
sudo apt-get update
```

安装 MongoDB 的最新稳定版本

```
sudo apt-get install -y mongodb-org
```

启动 MongoDB(端口运行缺省值:27017)

```
sudo service mongod start
```

如果你想阻止 MongoDB

```
sudo service mongod stop
```

或者重启 MongoDB

```
sudo service mongod restart
```

## 安装 Nginx — Http 代理服务器。

让我向你简单解释一下为什么我们在这个 Nodejs Web 应用程序中使用 Nginx。示例:我们的聊天应用程序在端口 3000 上运行，这是 Nodejs 应用程序运行的默认端口。我们可以将端口更改为 3001 或 3002 或 8080 …因此，如果您将您的域 tabvn.com 指向数字海洋云 vps，那么在这种情况下，我们可以访问位于[http://tabvn.com:3000](http://tabvn.com:3000)的聊天应用程序，因此我们只需查看默认端口 80 中的 nodejs web 应用程序，例如位于[http://tabvn.com](http://tabvn.com)的应用程序，这就是我们使用 Nginx 的原因。

要安装 Nginx，请访问 http://nginx.org/en/linux_packages.html 官方[的文档](http://nginx.org/en/linux_packages.html)

因此，我们将在 Ubuntu 云托管 16.04 上运行以下命令

```
apt-get updatesudo apt-get install nginx
```

启动 nginx:打开你的 IP 地址 ex: [http://123.456.789](http://123.456.789) 你应该看到“欢迎使用 Nginx！”，我们云中的所有 Nginx 配置都在/etc/nginx/nginx.conf 中

```
sudo nginx
```

停止 Nginx

```
sudo nginx -s stop
```

重新加载 Nginx

```
nginx -s reload
```

## 修复了 Ubuntu 16.04 上的 bcrypt 问题

```
sudo apt-get install build-essential
```

## Nginx 配置示例支持 WebSocket

Nginx Websocket 文档:[http://nginx.org/en/docs/http/websocket.html](http://nginx.org/en/docs/http/websocket.html)

```
server {
        listen 80;
        root /var/www/html;
        location / {

                proxy_pass http://127.0.0.1:3001;
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection "upgrade";
        }

}
```

视频:【https://www.youtube.com/watch?v=wJsH45eWNBo 