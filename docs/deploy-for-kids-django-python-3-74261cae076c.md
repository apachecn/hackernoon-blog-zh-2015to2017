# 为孩子部署— Django 和 Python 3

> 原文：<https://medium.com/hackernoon/deploy-for-kids-django-python-3-74261cae076c>

![](img/d0914c9a1a5e51321c3459f20be449a2.png)

许多人在部署时遇到问题的原因是他们没有足够重视细节。当您熟悉所有相关部分时，部署就很容易了。你必须知道如何通过 ssh 进行认证，习惯命令行和 Linux，了解如何配置和设置你的项目，了解什么是服务静态文件，什么是 guni corn……好吧，没那么简单。这就是为什么有很多部署工具、工具包和教程的原因。目前，有了 Ansible、Docker 和其他孩子们正在使用的东西，部署起来更容易了，但是幕后发生的事情变得更抽象了。

也许在几年后，这个帖子会过时，如果它还没有服务器和其他东西的话。无论如何，正如我将在这里展示的，只有少数人想学习如何部署 Django，但是如果它至少能帮助一个人，我就满足了。

享受这份**老式**指南吧！

## 服务器

我猜你没有服务器或者 [AWS](https://aws.amazon.com/) 账号、[数字海洋](https://www.digitalocean.com/)、 [Linode](https://www.linode.com/) …什么都没有！你必须在其中一个中创建一个帐户，并启动一个带有你想要的发行版的服务器。如果这是你的第一次，不要用 AWS，因为它比其他的要复杂得多。

在本教程中，我使用的是 Ubuntu 16.04，这是你能看到的最常见的发行版。如果你愿意，你也可以选择一个 Debian。

## 初始设置

配置服务器时区

```
sudo locale-gen --no-purge --lang pt_BR  # I'm using pt_BR, because HUE HUE BR BR
sudo dpkg-reconfigure tzdata
```

更新和升级操作系统包:

```
sudo apt-get update 
sudo apt-get -y upgrade
```

## 在 Python 3.5 上安装 Python 3.6

用 Python 3.6 替换我们发行版中默认的 Python 3.5。

```
sudo apt-get update
sudo add-apt-repository ppa:jonathonf/python-3.6
sudo apt-get install python3.6
sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.5 1
sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.6 2
```

当您键入 python3 时，您可以选择操作系统将调用哪个 Python 版本。

```
sudo update-alternatives --config python3
```

遇到问题，请看这里:

[](http://ubuntuhandbook.org/index.php/2017/07/install-python-3-6-1-in-ubuntu-16-04-lts/) [## 如何在 Ubuntu 16.04 LTS 安装 Python 3.6.1

### 这个快速教程将向你展示如何通过 PPA 在 Ubuntu 16.04 LTS 安装最新的 Python 3.6.1。Ubuntu 16…

ubuntuhandbook.org](http://ubuntuhandbook.org/index.php/2017/07/install-python-3-6-1-in-ubuntu-16-04-lts/) 

## 安装操作系统要求

```
sudo apt-get install python3-pip nginx supervisor git git-core libpq-dev python-dev 
python-virtualenv
```

如果您的项目有更多的操作系统需求，也要安装它们。

## Python3 的 VirtualEnvWrapper

我是 [VirtualEnvWrapper](https://virtualenvwrapper.readthedocs.io/en/latest/) 的粉丝。这非常简单，可以在同一个地方创建我所有的虚拟环境。那是个人选择，不喜欢就用你知道怎么用的。

首先，安装 **virtualenvwrapper** ，然后定义将 virtualenvs 放在哪里。(WORKON_HOME)。

如果需要在多个 Python 版本中使用，必须定义 VIRTUALENVWRAPPER_PYTHON。在这里，我使用的是 python3 的 always。这不成问题，因为您可以创建一个 virtualenv，指出 env 将使用哪种 Python。

```
sudo pip3 install virtualenvwrapper
echo 'export WORKON_HOME=~/Envs' >> ~/.bashrc
echo ‘export VIRTUALENVWRAPPER_PYTHON=`which python3`’ >> ~/.bashrc
echo 'source /usr/local/bin/virtualenvwrapper.sh' >> ~/.bashrc
source ~/.bashrc
```

现在，创建您的 virtualenv 并定义 Python 将要使用的内容。

```
mkvirtualenv name_venv --python=python3
```

VirtualEnvWrapper 真的很好用。如果你想激活一个虚拟 env，你可以使用上的**工作。**

```
workon name_venv
```

要停用此虚拟设备，请执行以下操作:

```
deactivate
```

为了**去掉**一个 virtualenv:

```
rmvirtualenv name_venv
```

## 为 GitHub 认证生成 SSH

您不希望(也不应该)在服务器上编写密码来 git pull 您的项目。

正在生成 SSH 密钥:

```
cd ~/.ssh
ssh-keygen -t rsa -b 4096 -C "dev@email.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```

查看并复制您公钥的内容( **id_rsa.pub** )

```
cat ~/.ssh/id_rsa.pub
```

然后登录你的 GitHub 账户，进入[设置> SSH 和 GPG 键](https://github.com/settings/keys)。单击新的 SSH 密钥，给它起一个名字，比如(“测试服务器密钥”)并在 Key 中粘贴您的 id_rsa.pub 的内容

## 克隆您的 Django 项目

从 GitHub 复制 SSH 链接来克隆您的项目。在这种情况下，我使用一个我刚刚发现的项目作为例子。

```
git clone git@github.com:kirpit/django-sample-app.git
```

在项目文件夹中，安装项目需求。

记住你必须在你的虚拟环境中

```
cd django-sample-app/
pip install -r requirements.txt
```

现在，对您的部署进行必要的修改，比如创建一个 settings_local.py 文件，更改数据库设置或任何特定于您的项目的内容。

完成后，运行迁移并收集静态文件(如果您正在使用的话)。

```
python manage.py migrate
python manage.py collectstatic
```

## 配置 NGINX

Nginx 和 Apache 一样，是一个完全独立的世界。现在，你只需要最基本的。

**/etc/nginx/sites-available/**是一个目录，您必须将可用站点的配置文件放在其中。还有另外一个目录，**/etc/nginx/sites-enabled/**显示哪些站点是启用的。它们是一回事，但是放在 enabled 上的将由 Nginx 提供服务。

通常在站点可用的地方创建配置文件，并创建一个指向站点启用的符号链接。

首先，我将从 Nginx 中删除默认站点。

```
sudo rm /etc/nginx/sites-enabled/default
```

现在，为您的站点创建配置文件。(如果不知道怎么用 viM，用 nano 代替 VI)

```
sudo vi /etc/nginx/sites-available/mysite
```

在您的文件中，更改必要的路径:

```
server {
 listen 80;
 access_log /home/username/logs/access.log;
 error_log /home/username/logs/error.log;

 server_name nome-site.com.br;

 location / {
 proxy_pass [http://127.0.0.1:8000;](http://127.0.0.1:8000;) 

 proxy_pass_header Server;
 proxy_set_header X-Forwarded-Host $server_name;
 proxy_set_header X-Real-IP $remote_addr;
 proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
 proxy_set_header Host $http_host;

 }

 location /static {

   alias /home/username/project_path/static/;

 }
```

并创建一个符号链接到启用了以下功能的站点:

```
sudo ln -s /etc/nginx/sites-available/mysite /etc/nginx/sites-enabled/mysite
```

重启 Nginx:

```
sudo service nginx restart
```

好的，如果你坚持到这里，如果你访问你的网站，你会看到一个来自 Nginx 的 502 错误网关。那是因为这里什么都没有: [http://127.0.0.1:8000](http://127.0.0.1:8000)

现在，配置网站在 8000 端口上运行。

## 配置 Gunicorn

你们还活着吗？不要放弃，我们就快到了。

在你的 virtualenv(还记得 workon name_env 吗？)安装 Gunicorn

```
pip install gunicorn
```

在您的项目目录中，创建一个 **gunicorn_conf** 文件:

```
bind = "127.0.0.1:8000"
logfile = "/home/username/logs/gunicorn.log"
workers = 3
```

现在，如果你运行 Gunicorn，你会看到你的网站工作！

```
/home/username/Envs/name_venv/bin/gunicorn project.wsgi:application -c gunicorn_conf
```

但是你打算怎么办呢？在屏幕中运行这个命令，然后离开？当然不是！你将使用[超级管理员](http://supervisord.org/)来控制 Gunicorn。

## 配置主管

现在创建一个 gunicorn.conf:

```
sudo vi /etc/supervisor/conf.d/gunicorn.conf
```

内容就是这样:

```
[program:gunicorn]
command=/home/username/Envs/name_venv/bin/gunicorn project.wsgi:application -c /home/username/project/project_django/gunicorn_conf
directory=/home/username/project/project-django
user=username
autostart=true
autorestart=true
redirect_stderr=true
```

现在，你只需告诉主管，镇上有一个新流程，主管会处理它:

```
sudo supervisorctl reread
sudo supervisorctl update
sudo supervisorctl restart gunicorn
```

瞧啊！你将会有一次新的跑步。

## 结论

部署过程中涉及到很多事情。你必须配置一个防火墙，可能你必须服务于多个静态文件夹，等等，等等…但是你必须从某个地方开始。

不敢相信我写了一整篇帖子都没有用任何 GIF。最后，请注意我在这里使用的所有路径。

![](img/54f8285c9ebd7511db39f440cc6384cd.png)

哎哟..

*最初发表于* [*【费尔南多·阿尔维斯】*](http://fernandofreitasalves.com/deploy-for-kids-django-python-3/) *。*