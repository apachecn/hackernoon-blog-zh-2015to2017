# 改进您的 Python: Python 类和面向对象编程

> 原文：<https://medium.com/hackernoon/improve-your-python-python-classes-and-object-oriented-programming-d09ff461168d>

注:本周的每一天，我都会重新发布我最受欢迎的帖子之一。我希望第一次错过它们的人现在会发现它们很有用。这篇关于“类”和面向对象编程的帖子一直是我最受欢迎的帖子。光是这个帖子，我就收到了上百封邮件。许多人发现这是对 Python 和 OOP 的简单介绍。

`class`是 [Python](https://hackernoon.com/tagged/python) 中的一个基本构件。它不仅是许多流行程序和库的基础，也是 Python 标准库的基础。理解什么是类，何时使用它们，以及它们如何有用是至关重要的，也是本文的目标。在这个过程中，我们将探索术语*面向对象* [*编程*](https://hackernoon.com/tagged/programming) 的含义以及它如何与 Python 类联系在一起。

# 一切都是物体…

`class`关键字到底是用来做什么的？就像它基于功能的表亲`def`一样，它关注事物的*定义*。`def`用于定义一个函数，`class`用于定义一个*类*。什么是阶级？只是数据和函数的逻辑分组(当在类中定义时，后者经常被称为“方法”)。

我们所说的“逻辑分组”是什么意思？嗯，一个类可以包含我们想要的任何数据，并且可以有我们想要的任何函数(方法)附加到它上面。我们试图创建事物之间有逻辑联系的类，而不是简单地将随机的事物放在一起命名为“类”。很多时候，类是基于现实世界中的对象的(比如`Customer`或者`Product`)。其他时候，类是基于我们系统中的概念，比如`HTTPRequest`或`Owner`。

不管怎样，类是一种*建模*技术；一种思考程序的方式。当你以这种方式思考和实现你的系统时，你被认为是在执行面向对象的编程。“类”和“对象”是经常互换使用的词，但它们实际上不是一回事。理解它们的不同之处是理解它们是什么以及它们如何工作的关键。

# ..所以万物都有类？

类可以被认为是创建对象的蓝图。当我*使用`class`关键字定义*一个客户类时，我实际上并没有创建一个客户。相反，我创建的是一种构造“客户”对象的指导手册。让我们看看下面的示例代码:

```
**class** **Customer**(object):
    *"""A customer of ABC Bank with a checking account. Customers have the*
 *following properties:*

 *Attributes:*
 *name: A string representing the customer's name.*
 *balance: A float tracking the current balance of the customer's account.*
 *"""*

    **def** __init__(self, name, balance=0.0):
        *"""Return a Customer object whose name is *name* and starting*
 *balance is *balance*."""*
        self.name = name
        self.balance = balance

    **def** withdraw(self, amount):
        *"""Return the balance remaining after withdrawing *amount**
 *dollars."""*
        **if** amount > self.balance:
            **raise** **RuntimeError**('Amount greater than available balance.')
        self.balance -= amount
        **return** self.balance

    **def** deposit(self, amount):
        *"""Return the balance remaining after depositing *amount**
 *dollars."""*
        self.balance += amount
        **return** self.balance
```

`class Customer(object)`行*不*创建新客户。也就是说，仅仅因为我们*定义了*一个`Customer`并不意味着我们*创造了*一个；我们仅仅概述了创建一个`Customer`对象的*蓝图*。为此，我们用适当数量的参数调用该类的`__init__`方法(减去`self`，我们稍后会讲到)。

因此，为了使用我们通过定义`class Customer`(用于创建`Customer`对象)创建的“蓝图”，我们几乎像调用函数一样调用类名:`jeff = Customer('Jeff Knupp', 1000.0)`。这一行简单地说“使用`Customer`蓝图为我创建一个新对象，我称之为`jeff`”

被称为*实例*的`jeff` *对象*，是`Customer` *类*的实现版本。在我们调用`Customer()`之前，没有`Customer`对象存在。当然，我们可以创建尽可能多的`Customer`对象。然而，仍然只有一个`Customer` *类*，不管我们创建了多少个*类的*实例。

# `self`？

那么所有`Customer`方法的`self`参数是怎么回事呢？这是什么？为什么，这是实例，当然！换句话说，像`withdraw`这样的方法定义了从*某个抽象客户账户*中取钱的指令。调用`jeff.withdraw(100.0)`将那些使用*的指令放到* `*jeff*` *实例*上。

所以当我们说`def withdraw(self, amount):`时，我们是在说，“这是你如何从一个客户对象(我们称之为`self`)和一个美元数字(我们称之为`amount`)中取钱。`self`是被调用`withdraw`的`Customer`的*实例*。这也不是我在做类比。`jeff.withdraw(100.0)`只是`Customer.withdraw(jeff, 100.0)`的简写，它是完全有效的(如果不经常看到的话)代码。

# `__init__`

`self`可能对其他方法有意义，但是`__init__`呢？当我们调用`__init__`时，我们正在创建一个对象，那么怎么可能已经有了一个`self`？Python 允许我们将`self`模式扩展到构造对象的时候，即使它并不*完全*适合。试想一下`jeff = Customer('Jeff Knupp', 1000.0)`和叫`jeff = Customer(jeff, 'Jeff Knupp', 1000.0)`是一样的；传入的`jeff`也是结果。

这就是为什么当我们调用`__init__`时，我们*通过说类似`self.name = name`的话来初始化*对象。记住，由于`self` *是*实例，这就相当于说`jeff.name = name`，和`jeff.name = 'Jeff Knupp`一样。同理，`self.balance = balance`与`jeff.balance = 1000.0`相同。在这两行之后，我们认为`Customer`对象已经“初始化”,可以使用了。

## 小心你的`__init__`

在`__init__`完成之后，调用者可以正确地假设对象已经准备好使用。也就是在`jeff = Customer('Jeff Knupp', 1000.0)`之后，我们就可以开始在`jeff`上打`deposit`和`withdraw`电话了；`jeff`是一个**完全初始化的**对象。

想象一下，我们对`Customer`类的定义略有不同:

```
**class** **Customer**(object):
    *"""A customer of ABC Bank with a checking account. Customers have the*
 *following properties:*

 *Attributes:*
 *name: A string representing the customer's name.*
 *balance: A float tracking the current balance of the customer's account.*
 *"""*

    **def** __init__(self, name):
        *"""Return a Customer object whose name is *name*."""* 
        self.name = name

    **def** set_balance(self, balance=0.0):
        *"""Set the customer's starting balance."""*
        self.balance = balance

    **def** withdraw(self, amount):
        *"""Return the balance remaining after withdrawing *amount**
 *dollars."""*
        **if** amount > self.balance:
            **raise** **RuntimeError**('Amount greater than available balance.')
        self.balance -= amount
        **return** self.balance

    **def** deposit(self, amount):
        *"""Return the balance remaining after depositing *amount**
 *dollars."""*
        self.balance += amount
        **return** self.balance
```

这可能看起来是一个合理的选择；我们只需要在开始使用实例之前调用`set_balance`。然而，没有办法将这一点传达给打电话的人。即使我们详尽地记录下来，我们也不能*强迫*呼叫者在呼叫`jeff.withdraw(100.0)`之前呼叫`jeff.set_balance(1000.0)`。由于在调用`jeff.set_balance`之前`jeff`实例甚至*都没有*一个 balance 属性，这意味着对象还没有被“完全”初始化。

经验法则是，不要在`__init__`方法之外给引入新的属性，否则你已经给了调用者一个没有完全初始化的对象。当然，也有例外，但这是一个需要牢记的好原则。这是更大的对象一致性概念的一部分:不应该有任何一系列的方法调用会导致对象进入没有意义的状态。

不变量(比如，“balance 应该总是一个非负数”)应该在进入和退出方法时都成立。一个对象不可能仅仅通过调用它的方法就进入无效状态。不言而喻，一个对象也应该在有效状态下启动，这就是为什么在方法中初始化所有东西是很重要的。

# 实例属性和方法

在类中定义的函数称为“方法”。方法可以访问对象实例中包含的所有数据；他们可以访问和修改之前在`self`上设置的任何内容。因为它们使用了`self`，所以它们需要一个类的实例才能被使用。因此，它们通常被称为“实例方法”。

如果有“实例方法”，那么肯定也有其他类型的方法，对吗？是的，有，但是这些方法有点深奥。我们将在这里简单介绍一下，但是可以更深入地研究这些主题。

# 静态方法

*类属性*是在*类级别*设置的属性，与*实例级别*相对。普通属性是在`__init__`方法中引入的，但是一个类的一些属性在所有情况下都适用于*所有*实例。例如，考虑下面一个`Car`对象的定义:

```
**class** **Car**(object):

    wheels = 4

    **def** __init__(self, make, model):
        self.make = make
        self.model = model

mustang = Car('Ford', 'Mustang')
**print** mustang.wheels
*# 4*
**print** Car.wheels
*# 4*
```

一个`Car`总是有四个`wheels`，不管是`make`还是`model`。实例方法可以像访问常规属性一样访问这些属性:通过`self`(即`self.wheels`)。

但是，有一类方法叫做*静态方法*，它们不能访问`self`。就像类属性一样，它们是不需要实例就能工作的方法。因为实例总是通过`self`被引用，所以静态方法没有`self`参数。

以下是`Car`类的有效静态方法:

```
**class** **Car**(object):
    ...
    **def** make_car_sound():
        **print** 'VRooooommmm!'
```

不管我们有什么样的车，它总是发出同样的声音(至少我这样告诉我十个月大的女儿)。为了明确这个方法不应该接收实例作为第一个参数(即“普通”方法上的`self`，使用了`@staticmethod`装饰器，将我们的定义变成:

```
**class** **Car**(object):
    ...
    @staticmethod
    **def** make_car_sound():
        **print** 'VRooooommmm!'
```

# 类方法

静态方法的一个变体是*类方法*。不是接收*实例*作为第一个参数，而是传递给*类*。它也是使用装饰器定义的:

```
**class** **Vehicle**(object):
    ...
    @classmethod
    **def** is_motorcycle(cls):
        **return** cls.wheels == 2
```

类方法现在可能没有多大意义，但那是因为它们在我们的下一个主题中使用得最多:*继承*。

# 遗产

虽然面向对象编程作为建模工具是有用的，但是当引入*继承*的概念时，它才真正变得强大。*继承*是一个“子”类*派生*一个“父”类的数据和行为的过程。这里举个例子肯定能帮到我们。

想象一下我们经营一家汽车经销商。我们出售各种类型的车辆，从摩托车到卡车。我们通过价格在竞争中脱颖而出。具体来说，我们如何确定一辆车的价格:5000 美元 x 一辆车的车轮数量。我们也喜欢回购我们的汽车。我们提供统一费率——车辆行驶里程的 10%。对于卡车，费率为 10，000 美元。对于汽车，8000 美元。对于摩托车，4000 美元。

如果我们想用面向对象的技术为我们的经销商创建一个销售系统，我们该怎么做呢？对象会是什么？我们可能有一个`Sale`类、一个`Customer`类、一个`Inventory`类等等，但是我们几乎肯定会有一个`Car`、`Truck`和`Motorcycle`类。

这些类看起来像什么？利用我们所学的，这里有一个`Car`类的可能实现:

```
**class** **Car**(object):
    *"""A car for sale by Jeffco Car Dealership.*

 *Attributes:*
 *wheels: An integer representing the number of wheels the car has.*
 *miles: The integral number of miles driven on the car.*
 *make: The make of the car as a string.*
 *model: The model of the car as a string.*
 *year: The integral year the car was built.*
 *sold_on: The date the vehicle was sold.*
 *"""*

    **def** __init__(self, wheels, miles, make, model, year, sold_on):
        *"""Return a new Car object."""*
        self.wheels = wheels
        self.miles = miles
        self.make = make
        self.model = model
        self.year = year
        self.sold_on = sold_on

    **def** sale_price(self):
        *"""Return the sale price for this car as a float amount."""*
        **if** self.sold_on **is** **not** None:
            **return** 0.0  *# Already sold*
        **return** 5000.0 * self.wheels

    **def** purchase_price(self):
        *"""Return the price for which we would pay to purchase the car."""*
        **if** self.sold_on **is** None:
            **return** 0.0  *# Not yet sold*
        **return** 8000 - (.10 * self.miles)

    ...
```

好吧，这看起来很合理。当然，我们很可能在这个类中有许多其他的方法，但是我已经展示了我们特别感兴趣的两个:`sale_price`和`purchase_price`。我们稍后会看到为什么这些很重要。

现在我们已经有了`Car`类，也许我们应该创建一个`Truck`类？让我们遵循我们为 car 所做的相同模式:

```
**class** **Truck**(object):
    *"""A truck for sale by Jeffco Car Dealership.*

 *Attributes:*
 *wheels: An integer representing the number of wheels the truck has.*
 *miles: The integral number of miles driven on the truck.*
 *make: The make of the truck as a string.*
 *model: The model of the truck as a string.*
 *year: The integral year the truck was built.*
 *sold_on: The date the vehicle was sold.*
 *"""*

    **def** __init__(self, wheels, miles, make, model, year, sold_on):
        *"""Return a new Truck object."""*
        self.wheels = wheels
        self.miles = miles
        self.make = make
        self.model = model
        self.year = year
        self.sold_on = sold_on

    **def** sale_price(self):
        *"""Return the sale price for this truck as a float amount."""*
        **if** self.sold_on **is** **not** None:
            **return** 0.0  *# Already sold*
        **return** 5000.0 * self.wheels

    **def** purchase_price(self):
        *"""Return the price for which we would pay to purchase the truck."""*
        **if** self.sold_on **is** None:
            **return** 0.0  *# Not yet sold*
        **return** 10000 - (.10 * self.miles)

    ...
```

哇哦。那就是*几乎和*一模一样的汽车类。编程最重要的规则之一(一般来说，不仅仅是在处理对象时)是“干”或“T21”on t**R**EPE at**Y**yourself。我们肯定在这里重复了一遍。事实上，`Car`和`Truck`类的区别仅仅在于*一个字符*(除了注释)。

那么是什么原因呢？我们哪里出错了？我们的主要问题是我们直接进入了具体的领域:`Car` s 和`Truck` s 是真实的东西，作为类具有直观意义的有形对象。然而，它们共享如此多的共同数据和功能，似乎必须有一个我们可以在这里引入的抽象。的确有:s 的概念。

# 抽象类

一个`Vehicle`不是真实世界的物体。相反，它是一些现实世界的物体(如汽车、卡车和摩托车)体现的一个概念。我们希望利用这样一个事实，即这些对象中的每一个都可以被认为是一种消除重复代码的工具。我们可以通过创建一个`Vehicle`类来实现:

```
class Vehicle(object):
    """A vehicle for sale by Jeffco Car Dealership.

    Attributes:
        wheels: An integer representing the number of wheels the vehicle has.
        miles: The integral number of miles driven on the vehicle.
        make: The make of the vehicle as a string.
        model: The model of the vehicle as a string.
        year: The integral year the vehicle was built.
        sold_on: The date the vehicle was sold.
    """

    base_sale_price = 0

    def __init__(self, wheels, miles, make, model, year, sold_on):
        """Return a new Vehicle object."""
        self.wheels = wheels
        self.miles = miles
        self.make = make
        self.model = model
        self.year = year
        self.sold_on = sold_on

    def sale_price(self):
        """Return the sale price for this vehicle as a float amount."""
        **if** self.sold_on is not None:
            **return** 0.0  # Already sold
        **return** 5000.0 * self.wheels

    def purchase_price(self):
        """Return the price for which we would pay to purchase the vehicle."""
        **if** self.sold_on is None:
            **return** 0.0  # Not yet sold
        **return** self.base_sale_price - (.10 * self.miles)
```

现在我们可以通过替换`class Car(object)`行中的`object`来使`Car`和`Truck`类*继承`Vehicle`类中的*。括号中的类是继承而来的类(`object`本质上是“没有继承”的意思)。我们稍后将讨论我们为什么要写这个)。

我们现在可以用一种非常简单的方式定义`Car`和`Truck`:

```
**class** **Car**(Vehicle):

    **def** __init__(self, wheels, miles, make, model, year, sold_on):
        *"""Return a new Car object."""*
        self.wheels = wheels
        self.miles = miles
        self.make = make
        self.model = model
        self.year = year
        self.sold_on = sold_on
        self.base_sale_price = 8000

**class** **Truck**(Vehicle):

    **def** __init__(self, wheels, miles, make, model, year, sold_on):
        *"""Return a new Truck object."""*
        self.wheels = wheels
        self.miles = miles
        self.make = make
        self.model = model
        self.year = year
        self.sold_on = sold_on
        self.base_sale_price = 10000
```

这是可行的，但是有一些问题。首先，我们仍然在重复大量的代码。我们最终想要摆脱所有的重复。第二，更有问题的是，我们已经引入了`Vehicle`类，但是我们真的应该允许人们创建`Vehicle`对象吗(相对于`Car`或`Truck` s)？一个`Vehicle`只是一个概念，不是一个真实的东西，那么下面说的是什么意思:

```
v = Vehicle(4, 0, 'Honda', 'Accord', 2014, None)
print v.purchase_price()
```

一个`Vehicle`没有一个`base_sale_price`，只有像`Car`和`Truck`这样的单个*子*类有。问题是`Vehicle`实际上应该是一个*抽象基类*。抽象基类是只能从其继承的类；您不能创建 ABC 的*实例*。这意味着，如果`Vehicle`是一个 ABC，以下是非法的:

```
v = Vehicle(4, 0, 'Honda', 'Accord', 2014, None)
```

不允许这样做是有意义的，因为我们从来没有打算让车辆被直接使用。我们只是想用它来抽象出一些常见的数据和行为。那么我们如何让一个类成为 ABC 呢？简单！`abc`模块包含一个名为`ABCMeta`的元类(元类有点超出了本文的范围)。将一个类的元类设置为`ABCMeta`并使其方法之一*为虚拟的*会使其成为一个 ABC。虚拟方法是 ABC 说必须存在于子类中的方法，但不一定要实际实现。例如，车辆类别可以定义如下:

```
**from** **abc** **import** ABCMeta, abstractmethod

**class** **Vehicle**(object):
    *"""A vehicle for sale by Jeffco Car Dealership.*

 *Attributes:*
 *wheels: An integer representing the number of wheels the vehicle has.*
 *miles: The integral number of miles driven on the vehicle.*
 *make: The make of the vehicle as a string.*
 *model: The model of the vehicle as a string.*
 *year: The integral year the vehicle was built.*
 *sold_on: The date the vehicle was sold.*
 *"""*

    __metaclass__ = ABCMeta

    base_sale_price = 0

    **def** sale_price(self):
        *"""Return the sale price for this vehicle as a float amount."""*
        **if** self.sold_on **is** **not** None:
            **return** 0.0  *# Already sold*
        **return** 5000.0 * self.wheels

    **def** purchase_price(self):
        *"""Return the price for which we would pay to purchase the vehicle."""*
        **if** self.sold_on **is** None:
            **return** 0.0  *# Not yet sold*
        **return** self.base_sale_price - (.10 * self.miles)

    @abstractmethod
    **def** vehicle_type():
        *""""Return a string representing the type of vehicle this is."""*
        **pass**
```

现在，由于`vehicle_type`是一个`abstractmethod`，我们不能直接创建一个`Vehicle`的实例。只要`Car`和`Truck`继承`Vehicle` **和**定义`vehicle_type`，我们就可以实例化那些类了。

回到我们的`Car`和`Truck`类中的重复，让我们看看是否可以通过提升基类的公共功能来消除它，`Vehicle`:

```
**from** **abc** **import** ABCMeta, abstractmethod
**class** **Vehicle**(object):
    *"""A vehicle for sale by Jeffco Car Dealership.*

 *Attributes:*
 *wheels: An integer representing the number of wheels the vehicle has.*
 *miles: The integral number of miles driven on the vehicle.*
 *make: The make of the vehicle as a string.*
 *model: The model of the vehicle as a string.*
 *year: The integral year the vehicle was built.*
 *sold_on: The date the vehicle was sold.*
 *"""*

    __metaclass__ = ABCMeta

    base_sale_price = 0
    wheels = 0

    **def** __init__(self, miles, make, model, year, sold_on):
        self.miles = miles
        self.make = make
        self.model = model
        self.year = year
        self.sold_on = sold_on

    **def** sale_price(self):
        *"""Return the sale price for this vehicle as a float amount."""*
        **if** self.sold_on **is** **not** None:
            **return** 0.0  *# Already sold*
        **return** 5000.0 * self.wheels

    **def** purchase_price(self):
        *"""Return the price for which we would pay to purchase the vehicle."""*
        **if** self.sold_on **is** None:
            **return** 0.0  *# Not yet sold*
        **return** self.base_sale_price - (.10 * self.miles)

    @abstractmethod
    **def** vehicle_type(self):
        *""""Return a string representing the type of vehicle this is."""*
        **pass**
```

现在`Car`和`Truck`类变成了:

```
**class** **Car**(Vehicle):
    *"""A car for sale by Jeffco Car Dealership."""*

    base_sale_price = 8000
    wheels = 4

    **def** vehicle_type(self):
        *""""Return a string representing the type of vehicle this is."""*
        **return** 'car'

**class** **Truck**(Vehicle):
    *"""A truck for sale by Jeffco Car Dealership."""*

    base_sale_price = 10000
    wheels = 4

    **def** vehicle_type(self):
        *""""Return a string representing the type of vehicle this is."""*
        **return** 'truck'
```

这完全符合我们的直觉:就我们的系统而言，轿车和卡车之间的唯一区别是基本销售价格。定义一个`Motorcycle`类也同样简单:

```
**class** **Motorcycle**(Vehicle):
    *"""A motorcycle for sale by Jeffco Car Dealership."""*

    base_sale_price = 4000
    wheels = 2

    **def** vehicle_type(self):
        *""""Return a string representing the type of vehicle this is."""*
        **return** 'motorcycle'
```

# 继承和长期服务协议

尽管看起来我们使用继承来消除重复，但我们真正做的只是提供适当的抽象层次。而*抽象*是理解继承的关键。我们已经看到了使用继承的一个副作用是我们减少了重复的代码，但是从调用者的角度来看呢？使用继承如何改变代码？

事实证明，相当多。假设我们有两个类，`Dog`和`Person`，我们想要编写一个函数，它接受任一类型的对象，并打印出所讨论的实例是否能说话(狗不能，人可以)。我们可以编写如下代码:

```
**def** can_speak(animal):
    **if** isinstance(animal, Person):
        **return** True
    **elif** isinstance(animal, Dog):
        **return** False
    **else**:
        **raise** **RuntimeError**('Unknown animal!')
```

当我们只有两种动物时，这是可行的，但是如果我们有二十种，或者两百种动物呢？这个链条会变得很长。

这里的关键见解是`can_speak`不应该关心它在处理什么类型的动物，动物类本身应该告诉*我们*它是否会说话。通过引入一个定义了`can_speak`的公共基类`Animal`，我们减轻了它的类型检查负担。现在，只要它知道传入的是一个`Animal`,确定它是否能说话就很简单了:

```
**def** can_speak(animal):
    **return** animal.can_speak()
```

这是因为`Person`和`Dog`(以及我们从`Animal`派生的任何其他类)遵循*利斯科夫替换原则*。这表明我们应该能够在任何需要父类(`Animal`)的地方使用子类(比如`Person`或`Dog`),一切都会很好。这听起来很简单，但是它是我们将在以后的文章中讨论的强大概念的基础:*接口*。

# 摘要

希望您已经学习了很多关于 Python 类是什么、为什么它们有用以及如何使用它们的知识。类和面向对象编程的主题非常深奥。事实上，它们触及了计算机科学的核心。这篇文章并不是对类的详尽研究，也不应该是你唯一的参考。网上有数以千计的关于 OOP 和类的解释，所以如果你没有找到一个合适的，搜索一下肯定会发现一个更适合你的。

一如既往，欢迎评论中的更正和争论。尽量保持文明。

Jeff Knupp 于 2017 年 3 月 27 日发布

*原载于 2017 年 3 月 27 日 jeffknupp.com*[](https://jeffknupp.com/blog/2017/03/27/improve-your-python-python-classes-and-object-oriented-programming/)**。**

> *[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 T21 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。*
> 
> *要了解更多信息，请阅读我们的“关于”页面、[喜欢/在脸书上给我们发消息](http://bit.ly/HackernoonFB)，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)*
> 
> *如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！*