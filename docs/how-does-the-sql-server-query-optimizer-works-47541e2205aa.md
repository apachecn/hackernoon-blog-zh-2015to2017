# SQL Server 查询优化器是如何工作的

> 原文：<https://medium.com/hackernoon/how-does-the-sql-server-query-optimizer-works-47541e2205aa>

SQL Server [数据库](https://hackernoon.com/tagged/database)引擎的核心是两个主要组件:**存储引擎**和**查询处理器**，也称为关系引擎。

*   存储引擎:以优化并发性的方式在磁盘和内存之间读取数据，同时保持数据的完整性
*   查询处理器:1。由**查询优化器** 2 负责设计查询计划(实现什么算法/操作符)。由**执行引擎**根据该计划执行查询

查询处理器执行以下操作:

1.  查询解析
2.  将查询绑定到对象
3.  生成可能的执行计划
4.  每个计划的成本评估

执行引擎做以下事情:

1.  查询执行
2.  计划缓存

解析将 SQL 查询翻译成初始的树表示。绑定主要与名称解析有关。

# 搜索空间

我们将给定查询的搜索空间定义为该查询的所有可能的执行计划的集合，在该搜索空间中的任何可能的计划都返回相同的结果。

# 生成候选人执行计划

如前所述，查询优化器的基本目的是为您的查询找到一个有效的执行计划。即使对于相对简单的查询，也可能有大量不同的方法来访问数据以产生相同的最终结果。因此，查询优化器必须从可能非常多的候选执行计划中选择最佳计划，做出明智的选择非常重要，因为根据选择的计划，向用户返回结果所需的时间可能会有很大差异。

查询优化器必须在优化时间和计划质量之间取得平衡。SQL Server 不会进行彻底的搜索，而是尝试尽快找到一个合适的高效计划。

# 评估每个计划的成本

查询优化器需要估计这些计划的成本，并选择最便宜的一个。为了估计计划的成本，它使用考虑了 I/O、CPU 和内存等资源使用的成本计算公式来估计该计划中每个物理操作符的成本。

**基数估计**:查询计划的成本估计主要取决于物理操作符使用的算法，以及需要处理的记录的估计数量；这种对记录数量的估计称为基数估计。

# 查询执行和计划缓存

优化查询后，执行引擎将使用生成的计划来检索所需的数据。生成的执行计划可以存储在内存中的计划缓存(在 SQL Server 的早期版本中称为过程缓存)中，以便在再次执行同一查询时可以重用。

然而，对于给定的查询，重用现有的计划并不总是最佳的解决方案。根据表中数据的分布情况，给定查询的最佳执行计划可能会因所述查询中提供的参数而有很大差异，而被称为[参数嗅探](https://www.brentozar.com/archive/2013/06/the-elephant-and-the-mouse-or-parameter-sniffing-in-sql-server/)的行为可能会导致选择次优计划。

即使执行计划在计划缓存中可用，一些元数据更改(如删除索引或约束，或者对数据库内容进行足够大的更改)也可能会使现有计划无效或次优，从而导致该计划从计划缓存中被丢弃，并生成新的优化。

# 重新编译

您可以强制 SQL Server 在每次运行存储过程时重新编译它。这样做的好处是，每次运行时都会创建最佳的查询计划。但是，重新编译是一项 CPU 密集型操作。对于频繁运行的存储过程，或者在已经受到 CPU 资源限制的服务器上，这可能不是理想的解决方案。另一件要记住的事情是，计划不会存储在缓存中，这使得如果它们有问题的话更难被发现。

```
**ALTER** **PROCEDURE** Get_OrderID_OrderQty
@ProductID INT
**WITH** RECOMPILE
**AS**
**SELECT** SalesOrderDetailID, OrderQty
**FROM** Sales.SalesOrderDetail
**WHERE** ProductID = @ProductID;
```

# 暗示

另一种选择是使用 OPTIMIZE FOR 查询提示。这告诉 SQL Server 在编译计划时使用指定的值。如果，通过测试，你能找到一个每次都能产生一个“足够好”的计划的值，并且性能对老鼠和大象来说都是可以接受的，这对你来说是一个好的选择。

但是，要明白您是在指挥查询优化器。你告诉它你认为最好的。OPTIMIZE FOR 的最大缺点是在数据分布发生变化的表上。变化越快，这个提示就越过时。如果你提供的价值在一个月或一年内都不是最优的呢？你需要有一个方法来定期检查和修改它。

```
**ALTER** **PROCEDURE** Get_OrderID_OrderQty
@ProductID INT
**AS**
**SELECT** SalesOrderDetailID, OrderQty
**FROM** Sales.SalesOrderDetail
**WHERE** ProductID = @ProductID
**OPTION** (**OPTIMIZE** **FOR** (@ProductID=945));
```

> 现实是，即使经过 30 多年的研究，查询优化器仍然是高度复杂的软件，仍然面临一些技术挑战。因此，可能会出现这样的情况，即使您已经为查询优化器提供了它需要的所有信息，而且似乎没有任何明显的问题，您仍然没有得到一个有效的计划。

来源:

*   [大象和鼠标，或者，SQL Server 中的参数嗅探](https://www.brentozar.com/archive/2013/06/the-elephant-and-the-mouse-or-parameter-sniffing-in-sql-server/)
*   [SQL Server 查询优化器](https://www.simple-talk.com/sql/sql-training/the-sql-server-query-optimizer/)

*原载于*[*xameeramir . github . io*](http://xameeramir.github.io/How-SQL-Server-Query-Optimizer-Works/)*。*