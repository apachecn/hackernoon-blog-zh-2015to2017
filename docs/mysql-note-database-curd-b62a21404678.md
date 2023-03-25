# [MySQL]注意:数据库凝乳

> 原文：<https://medium.com/hackernoon/mysql-note-database-curd-b62a21404678>

![](img/b5802649a8d3aaa055830b54af28e7ed.png)

> MySQL 数据库，它由表组成，表由列(或字段)和行(记录)组成。它们是一张蜘蛛网，复杂但组织良好。

## 这个注释包括 4 个最常用的命令，这是我在操作数据库时所需要的:

1.  创建和删除
2.  显示所有数据库
3.  设置默认数据库
4.  显示当前数据库

# 1-创建和删除

```
mysql> **CREATE DATABASE southwind;**
Query OK, 1 row affected (0.03 sec)

mysql> **DROP DATABASE southwind;**
Query OK, 0 rows affected (0.11 sec)

mysql> **CREATE DATABASE IF NOT EXISTS southwind;**
Query OK, 1 row affected (0.01 sec)

mysql> **DROP DATABASE IF EXISTS southwind;**
Query OK, 0 rows affected (0.00 sec)
```

# 2-显示所有数据库

```
mysql> **SHOW DATABASES;**
```

# 3-设置默认数据库

```
mysql> **USE southwind;**
```

# 4-显示当前数据库

```
mysql> SELECT DATABASE();
```

# 你可能也喜欢

*   [MySQL 手册](/curiousian/mysql-handbook-tutorial-example-for-beginner-setup-admin-query-curd-relation-583b05106c1f#.v7tni8uoj)
*   [【MySQL】注意:Node.js 中的快速设置和运行](https://hackernoon.com/nodejs-mysql-install-setup-tutorial-example-connection-insert-query-fd5c5032d3b0#.hpp65qg28)
*   [【MySQL】注意:创建管理员用户](https://hackernoon.com/mysql-note-create-admin-user-5e77b43ecc8e#.uncmszepg)
*   [【MySQL】注:数据库凝乳](https://hackernoon.com/mysql-note-database-curd-b62a21404678#.jm4eqcmgu)
*   [【MySQL】注:排凝乳](/hacker-daily/mysql-tutorial-query-limit-offset-condition-row-foreign-key-join-select-d3835b28e1a#.dob477vn4)
*   [【MySQL】注:关系:1–1，1-n，n-n，嵌套](https://hackernoon.com/mysql-tutorial-example-relation-foreign-key-database-funtion-join-table-query-one-namy-nest-41dd09648fbd)

## 参考:

[https://www.cyberciti.biz/faq/mysql-list-databases/](https://www.cyberciti.biz/faq/mysql-list-databases/)

[https://www . NTU . edu . SG/home/ehchua/programming/SQL/MySQL _ beginner . html](https://www.ntu.edu.sg/home/ehchua/programming/sql/MySQL_Beginner.html)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)