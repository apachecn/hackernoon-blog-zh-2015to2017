# 你不需要 ORM

> 原文：<https://medium.com/hackernoon/you-dont-need-an-orm-7ef83bd1b37d>

关于 ORM 为什么是反模式已经说了很多。核心原因在这里描述[。要点是数据存储和检索的方式是任何对象的固有属性。但 ORM 似乎是“一等公民”:我们在需要取一些数据的时候显式调用它的方法，ORM 把它框成一个对象；当我们需要持久化一个对象时，我们调用 ORM 的功能。在这两种情况下，ORM 对我们的对象进行操作，将它们视为数据包。真的很冒犯。所以我宁愿反过来:我希望我的对象调用 ORM，而不是相反。](http://www.yegor256.com/2014/12/01/orm-offensive-anti-pattern.html)

## 那么，什么是对象呢？

毕竟，什么是对象？它代表了现实生活中的一些事物，来自我们无处不在的语言。它的数据存储方式只是一个实现细节。我希望它被封装在我的对象边界内。谁说所有对象的数据都必须存储为对象属性？谁说那个对象包含任何数据？是即时计算的吗？它存储在内存中吗？它存储在磁盘上吗？是否存储在云端？没关系。对象不是数据包。就像[我的微服务](/@wrong.about/what-characteristics-my-services-should-possess-ca22294bbea6)一样，它暴露的是行为而不是数据。这个原理被称为[统一访问原理](https://martinfowler.com/bliki/UniformAccessPrinciple.html)，它是封装——基本 OOP 概念的一种体现。

## 持久对象状态

所以我的持久化对象封装了两件事:第一件由对象用来访问存储，第二件由对象用来查找或保存数据。这里举的例子[是](https://github.com/aintshy/hub/blob/master/src/main/java/com/aintshy/pgsql/PgHuman.java):

```
**final class** PgHuman **implements** Human
{
    */**
     * Data source.
     */* **private final** transient PgSource src;

    */**
     * Number of it.
     */* **private final** transient long number;

    PgHuman(**final** PgSource source, **final** long num)
    {
        this.src = source;
        this.number = num;
    }
}
```

但是在实施这个概念的过程中，有一件事让我担心。再次考虑 [PgHuman](https://github.com/aintshy/hub/blob/master/src/main/java/com/aintshy/pgsql/PgHuman.java) 类。里面肯定既有数据库查询逻辑，也有业务逻辑。看下一个()方法。它检索带有未读消息的对话，如果没有，则开始与没有任何消息的随机问题相关的新的对话，并且该对话不是由当前用户询问的。我觉得把这些责任分开会很棒。我完全理解数据库切换很少发生，但尽管如此，它对本地集成测试还是很有帮助的，因为数据只存储在内存中。

## 更好的实施

那么，将数据库逻辑分解出来并隐藏在接口后面的明显步骤是什么呢？考虑一个基于[这篇文章](http://gorodinski.com/blog/2012/04/14/services-in-domain-driven-design-ddd/)的例子。
下面是一个实现:

```
**class** PurchaseOrder **implements** IPurchaseOrder
{
    */**
     ** ***@var*** *OrderStorage
     */* **private $storage**;

    */**
     ** ***@var*** *OrderId
     */* **private $id**;

    **public function** __construct(OrderStorage $storage, OrderId $id)
    {
        $this->**storage** = $storage;
        $this->**id** = $id;
    }

    **public function** newInvoice(
        IInvoiceNumber $invoiceNumber,
        IVendorInvoiceNumber $vendorInvoiceNumber,
        DateTime $dateTime,
        InvoiceAmount $amount
    )
    {
        $this->**storage**->save($invoiceNumber, $vendorInvoiceNumber, $dateTime, $amount);

        **return new** Invoice($this->**storage**, $invoiceNumber);
    }
}
```

因此，所有数据库逻辑都放在存储的“save()”方法中。可能是事务处理、相关表中的一些插入、显式锁等。好的一面是域类 PurchaseOrder 根本没有耦合到特定于数据库的代码。
如果我们使用任何带有虚拟数据库抽象库的关系数据库，我们的存储代码可能如下所示:

```
**class** SQLOrderStorage **implements** OrderStorage
{
    **private $connection**;

    **public function** __construct(SQLConnection $connection)
    {
        $this->**connection** = $connection;
    }

    **public function** save(
        IInvoiceNumber $invoiceNumber,
        IVendorInvoiceNumber $vendorInvoiceNumber,
        DateTime $dateTime,
        InvoiceAmount $amount
    )
    {
        **new** InsertQuery($this->**connection**)
            ->into(**'invoice'**)
            ->set(**'invoice_number'**, $invoiceNumber->value())
            ->set(**'vendor_invoice_number'**, $vendorInvoiceNumber)
            ->set(**'datetime'**, $dateTime)
            ->set(**'amount'**, $amount->amount())
            ->set(**'currency'**, $amount->currency())
        ;
    }
}
```

需要 Memcached 缓存吗？没问题，让我们用一个装饰者:

```
**class** MemcachedOrderStorage **implements** OrderStorage
{
    **private $storage**;
    **private $memcachedClient**;

    **public function** __construct(OrderStorage $storage, MemcachedClient $memcachedClient)
    {
        $this->**storage** = $storage;
        $this->**memcachedClient** = $memcachedClient;
    }

    **public function** save(
        IInvoiceNumber $invoiceNumber,
        IVendorInvoiceNumber $vendorInvoiceNumber,
        DateTime $dateTime,
        InvoiceAmount $amount
    )
    {
        $this->**storage**->save($invoiceNumber, $vendorInvoiceNumber, $dateTime, $amount);

        $this->**memcachedClient**->set(
            $invoiceNumber->value(),
            *base64_encode*(
                json_encode(
                    [
                        **'invoice_number'** => $invoiceNumber->value(),
                        **'vendor_invoice_number'** => $vendorInvoiceNumber->value(),
                        **'datetime'** => $dateTime->format(**'c'**),
                        **'amount'** => $amount->amount(),
                        **'currency'** => $amount->currency(),
                    ]
                )
            )
        );
        ;
    }
}
```

需要内存缓存？让我们再次使用一个装饰器:

```
**class** LocalOrderStorage **implements** OrderStorage
{
    **private $storage**;
    **private $localStorage**;

    **public function** __construct(OrderStorage $storage)
    {
        $this->**storage** = $storage;
        $this->**localStorage** = [];
    }

    **public function** save(
        IInvoiceNumber $invoiceNumber,
        IVendorInvoiceNumber $vendorInvoiceNumber,
        DateTime $dateTime,
        InvoiceAmount $amount
    )
    {
        $this->**storage**->save($invoiceNumber, $vendorInvoiceNumber, $dateTime, $amount);

        $this->**localStorage**[$invoiceNumber->value()] =
            [
                **'invoice_number'** => $invoiceNumber->value(),
                **'vendor_invoice_number'** => $vendorInvoiceNumber->value(),
                **'datetime'** => $dateTime->format(**'c'**),
                **'amount'** => $amount->amount(),
                **'currency'** => $amount->currency(),
            ]
        ;
    }
}
```

## 事务管理

您也不应该担心事务管理。如果你对感兴趣，你会意识到[聚合根](https://martinfowler.com/bliki/DDD_Aggregate.html)的概念。简而言之，它是您的领域模型中一致性边界的量子。在任何时候都必须保持不变的东西。您可能知道在改变聚合时的经验法则:每个事务一个聚合，下一个聚合通常由当前事件发布的事件触发。[这里是这种方法的一个例子](/@wrong.about/example-of-service-boundaries-identification-e9077c513560)。所以没有跨越几个聚合根的事务。在我的示例中，“PurchaseOrder”类是一个聚合根，所有事务逻辑都可以驻留在“SQLOrderStorage”中。

## 多语种物体

这个概念不仅包括使用 SQL 语言的对象、使用 NoSQL 语言的对象或任何其他使用特定存储语言的对象，还包括其数据不仅保存在内存中的任何对象。这包括一些远程 json/xml/etc 资源，或者一个本地文件。例如，如果我可以通过调用某个 API 找到我的财务事务，事务类可能如下所示:

```
**class** FinancialTransaction
{
    **private $dataSource**;
    **private $id**;

    **public function** __construct(TransactionDataSource $dataSource, TransactionId $id)
    {
        $this->**dataSource** = $dataSource;
        $this->**id** = $id;
    }

    **public function** isSuccessful()
    {
        **return** $this->**dataSource**->isSuccessful();
    }
}
```

和数据源实现可以如下所示:

```
**interface** TransactionDataSource
{
    **public function** isSuccessful(TransactionId $transactionId);
}

**class** HttpTransactionDataSource **implements** TransactionDataSource
{
    **private $connection**;

    **public function** __construct(Connection $connection)
    {
        $this->**connection** = $connection;
    }

    **public function** isSuccessful(TransactionId $transactionId)
    {
        (**new** Response(
            **new** PostHttpRequest(
                $this->**connection**->uri(),
                **new** HttpVersion(**'HTTP/1.1'**),
                [
                    $this->**connection**->credentials(),
                    **new** Header(**'Content-Type: application/xml'**),
                    **new** Header(**'SOAPAction: ""'**),
                ],
                **new** RequestBody(
                    (**new** SimpleXMLElement(**"<data></data>"**))
                        ->addChild(**'transaction_id'**, $transactionId->value())
                ),
                $this->**connection**->transport()
            )
        ))
            ->value()
        ;
    }
}
```

## 包装它

嗯，这种方式无非就是倒过来的 ORM/HTTP call/等。但是概念上的差异是巨大的。
主要的一点是，这种方法中的动力来自于“对象”的概念。它是一个声明性的概念，代表现实生活中的实体。知道做什么和如何做的对象。不需要任何指令的对象。不需要以任何方式编排或控制的对象。最后，拥有所有资源或[服务](/@wrong.about/you-dont-need-a-domain-service-class-in-ddd-9ecd3140782)的对象需要完成它的工作。

用人类的比喻来说，对象是成年人，而不是孩子。