# 你不需要依赖注入容器

> 原文：<https://medium.com/hackernoon/you-dont-need-a-dependency-injection-container-10a5d4a5f878>

当错误地使用 DI 容器时，也就是说，在大多数情况下，不仅仅是代码污染者，而是绝对的恶魔。

## 这是一种静态依赖

DI 容器经常被从各个地方调用。在这种情况下，DI 容器只是一个[服务定位器](http://blog.ploeh.dk/2010/02/03/ServiceLocatorisanAnti-Pattern/)，它是一个单体。拥有大量方法的 Singleton。除此之外，它还引入了[隐藏依赖](/@wrong.about/static-classes-are-evil-or-make-your-dependencies-explicit-af3e73bd29dd)。看起来可能是这样的:

```
**class** PaymentProcessor
{
    **public function** purchase()
    {
        **return** DIPaymentModuleFactory::*me*()->purchase();
    }
}
```

要纠正这个错误，唯一的方法是只在应用程序的最高级别使用它，最好是在应用程序启动的索引文件中。如果你这样使用它，你真的还需要它吗？反正你不知道。已经 [Catch-22](https://en.wikipedia.org/wiki/Catch-22) 了，前面还有两分。

## 它打破了最坚实的 OOP 隐喻之一

DI container 打破了大卫·韦斯特在他的书[对象思维](https://www.amazon.com/Object-Thinking-Developer-Reference-David/dp/0735619654)中谈到的[乐高积木](/@wrong.about/on-good-domain-decomposition-385ee8ce5a3)的比喻。我希望我的类在一个地方被实例化，这个地方应该是任何请求或消息的入口点。他们的合作只能通过组合来实现。可组合性是任何有抱负的对象思考者努力追求的目标。

## 它破坏了凝聚力

此外，它打破了基本的 OOP 原则——数据封装和公开行为。为什么一个对象应该被实例化的方式对程序代码是隐藏的？为什么放在某个[配置文件](https://symfony.com/doc/current/components/dependency_injection.html)里？真的，想想看——简直是胡说八道！这一点更宽泛。为什么我们需要一个配置文件？为什么有些数据会被从其行为中分离出来？这正是马丁·福勒 14 年前所说的，对吗？每个人似乎都同意这一点。但是为什么我们一直遵循这个概念呢？配置文件只是程序思维的反映！

## 没有容器的生活

是一个[纯依赖注入](http://blog.ploeh.dk/2014/06/10/pure-di/)。这就是我的入口点通常的样子:

```
**try** {
    **echo** (**new** PurchaseOrder(
            **new** LocalOrderStorage(
                **new** NullOrderStorage()
            ),
            **new** OrderId($inputParams[**'order_id'**])
        ))
            ->newInvoice(
                **new** InvoiceNumber(
                    **new** Vendor(
                        **new** LocalVendorStorage(),
                        **new** VendorId($inputParams[**'vendor_id'**])
                    ),
                    **new** VendorInvoiceNumber($inputParams[**'vendor_invoice_number'**]),
                    **new** DateTime($inputParams[**'date_time'**])
                ),
                **new** VendorInvoiceNumber($inputParams[**'vendor_invoice_number'**]),
                **new** DateTime($inputParams[**'date_time'**]),
                **new** InvoiceAmount(
                    **new** Amount($inputParams[**'amount'**]),
                    **new** Currency($inputParams[**'currency'**])
                )
            )
                ->json()
    ;
} **catch** (Exxeption $exxeption) {
    **return** (**new** ErrorResult())
            ->json($exxeption->getCode(), $exxeption->getMessage())
        ;
}
```

太棒了。加入我吧。