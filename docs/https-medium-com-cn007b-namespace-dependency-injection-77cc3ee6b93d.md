# 命名空间依赖注入

> 原文：<https://medium.com/hackernoon/https-medium-com-cn007b-namespace-dependency-injection-77cc3ee6b93d>

![](img/a2f640ee94f0ec6798462ee38eecb768.png)

我确信您已经熟悉了依赖注入(DI)设计模式和依赖注入的类型:构造函数、设置器、接口。同样在一些源代码中，你可以找到参数依赖注入类型。
在本主题中，您将发现另一种类型的 DI——名称空间！

## 先决条件

我希望你已经熟悉 DI 背后的想法，并且你清楚地了解你为什么需要它。如果不是，请先阅读关于 DI 的基本信息。

让我们假设我们的应用程序是乌克兰🇺🇦签证中心，它可以提供，批准或拒绝世界上任何国家的签证，为简单起见，这里只考虑欧盟。首先，我们需要接口，
文件`src/Domain/Interfaces/VISA/EuropeanUnion.php`:

现在我们对非免签案件进行执行(姑且称此案为“审查”)，文件`src/Infrastructure/VISA/Review/EuropeanUnion.php`:

而另一个实施为免签证的案例，
文件`src/Infrastructure/VISA/Free/EuropeanUnion.php`:

## **目标**

主要目的是在某个地方(例如在文件`index.php` )
有这样超级简单的代码:

## 解决办法

使用名称空间依赖注入并配置您的`composer.json`文件:

并且运行:`php composer dump-autoload`(很重要，请不要忘记运行这个命令)。

## 试验

现在只需运行命令:`php index.php`，您将看到:

```
'You have to provide documents for review.'
```

现在请用下一个内容更新`composer.json`:

和 run: `php composer dump-autoload`和 run: `php index.php`你会看到:

```
'Your request is approved!'
```

就是这样！恭喜你。

## 与其他方法的比较

备选方案:工厂方法(甚至抽象工厂)、构造器或设置器依赖注入或策略设计模式。是的，这些模式可以被认为是一种替代方案，但是它们都需要您编写技术代码，例如:

```
class Factory
{
    public static function create($name)
    {
        if (class_exists($name)) {
            return new $name();
        }
    }
}
```

或者

```
public static function getInstance(string $name)
{
    if (!isset(self::$instances[$name])) {
        $className = "App\\Infrastructure\\" . ucfirst($name);
        if (!class_exists($className)) {
            throw new RuntimeException("Unknown class: $className");
        }
        self::$instances[$name] = new $className();
    }
    return self::$instances[$name];
}
```

同样在现实世界的项目中，它看起来会更复杂，也许像这样的。此外，您还将把声明式代码与如下内容混合在一起:

```
$name = ($parameter === 'free') ? 'VISA/Free' : 'VISA/Review';
$visa = Factory::create($name);
// and only now
$visa->approve();
```

同样，在使用这些模式的情况下，你必须将所有代码放在一个地方(一个回购)，但是想象一下`VISA/Review`和`VISA/Free`可能是拥有自己的工厂、策略、微服务、内部业务逻辑实现等的巨大子系统——这可能是一场噩梦。

通过名称空间依赖注入，你可以为接口、`VISA/Review`、`VISA/Free`等创建专用包(repos)。每个包只需要知道接口，不需要更多。所以它更灵活，更容易维护，扩展和重用！如果你有一个非常大的项目，你甚至可以为每个包建立独立的开发团队。

最重要的是——在应用程序层面上，你将拥有超级简单的声明式代码，所有的内部细节都不会让你困惑。您的项目将更加可预测，因为运行时的行为不会根据不同的环境而变化，它将严格按照您的配置工作。另外，IDE(像 PHPStorm 一样)会给你更多的帮助，因为它不会像`new $className();`那样对代码大喊大叫。

## 性能比较

名称空间 DI 比所有其他类型的依赖注入都要快！！！因为这种方法没有前面提到的`yaml`配置或基础设施，也没有`DI Container`或其他什么…，因此这样 PHP 就不会浪费时间在每个 http 请求中执行多余的代码！

## 为什么这种方法不受欢迎

1.  因为由于自动加载的工作方式，这种类型的依赖注入只可能出现在 PHP 中。用 JavaScript 不可能做同样的事情，用其他语言也没用。因此，这种技术没有广泛应用。
2.  对于其他类型的依赖注入，可以在运行时改变应用程序的行为，但是对于名称空间 DI，这是不可能的。

## 结论

如果您不需要在运行时改变应用程序的行为(或内部实现),但需要有机会从配置文件中改变它(甚至是`composer.json`),并减少执行时间——您可以考虑一个选项名称空间依赖注入！