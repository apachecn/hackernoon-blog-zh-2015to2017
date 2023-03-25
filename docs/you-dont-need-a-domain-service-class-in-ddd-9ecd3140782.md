# 在 DDD 你不需要域服务类

> 原文：<https://medium.com/hackernoon/you-dont-need-a-domain-service-class-in-ddd-9ecd3140782>

## 你也不需要一个应用服务类！

让我们检查一下这个概念的作者所下的定义:

> 当领域中的重要过程或转换不是实体或值对象的自然职责时，将操作作为独立接口添加到模型中，并声明为服务。根据模型的语言定义接口，并确保操作名称是通用语言的一部分。使服务无状态。
> 
> [**埃里克·埃文斯，**领域驱动设计](https://www.amazon.com/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215)

## 服务类别是程序性的

用简单的英语来说，这意味着它只不过是一个程序。为什么？因为你接受一个对象，操作它的数据并把它传递给另一个对象。这与面向对象的基本原则相矛盾，面向对象的基本原则是数据和行为共存。所以在我的客体世界里，一切都是某个客体的责任。涉及域服务的程序代码的典型例子是[密码散列](http://Consider an example of password hashing given here.)。

## 请使用装饰者

每当你需要一些新的行为时，你应该考虑使用一个[装饰模式](https://en.wikipedia.org/wiki/Decorator_pattern)。所以上面的例子可以这样改写:

```
**try** {
    **echo** (**new** HashedPassword(
        **new** RegexMatchedPassword(
            **new** NonEmptyPassword(
                **new** Password(**'asdSd12@A1'**)
            ),
            **new** Regex(**'/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[$@$!%*?&])[A-Za-z\d$@$!%*?&]{8,10}$/'**)
        )
    ))
        ->string()
    ;
} **catch** (Exception $exception) {
    **echo** $exception->getMessage();
}
```

所有人注意' HashedPassword '类。所有散列功能都驻留在那里。我喜欢这个名字——哈希密码。它非常具有声明性，不像“哈希密码”，它更像是“如何”，而不是“什么”，因此本质上是必要的。

除了将域服务的功能转移到 [DDD 的](https://en.wikipedia.org/wiki/Domain-driven_design) [值对象](http://deviq.com/value-object/)之外，我还使用了[验证装饰器](http://www.yegor256.com/2016/01/26/defensive-programming.html)，这是叶戈尔·布加延科引入的概念。

下面是剩余的代码:

```
**interface** IPassword
{
    **public function** string();
}

**class** Password **implements** IPassword
{
    **private $password**;

    **public function** __construct($password)
    {
        $this->**password** = $password;
    }

    **public function** string()
    {
        **return** $this->**password**;
    }
}

**class** NonEmptyPassword **implements** IPassword
{
    **private $password**;

    **public function** __construct(IPassword $password)
    {
        $this->**password** = $password;
    }

    **public function** string()
    {
        **if** (*is_null*($this->**password**->string())) {
            **throw new** Exception(**'Password can not be empty'**);
        }

        **return** $this->**password**->string();
    }
}

**class** RegexMatchedPassword **implements** IPassword
{
    **private $password**;
    **private $regex**;

    **public function** __construct(IPassword $password, IRegex $regex)
    {
        $this->**password** = $password;
        $this->**regex** = $regex;
    }

    **public function** string()
    {
        *// https://stackoverflow.com/a/21456918/618020* **if** (!*preg_match*($this->**regex**->value(), $this->**password**->string())) {
            **throw new** Exception(
                **'Password must have minimum eight and maximum 10 characters, at least one uppercase letter,
                one lowercase letter, one number and one special character.'** );
        }

        **return** $this->**password**->string();
    }
}

**interface** IRegex
{
    **public function** value();
}

**class** Regex **implements** IRegex
{
    **private $regex**;

    **public function** __construct($regex)
    {
        $this->**regex** = $regex;
    }

    **public function** value()
    {
        **if** (*preg_match*($this->**regex**, **null**) === **false**) {
            **throw new** Exception(**'Your regex is broken.'**);
        }

        **return** $this->**regex**;
    }
}

**class** HashedPassword **implements** IPassword
{
    **private $password**;

    **public function** __construct(IPassword $password)
    {
        $this->**password** = $password;
    }

    **public function** string()
    {
        **return** *sha1*($this->**password**->string());
    }
}
```

因此，如果对象设计得当，就不会有服务。只有正常的物体，代表真实的东西。所以根据这个启发，我认为应用服务也是一个错误。

敬请关注。