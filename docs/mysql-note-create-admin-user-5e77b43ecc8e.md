# [MySQL]注意:创建管理员用户

> 原文：<https://medium.com/hackernoon/mysql-note-create-admin-user-5e77b43ecc8e>

![](img/366bbc89752a5377402a05c1d78be71d.png)

> 我曾经沉迷于 MongoDB，现在我正从它退出到 MySQL，因为我们希望有多少应用程序以这种方式纠缠在一起是一个商业决定，但双方都有权衡。MongoDB 很容易构建分片、复制和扩展，但在其上设计复杂的电子商务平台就是另一回事了。

首先，任何 MySQL 操作都需要一个已经存在并经过验证的用户帐户。本说明是关于一个 ***快速*** 配置创建 ***可用*** 用户用于 hello-world 目的。

## 注意

—“百分号”、“%”表示所有 ip，因此 localhost 是多余的
—当创建一个新用户时，该用户对数据库没有任何权限。事实上，如果 newuser 甚至尝试登录(使用密码，password)，他们将无法到达 MySQL shell。 ***先授予再使用*** 。

# 从 mysql 客户端登录 MySQL

```
$ mysql -u root -p
```

# 使用密码创建用户

```
mysql> CREATE USER ‘peter’@’%’ IDENTIFIED BY ‘1234’;
```

# 删除用户

```
mysql> DROP USER ‘peter’@‘%’;
```

# 授予特权

该命令允许用户读取、编辑、执行和执行所有数据库和表中的所有任务。

撤销**【数据库名称】**上的**【权限类型】**。**【表名】** FROM ' **【用户名】**' @ ' % '；

```
mysql> GRANT ALL PRIVILEGES ON *.* TO ‘peter’@’%’;
```

# 撤销特权

```
mysql> REVOKE ALL PRIVILEGES ON *.* FROM ‘peter’@‘%’;
```

# 重新加载所有权限

```
mysql> FLUSH PRIVILEGES;
```

# 你可能也喜欢

*   [MySQL 手册](/curiousian/mysql-handbook-tutorial-example-for-beginner-setup-admin-query-curd-relation-583b05106c1f#.v7tni8uoj)
*   [【MySQL】注意:在 Node.js 中快速设置和运行](https://hackernoon.com/nodejs-mysql-install-setup-tutorial-example-connection-insert-query-fd5c5032d3b0#.hpp65qg28)
*   [【MySQL】注意:创建管理员用户](https://hackernoon.com/mysql-note-create-admin-user-5e77b43ecc8e#.uncmszepg)
*   [【MySQL】注:数据库凝乳](https://hackernoon.com/mysql-note-database-curd-b62a21404678#.jm4eqcmgu)
*   [【MySQL】注:排凝乳](/hacker-daily/mysql-tutorial-query-limit-offset-condition-row-foreign-key-join-select-d3835b28e1a#.dob477vn4)
*   [【MySQL】注:关系:1–1，1-n，n-n，嵌套](https://hackernoon.com/mysql-tutorial-example-relation-foreign-key-database-funtion-join-table-query-one-namy-nest-41dd09648fbd)

## **参考:**

[https://www . phpini . com/MySQL/MySQL-add-new-users-databases-privileges](https://www.phpini.com/mysql/mysql-add-new-users-databases-privileges)

[http://shaocian.blogspot.tw/2012/11/mysql.html](http://shaocian.blogspot.tw/2012/11/mysql.html)

[https://www . digital ocean . com/community/tutorials/how-to-create-a-new-user-and-grant-permissions-in-MySQL](https://www.digitalocean.com/community/tutorials/how-to-create-a-new-user-and-grant-permissions-in-mysql)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)