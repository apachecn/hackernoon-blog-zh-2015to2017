# 如何在 Ubuntu 16/14 上用 PHP5-FPM 回退安装 NGINX 和 HHVM

> 原文：<https://medium.com/hackernoon/how-to-install-nginx-and-hhvm-with-php5-fpm-fallback-on-ubuntu-14-04-7faf07225013>

在本教程中，我将指导你安装 [NGINX](https://hackernoon.com/tagged/nginx) stable，HHVM 3.9 和 [PHP](https://hackernoon.com/tagged/php) 5 FPM。

*   [Nginx](https://nginx.org) 是一个很棒的 web 服务器或反向代理，它的默认配置比 Apache 2.4 的默认 MPM (Prefork)快得多。
*   HHVM 是一个使用 JIT(实时)编译方法的虚拟机。
*   PHP-FPM (FastCGI 进程管理器)是一个替代的 PHP FastCGI 实现，具有一些额外的功能，适用于任何规模的网站，尤其是繁忙的网站。

一点设置背景:

*   用 Ubuntu 16/14 LTS 清理 KVM 机器，带有 1GB RAM 和一个来自 [Vultr](http://www.vultr.com/?ref=6823526) 的 CPU 内核(如果捆绑 MySQL，小于 1GB 的 RAM 会给你带来麻烦)
*   Root 访问权限(否则您必须执行 sudo[命令])

首先，Nginx:

```
add-apt-repository ppa:nginx/stable
apt-get update && apt-get upgrade
apt-get install nginx
```

第二，HHVM:

```
apt-key adv --recv-keys --keyserver hkp://keyserver.ubuntu.com:80 0x5a16e7281be7a449
add-apt-repository "deb http://dl.hhvm.com/ubuntu $(lsb_release -sc) main"
apt-get update
apt-get install hhvm
update-rc.d hhvm defaults
/usr/bin/update-alternatives --install /usr/bin/php php /usr/bin/hhvm 60
/usr/share/hhvm/install_fastcgi.sh
```

我们想在 Unix 套接字上运行 HHVM，所以让我们编辑它的配置:

```
nano /etc/hhvm/server.ini
```

用注释“hhvm . server . port = 9000”；并添加“hhvm . server . file _ socket =/var/run/hhvm/hhvm . sock”

它应该是这样的:

```
; php options
pid = /var/run/hhvm/pid; hhvm specific
;hhvm.server.port = 9000
hhvm.server.file_socket = /var/run/hhvm/hhvm.sock
hhvm.server.type = fastcgi
hhvm.server.default_document = index.php
hhvm.log.use_log_file = true
hhvm.log.file = /var/log/hhvm/error.log
hhvm.repo.central.path = /var/run/hhvm/hhvm.hhbc
```

按 Ctrl + X 保存，然后按“Y”

现在 PHP-FPM:

```
apt-get install php5-fpm
```

默认情况下，这会安装 FPM 并监听/var/run/php5-fpm.sock

我们安装完毕！让我们现在配置东西。让我们首先为 nginx 创建一个上游配置文件:

```
nano /etc/nginx/conf.d/upstream.conf
```

添加以下内容并保存:

```
upstream php {
        server unix:/var/run/hhvm/hhvm.sock;
        server unix:/var/run/php5-fpm.sock backup;
}
```

如你所见，PHP-FPM 将是一个备份，以防 HHVM 失败。

现在让我们编辑 Nginx 的 HHVM 配置文件:

```
nano /etc/nginx/hhvm.confg
```

将“fastcgi _ pass 127 . 0 . 0 . 1:9000；”用“fastcgi _ pass php”。它应该是这样的:

```
location ~ \.(hh|php)$ {
    fastcgi_keep_conn on;
    fastcgi_pass   php;
    fastcgi_index  index.php;
    fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
    include        fastcgi_params;
}
```

使用以下命令重新启动服务:

```
service hhvm restart && service php5-fpm restart && service nginx restart
```

测试方式:

```
curl -I localhost/test.php
```

应该是一个 404(因为实际的 test.php 不存在),具有以下标题:

```
Server: nginx/1.8.0
X-Powered-By: HHVM/3.9.0
```

仅此而已！我们在 fastcgi 中有最新的稳定的 Nginx 和最新的 HHVM，还有 php-fpm 回退！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！