# 部署您的 React &。使用 Nginx 和 Supervisor 的 Linux 上的. NET 核心应用程序

> 原文：<https://medium.com/hackernoon/deploy-your-react-net-core-apps-on-linux-using-nginx-and-supervisor-5a29d0b6ef94>

![](img/98930c110a101902a2c04150d31a7472.png)

经过漫长的一天试图部署我的。NET Core 微服务到 IIS7(？)，我最终说服了我的客户将我们的应用程序移植到 linux 服务器上。

在这篇文章中，我将创建一个使用. NET 核心后端服务的简单的 React 应用程序，事实上，我的目标是写一些复杂的部署过程。以下是步骤:

*   创建一个示例 React 应用程序并获取生产就绪文件
*   创建样本。网络核心应用
*   连接并将它们发送到 Linux 机器
*   安装 Nginx 和配置—客户端部署
*   主管安装和配置—服务器部署

# 创建示例 React 应用程序

为了创建样板代码，我将使用 create-react-app，这是 react 应用程序的官方 cli。我对此毫无异议。

[在这里](https://github.com/facebookincubator/create-react-app)你可以找到更多信息

1.  安装它:

```
$ npm install -g create-react-app
```

2.创建您的项目:

```
$ create-react-app sample-react-app
```

3.检查一下是否一切正常。应用程序将在端口 3000 上运行。

```
$ cd sample-react-app
$ npm start
```

4.让我们为部署准备构建文件

```
$ npm run build
```

现在你的项目文件夹中有了一个包含`asset-manifest.json` `favicon.ico` `index.html`和`static.` Cool 的`build`文件夹，我们现在有了一个样本前端库。我们将把这个文件发送到远程服务器。

# 创建样本。网络核心应用

我可以使用 Visual Studio 2015 来创建我们的后端基础，但对于那些拥有 Mac 或 Linux 的人来说，我更喜欢使用 Yeoman。

1.  安装约曼:

```
$ npm install -g yo
```

2.安装 ASP.NET 发电机:

```
$ npm install -g generator-aspnet
```

3.创建您的。网络核心微服务:

*选择:
* Web 应用基础【无会员和授权】
* Bootstrap
命名:
* SampleMicroservice*

```
$ yo aspnet
```

进入创建的文件夹，改变`HomeController:`的索引方法

```
public IActionResult Index()
{
    return Json(new {
        Hello = "From the api"
    });
}
```

4.进入`Program.cs`内部，在`.UseKestrel()`后添加`.UseUrls("http://0.0.0.0:5000")`

运行服务并尝试点击`[http://localhost:5000](http://localhost:5000)`，确保 json 出现在屏幕上:

# 连接并将它们发送到 Linux 机器

对于廉价的 Linux VPS，我更喜欢 [DigitalOcean](http://digitalocean.com) 。对于你的个人项目，你可以用树莓派+ a 静态 IP 作为自己的服务器。

要发送我们到目前为止创建的内容，你可以使用版本控制系统，比如 Github 和 SVN，或者使用 SFTP 协议。

提醒 SFTP:

```
sftp username@remote_hostname_or_IP# Basic Commands:
pwd -> remote working dir
ls -> remote ls
lpwd -> your working dir
lls -> ls for your machine
get remoteFile localFile -> Get the file to the local machine
put localFile -> Send the file to the remote server# simply put l as a prefix to the commands for running them in local machine
```

# 安装 Nginx 和配置—客户端部署

因此，我们在我们的 Linux 服务器中，通过`ssh`连接，由`npm run build`提取的`build`文件夹在我们的工作区中。

SSH 提醒:

```
$ ssh user_name@IP
Then enter your pass
```

安装 Nginx:

```
$ sudo apt-get install nginx
```

通过 vim 或 nano 编辑配置文件:

```
vim -or nano- /etc/nginx/sites-available/default
```

在该文件中，会出现默认配置:

```
server {
        listen 80 default_server;
        listen [::]:80 default_server; # Some comments... root /var/www/html;  **# STATIC FILE LOCATION** # Some comments...
        index index.html index.htm index.nginx-debian.html; server_name _; location / {
                # Some comments...
                try_files $uri /index.html;   **# ADD THIS**
        } # Some comments...
}
```

首先，您应该将您的`build`文件夹中的所有文件移动到静态文件位置，默认为`/var/www/html`。

```
$ cp -a /your/build/folder/location/build/* /var/www/html/
```

在复制文件之前，你应该在 nginx 配置中的`location /`内添加`try_files $uri /index.html;`，因为你不想给试图直接进入你的单页应用程序中不同 url 的用户 404 not found 错误。您只需在这里说“任何路由都应该与`index.html`匹配，让我们将责任交给 React 路由器。”

重启 nginx 后，你的主页应该在 [http://your_ip](http://your_ip) 上运行:

```
$ sudo service nginx restart
```

# 主管安装和配置—服务器部署

我们又回到了我们的 Linux 服务器上，而**我们的 dotnet 核心项目文件夹**在我们的工作区中。

我更喜欢一步一步地解释:

## 1.创建一个用户来运行您未来的守护进程服务-。并使用该用户登录:

```
$ adduser sampleMicroService
...
$ usermod -aG sudo sampleMicroService
$ su sampleMicroService
```

## 2.通过[这些步骤安装 dotnet。](https://www.microsoft.com/net/core#linuxubuntu)

## 3.准备您的 dll 文件:

进入您的项目文件夹并提取部署文件:

```
$ dotnet restore$ dotnet run (optional to check that your app runs successfully)$ dotnet publish
```

`publish`文件夹应该在`/bin/Debug/netcoreapp1.x/`中

4.将`publish`文件夹中的所有文件移动到`/var`中的某个位置

```
$ mv /your/publish/folder/location/publish/* /var/SampleMicroservice/
```

注意:我们在部署服务器而不是本地机器上编译和发布 dll 的原因是，我们希望`/home/sampleMicroService/.nuget/packages`文件夹将其设置为 supervisor 配置中的一个环境变量。实际上这是 dotnet cli 中的一个错误，如果我们不设置环境变量`ArgumentNullException`就会抛出`path1 can't be null`消息。[这里的](https://github.com/dotnet/cli/issues/2385)是 bug 详情。

5.安装 supervisor 来创建您的守护进程:

```
$ sudo apt-get install supervisor
```

6.通过以下方式创建主管配置文件:

```
$ sudo vim -or nano-  /etc/supervisor/conf.d/sampleMicroService.conf
```

把这个放进去:

```
[program:sampleMicroService]
command=/usr/bin/dotnet /var/sampleMicroService/SampleMicroService.dll
directory=/var/sampleMicroService/
autostart=true
autorestart=true
stderr_logfile=/var/log/sampleMicroService.err.log
stdout_logfile=/var/log/sampleMicroService.out.log
environment=ASPNETCORE_ENVIRONMENT=Production,NUGET_PACKAGES="/home/anil/.nuget/packages"
user=sampleMicroService
stopsignal=INT
```

请确保在此处设置了 NUGET_PACKAGES 环境变量。

停止 supervisor，启用 systemctl 并启动 supervisor:

```
$ sudo service supervisor stop
$ sudo systemctl enable supervisor
$ sudo service supervisor start
```

从现在起，您可以通过以下方式启动/停止服务:

```
$ sudo supervisorctl start/stop/status sampleMicroService
```

检查错误或输出日志:

```
$ tail -f /var/log/sampleMicroService/sampleMicroService.err.log
$ tail -f /var/log/sampleMicroService/sampleMicroService.out.log
```

就是这样，你的微服务运行在`http://YOUR_IP:5000`

赞美诗如果你像我一样使用 digitalocean，你可以通过点击 droplet 菜单中的“添加域名”来轻松添加域名。

谢谢，

有时候我会发一些有用的信息: [@_skynyrd](https://twitter.com/_skynyrd)

顺便说一句，我的推荐人:

*   [官方文件](https://docs.microsoft.com/en-us/aspnet/core/publishing/linuxproduction)
*   [汉瑟曼的帖子](http://www.hanselman.com/blog/PublishingAnASPNETCoreWebsiteToACheapLinuxVMHost.aspx)
*   [Cameron white 90 的帖子](/@cameronbwhite90/deploy-react-webpack-to-digital-ocean-with-nginx-and-github-b83d1cf4be0b#.854bmxdvz)
*   [Stackoverflow](http://stackoverflow.com)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)