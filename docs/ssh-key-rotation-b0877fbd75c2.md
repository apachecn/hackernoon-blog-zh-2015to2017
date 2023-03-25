# SSH 密钥旋转

> 原文：<https://medium.com/hackernoon/ssh-key-rotation-b0877fbd75c2>

SSH 密钥轮换！更多周六早晨的乐趣。

![](img/89d345160ed159421a1fd3d2611b29a2.png)

在某些圈子里，ssh 密钥轮换是可怕的，被认为是一个巨大的难题。在安全圈里，缺乏密钥轮换是一个成熟的目标来妥协和破解所有的东西。

我们如何解决这个问题？你可以写一个 bash 脚本，遍历一系列主机，然后对每个主机执行` *ssh-copy-id`* ,但是这不会使旧的密钥失效，它只是添加新的密钥。只解决了问题的一半。尽管如此，对于解决方案的初始设置来说，这个命令还是很不错的。

> ssh-copy-id -i ~/。ssh/mykey user @ host
> 【https://www.ssh.com/ssh/copy-id 

解决方案:Ansible 并不是什么新东西，它已经存在了一段时间，但是令人惊讶的是没有人使用它。这里有一些关于 http://docs.ansible.com/的信息。
我们要重点介绍的功能是 authorized_key 模块[http://docs . ansi ble . com/ansi ble/latest/authorized _ key _ module . html](http://docs.ansible.com/ansible/latest/authorized_key_module.html)

实验室设置:

![](img/e3f286c4c335b0a3047bc82846b1a929.png)

需要的是，SSH 密钥已经在您要更改的每台主机上设置好了。
Ansible 运行在您的笔记本电脑上，这就是我们将要进行 ssh 的地方。
具有共享 ssh 密钥的主机的清单。对于我的实验室，我使用了一堆物理的树莓 pi，因为我喜欢它们，它让我感觉自己像一个小小的疯狂科学家，在我的桌子上运行着一个迷你 pi 集群。有些日子我宁愿有一个贝奥武夫集群的机器运行，但嘿，你得凑合。

[https://www.youtube.com/watch?v=8LsxmQV8AXk](https://www.youtube.com/watch?v=8LsxmQV8AXk):切换到 linux

下面是一个库存示例——非常简单

> [pi]
> 192.168.1.6 用户=pi
> 192.168.1.7 用户=pi
> 192.168.1.8 用户=pi
> 192.168.1.9 用户=pi

Ansible 将在一个不同于我的主键的位置生成一个新的 ssh 密钥，因为现在我只是把它拿出来，不想破坏所有的东西。一旦我们离开实验室阶段，使用您的中心位置就很容易了。未来的实现将有一个 KMS，这样我就可以安全地存储所有的密钥，但现在让我们不安全地这样做，我们可以稍后讨论最佳实践。跳马:【https://www.vaultproject.io/docs/secrets/ssh/index.html】T21

太好了，我们在每台主机上都有清单和 ssh 密钥。让我们向我们的主机问好，确保实验室已配置好

*ansi ble pi-a "/bin/echo hello world "-I hosts-u pi—private-key = " ~/。ssh/id_rsa*

> 192.168.1.6 |成功| RC = 0 > >
> hello world
> 92.168.1.7 |成功| RC = 0>>
> hello world
> 92.168.1.8 |成功| RC = 0>>
> hello world
> 92.168.1.9 |成功| rc=0 > >
> hello world

太好了，接下来让我们改变密钥，希望不要破坏一切。我们将创建一个可行的行动手册，其中包含一些任务。第一个任务是在我们的新位置本地生成一个新的 ssh-key。

> 名称:“为根用户设置授权密钥”
> 主机:pi
> 用户:pi
> 
> 任务:
> —名称:创建新的 ssh 密钥对
> local_action:命令 ssh-keygen-t RSA-N " "-q-f ~/test/id _ RSA

接下来，我们将有一个任务，使用一个独占属性获取新的密钥文件并将其推送到主机。独占属性很重要，因为没有它，我们可以保留旧的密钥。如果我们想要部署多个键，我们可以执行多个步骤，第一步是独占推送，然后在没有独占设置的情况下添加我们的键。

> — name:为 pi 用户设置 authorized _ keys
> authorized _ key:user = pi key = " { { item } } " state = present exclusive = yes
> with _ file:
> —~/test/id _ RSA . pub

最后，我们有两个步骤来移动新生成的密钥到我们的超级秘密档案，我还没有解决这个问题，但清理是需要的。目前它正试图运行多次，这是一个错误。

> - name:移动密钥对
> local _ action:command mv ~/test/id _ RSA ~/test/id _ RSA . bak
> run _ once:true
> 
> — name:移动密钥对
> local _ action:command mv ~/test/id _ RSA . pub ~/test/id _ RSA . pub . bak
> run _ once:true

综合起来我们得到了。
我意识到这种格式很糟糕，所以这里有一个要点:[https://gist . github . com/ridingintraft/a 89 b 8 a 27 CB 6b 9148 C5 BF 7 f 9 DD 4294 c 35](https://gist.github.com/ridingintraffic/a89b8a27cb6b9148c5bf7f9dd4294c35)

> - name:"为 root 用户设置授权密钥"
> hosts: pi
> user: pi
> 
> 任务:
> —名称:创建新的 ssh 密钥对
> local_action:命令 ssh-keygen-t RSA-N " "-q-f ~/test/id _ RSA
> 
> —名称:为 pi 用户设置 authorized _ keys
> authorized _ key:user = pi key = " { { item } } " state = present exclusive = yes
> with _ file:
> —~/test/id _ RSA . pub
> 
> - name:移动密钥对
> local _ action:command mv ~/test/id _ RSA ~/test/id _ RSA . bak
> run _ once:true
> 
> - name:移动密钥对
> local _ action:command mv ~/test/id _ RSA . pub ~/test/id _ RSA . pub . bak
> run _ once:true

让我们点燃它，换一些钥匙！

> $ ansi ble-playbook-I hosts playbook/ssh-key-push-revoke-old . yml
> 
> 播放[为根用户设置授权密钥]* * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
> 
> 任务[设置]* * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
> ok:[192 . 168 . 1 . 9]
> 
> 任务[创建新的 ssh 密钥对]* * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
> 已更改:[192.168.1.9 - > localhost]
> 
> 任务[为 pi 用户设置授权密钥]* * * * * * * * * * * * * * * * * * * * * * * * * * * * *
> 已更改:[192 . 168 . 1 . 9]=>(item = ssh-RSA xxxxx XXX @ XXX)
> 
> 任务[移动密钥对]* * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
> 变更:[192.168.1.9 - >本地主机]
> 
> 任务【移动密钥对】* * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
> 变更:【192 . 168 . 1 . 9->localhost】
> 重试，使用:—limit[@ playbook/ssh-key-push-revoke-old](http://twitter.com/playbook/ssh-key-push-revoke-old)。重试
> 
> 播放摘要* * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
> 192 . 168 . 1 . 9:ok = 1 changed = 1 unreachable = 0 failed = 0

成功了！我们更换了钥匙，比一小时前更安全了！！！

我在此过程中使用了大量的链接，所以我将在这里一一列出:
[https://der pops . bike/2014/06/07/ssh-key-rotation-with-ansi ble/](https://derpops.bike/2014/06/07/ssh-key-rotation-with-ansible/)
[http://vicendominguez . blogspot . kr/2014/05/ansi ble-generating-pub-key-file-and . html](http://vicendominguez.blogspot.kr/2014/05/ansible-generating-pub-key-file-and.html)

ansi ble docs:
[http://docs.ansible.com/ansible/latest/playbooks_intro.html](http://docs.ansible.com/ansible/latest/playbooks_intro.html)
[http://docs . ansi ble . com/ansi ble/latest/authorized _ key _ module . html](http://docs.ansible.com/ansible/latest/authorized_key_module.html)

手动 ssh-copy-id
https://www.ssh.com/ssh/copy-id