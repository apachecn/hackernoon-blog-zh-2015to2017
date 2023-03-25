# Python 鸭类型(或自动接口)

> 原文：<https://medium.com/hackernoon/python-duck-typing-or-automatic-interfaces-73988ec9037f>

![](img/c5acdb83b1d7e7141341feecc6a181dd.png)

现在我全职使用 python，更多的时候，我对它的做事方式有了一些见解(或者对一些人来说是 python 的 T2 禅)。在我的日常生活中，有一件事让我印象深刻，那就是鸭子打字。老实说，这不是 python 独有的特性，因为几乎每种动态语言都有这种行为。不过说好听点，我喜欢 Python。

如果有些读者不知道它是什么，鸭子类型是类型系统的一个特性，其中类的语义是由它对一些消息(方法或属性)的响应能力决定的。典型的例子(以及名字背后的原因)是[鸭子测试](https://en.wikipedia.org/wiki/Duck_test) : *如果它看起来像鸭子，游泳像鸭子，嘎嘎叫也像鸭子，那么它很可能就是一只鸭子。*

```
class Duck:
   def quack():
      print('Quack!')class Goose:
   def quack():
      print('Quack')Goose().quack()
>> Quack!Duck().quack()
>> Quack!
```

好吧，但是有什么问题呢？这里没有什么新东西。是的，没什么新的。我想展示的是这种行为在系统架构中的含义。我主张这是一个好的暗示。让我解释一下。

想象一个名为 *Car* 的类:

```
class Car:
   def __init__(self, engine):
      self.engine = engine def run():
       self.engine.turn_on()
```

这是依赖注入的一个经典例子。我的类 *Car* 接收一个引擎实例，并在 run 方法中使用它，在那里它调用 *turn_on* 方法。注意，我的*汽车*不依赖于引擎的任何具体实现。而且我没有导入任何其他类型！仅仅使用依赖注入的实例来响应 *turn_on* 消息。我可以说我的类 *Car* 依赖于一个接口。但是我不需要申报。是自动界面！

在没有 duck typing 的语言中，我可能必须声明一个显式接口，例如命名为 *IEngine* ，拥有实现(例如 *EngineV1* )并显式定义我的 *Car* 参数为 *IEngine* 的实现。

```
interface IEngine {
    void turnOn();
}public class EngineV1 implements IEngine {
    public void turnOn() {
        // do something here
    }
}public class Car {
    public Car(IEngine engine) {
       this.engine = engine;
    } public void run() {
        this.engine.turnOn();
    }}
```

啊！多少代码。

所以，你可以看到效果是一样的。我的类 *Car* 在这两种情况下都依赖于一个接口。但是在第一种情况下，代码更少，我不需要显式实现接口。如果我定义了方法 *turn_on* ，它就已经实现了。

## 弱点

这里我可以看到两个问题。

1.  脂肪界面

首先是对[接口膨胀](https://en.m.wikipedia.org/wiki/Interface_bloat)的刺激。由于我们没有明确定义 API，这可能导致接口中有太多的粒度方法。

2.未命名的依赖项

因为我们不知道一个类所依赖的接口的名字，所以我们没有自动的依赖树。因此，所有依赖注入框架都必须以某种方式解决依赖解析。

例如，[注入器库](http://injector.readthedocs.io/en/latest/index.html)必须实现[键](http://injector.readthedocs.io/en/latest/terminology.html#keys)的概念，以允许自动解析依赖树。在 injector 术语中，键只不过是命名/标识类实现的接口的一种方式。

## 结论

我不认为鸭子打字是故意设计成有这种行为的。我甚至不认为 duck typing 是在一种语言中有意设计的，而是这些类型系统的动态性质的副作用。但是有趣的是它能给系统[设计](https://hackernoon.com/tagged/design)和架构带来简洁和低耦合。