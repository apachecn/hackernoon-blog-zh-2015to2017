# 在 Windows 和 Ubuntu 上安装 node.js

> 原文：<https://medium.com/hackernoon/installing-node-js-on-windows-and-ubuntu-d5707ace435b>

![](img/b25748e615960707c6e4bb28d6539435.png)

[礼遇](https://nodejs.org/en/about/resources/)

为了让[获得](http://ionicframework.com/docs/guide/installation.html)最新的 [Apache conrdova](http://cordova.apache.org/) ，需要确保 [node.js](http://nodejs.org/) (和 [npm](https://www.npmjs.com/) )存在。

从[http://nodejs.org/](http://nodejs.org/)下载最新 [node.js](https://hackernoon.com/tagged/nodejs)

打开下载的安装文件后，按照以下步骤操作:

让安装程序计算所需的空间。

![](img/83ae38053b2d3a5dee7164fc946cb16b.png)

因此，如果我们需要空间:

![](img/1bb1af1c8a761d55d1561dcb5bcea486.png)![](img/854a30361afa0d229a8a61c63cec2856.png)

选择安装节点的目标

![](img/f8e99987291f3bdcfc5dfa24f91ac6ab.png)

选择要安装的节点组件—这可能对服务器配置有用

![](img/94f9c44109580323faca7f45c72db774.png)![](img/b80c45beb075df0aa9b21e7a9831c7a2.png)![](img/0d75bf6663ff9774820a1992c1deac0c.png)![](img/d0cfecc72ac8b6d23f702df7785b8bcc.png)

在 [Ubuntu](https://hackernoon.com/tagged/ubuntu) 上安装 node 有多种方式，其中一种是[编译安装](https://thomashunter.name/blog/install-node-js-on-debian-6/)

一行一行地使用下面的命令

```
$ sudo apt-get install build-essential $ wget http://nodejs.org/dist/v0.8.16/node-v0.8.16.tar.gz $ tar -xzf node-v0.8.16.tar.gz $ cd node-v0.8.16/ $ ./configure $ make $ sudo make install
```

要验证安装，请执行以下操作:

```
$ node -v v0.8.16 $ npm -v 1.1.69
```

对，就是这样。任务完成了！

[照片](https://goo.gl/photos/2tKMDYrptwJKJZFN8)

*原载于*[*xameeramir . github . io*](http://xameeramir.github.io/install-node/)*。*