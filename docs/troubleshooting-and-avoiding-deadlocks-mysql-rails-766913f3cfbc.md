# 故障排除和避免死锁— MySQL/Rails

> 原文：<https://medium.com/hackernoon/troubleshooting-and-avoiding-deadlocks-mysql-rails-766913f3cfbc>

最近我有一个有趣的问题要解决。刚从大学毕业，在现实世界中第一次看到学到的东西，有点令人兴奋。

问题是，在我们的电子商务 Rails 应用程序中，偶尔会有订单和一些 API 请求失败。从异常处理中我们发现这是由 [MySQL](https://hackernoon.com/tagged/mysql) 错误引起的。

> "试图获取锁时发现死锁；尝试重新启动交易"
> 
> “超过锁定等待超时；尝试重新启动交易"

这很糟糕，因为作为企业不可或缺的一部分，某些东西不应该失败。所以我的首要任务是找出原因。从日志中，我能够回溯到失败的代码行，并且还发现上面的两个错误都是针对同一行代码的。

## 那么到底什么是死锁呢？

假设有两个事务需要获得一些锁，以便提交事务。

> 事务 A:锁定行 X，并等待锁定行 y。
> 
> 事务 B:锁定行 Y，并等待锁定行 x。

但是这两个事务持有另一个事务需要起作用的行的锁，因此造成了两个事务都停止起作用的情况。

在这种情况下，MySQL 会使一个或两个事务超时，并释放锁。

但是 Rails 的问题是你有 ORM，事务封装的回调，我经常忘记它必须维护的 SQL，底层数据库协议和 ACID 属性。这都是 Rails 的魔力。

To the muggle world now!

现在可以推断出三件事:

1.  失败代码行所需的锁与其他事务所需的锁顺序相反，因此会导致死锁。
2.  有一些繁重的事务可能会锁定表而不是行，这样订单就会由于超时而失败。
3.  因为它是失败的同一行代码，所以当死锁没有发生时会发生超时错误。

所以下一步是找出另一个导致超时和死锁的锁定行的事务。

# 死锁故障排除:

在 MySQL 控制台中执行查询:

```
SHOW ENGINE INNODB STATUS;
```

这为您提供了 InnoDB 存储引擎的状态。它有不同的信息部分，您可以在其中找到有关最新检测到的死锁的信息。你可以在这里阅读更多关于这个[的内容。](https://dev.mysql.com/doc/refman/5.5/en/innodb-standard-monitor.html)

以下是死锁部分的示例:

```
------------------------
LATEST DETECTED DEADLOCK
------------------------
141016 15:39:58
*** (1) TRANSACTION:
TRANSACTION 3D009, ACTIVE 19 sec starting index read
mysql tables in use 1, locked 1
LOCK WAIT 2 lock struct(s), heap size 376, 1 row lock(s)
MySQL thread id 2, OS thread handle 0x7f0ee43cd700, query id 78 localhost root
updating
DELETE FROM t WHERE i = 1
*** (1) WAITING FOR THIS LOCK TO BE GRANTED:
RECORD LOCKS space id 0 page no 2428 n bits 72 index `GEN_CLUST_INDEX` of table
`mysql`.`t` trx id 3D009 lock_mode X waiting
Record lock, heap no 2 PHYSICAL RECORD: n_fields 4; compact format; info bits 0
 0: len 6; hex 000000000700; asc       ;;
 1: len 6; hex 00000003d007; asc       ;;
 2: len 7; hex 87000009560110; asc     V  ;;
 3: len 4; hex 80000001; asc     ;;*** (2) TRANSACTION:
TRANSACTION 3D008, ACTIVE 69 sec starting index read
mysql tables in use 1, locked 1
4 lock struct(s), heap size 1248, 3 row lock(s)
MySQL thread id 1, OS thread handle 0x7f0ee440e700, query id 79 localhost root
updating
DELETE FROM t WHERE i = 1
*** (2) HOLDS THE LOCK(S):
RECORD LOCKS space id 0 page no 2428 n bits 72 index `GEN_CLUST_INDEX` of table
`mysql`.`t` trx id 3D008 lock mode S
Record lock, heap no 1 PHYSICAL RECORD: n_fields 1; compact format; info bits 0
 0: len 8; hex 73757072656d756d; asc supremum;;Record lock, heap no 2 PHYSICAL RECORD: n_fields 4; compact format; info bits 0
 0: len 6; hex 000000000700; asc       ;;
 1: len 6; hex 00000003d007; asc       ;;
 2: len 7; hex 87000009560110; asc     V  ;;
 3: len 4; hex 80000001; asc     ;;*** (2) WAITING FOR THIS LOCK TO BE GRANTED:
RECORD LOCKS space id 0 page no 2428 n bits 72 index `GEN_CLUST_INDEX` of table
`mysql`.`t` trx id 3D008 lock_mode X waiting
Record lock, heap no 2 PHYSICAL RECORD: n_fields 4; compact format; info bits 0
 0: len 6; hex 000000000700; asc       ;;
 1: len 6; hex 00000003d007; asc       ;;
 2: len 7; hex 87000009560110; asc     V  ;;
 3: len 4; hex 80000001; asc     ;;*** WE ROLL BACK TRANSACTION (1)
```

我承认，看到这一大堆信息，我有些不知所措。但是，如果你眯着眼睛看，一切都变得有意义了。在这个部分中，您可以了解哪些事务导致了死锁，持有和等待的锁的类型。

你可以从这些信息中推断出:

1.  **涉及的交易**:从交易段可以看出`[*RECORD LOCKS*](https://dev.mysql.com/doc/refman/5.5/en/innodb-locking.html#innodb-record-locks) SPACE id 0`**的锁定索引，更重要的是`WAITING FOR THIS LOCK TO BE GRANTED`**`*HOLDS THE LOCKS(S)*`*的交易查询。*****
2.  ****所涉及的锁模式**:在*上方可以看到`lock mode S`和`lock mode X` 。*你有必要了解锁是如何工作的，这样你就知道为什么会发生死锁。**

*   **[排他锁(X)](https://dev.mysql.com/doc/refman/5.5/en/glossary.html#glos_exclusive_lock) :一种 ***锁*** ，防止任何其他 ***事务*** 锁定同一行。**
*   **[共享锁](https://dev.mysql.com/doc/refman/5.5/en/glossary.html#glos_shared_lock):一种 ***锁*** ，允许其他 ***事务*** 读取锁定的对象，也可以获取其上的其他共享锁，但不能写入。**

**瞧啊。现在您知道是哪些事务导致了问题。**

## **避免死锁:**

**web 应用程序是一个相当复杂的硬件、软件和网络系统。可以有多个用户同时请求 web 应用程序。您的应用程序必须以一种能够处理并发数据访问的方式进行编码。如果操作不当，可能会导致超时和死锁。**

**此外，App server 可以有多个进程，这样您的主机就可以同时为多个请求运行 Ruby 代码。因此，处理并发的两个主要工具是事务和锁。**

**请记住以下几点以避免死锁**

1.  **事务必须很短—尽可能在最短的时间内锁定更少的行。**

**2 .关心您使用的 ActiveRecord 回调。**

**对于长时间运行的代码，使用`after_commit`。例如:**

```
**class SailBoat
  has_many :passengers
  after_create :get_passenger_list
  def get_passenger_list
    # associating all passengers
  end
end**
```

**现在回调在很长一段时间内打开一个事务块并进行关联。这很糟糕，因为这会锁定行并导致死锁。**

**3.确保公共工作流总是以相同的顺序获得锁定。**

**4.使用队列来防止并发数据访问。**

**5.检查您的应用用例是否需要何种模式的[隔离级别](https://dev.mysql.com/doc/refman/5.7/en/innodb-transaction-isolation-levels.html)。默认情况下，INNODB 使用`REPEATABLE READ`。**

## **解决问题**

**持有锁的事务来自应用程序的外部服务。多亏了 Unicorn，这些请求同时发生，增加了超时/死锁的可能性。为什么？因为这些是繁重的事务性请求，有时会远远超出默认的 50 秒锁等待时间，或者会获取锁来制造死锁。**

**从`INNODB STATUS`中，我能够找出`HOLDS THE LOCK(S)`的查询，它来自 API 请求调用的存储过程。订单流和存储过程中的锁获取是相反的。把它们做成同样的顺序是很大的改变。但是在存储过程中引入了一个新的`TEMPORARY TABLE`,这样，通过一个索引临时表来连接它，而不是直接连接两个索引表，解决了问题的再次出现。**

**对于已经失败的死锁/超时 API 请求，我简单地实现了一个带有重试块的队列，并将它们推给它。**

**咻，我想我已经做完了！这是一条相当长的学习曲线，理解数据库如何在每个查询保持真实属性的情况下忙碌是非常酷的。如果有任何错误，请随时纠正我，或者在评论中分享你的想法。**

**祝你愉快。长码繁荣！**

**[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)****[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)****[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)**

> **[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。**
> 
> **如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！**

**![](img/be0ca55ba73a573dce11effb2ee80d56.png)**