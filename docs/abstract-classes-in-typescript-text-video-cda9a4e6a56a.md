# TypeScript 中的抽象类:文本和视频

> 原文：<https://medium.com/hackernoon/abstract-classes-in-typescript-text-video-cda9a4e6a56a>

这是摘自我最近免费出版的关于打字本[编程](https://hackernoon.com/tagged/programming)语言、 [*又一本打字本*](https://www.gitbook.com/book/pagalvin/yet-another-typescript-book/details)*([https://www . git Book . com/Book/pagal vin/Yet-Another-TypeScript-Book/details](https://www.gitbook.com/book/pagalvin/yet-another-typescript-book/details))的[书](https://hackernoon.com/tagged/book)。*

*它来自第九章“深度课程”在这里:*

# *抽象类*

*抽象类完善了 TypeScript 对这种性质的层次结构的支持。抽象类看起来和感觉上像标准类，但有一个关键的例外:抽象类可能永远不会被实例化。如果 JavaScript 是您的第一和主要编程语言，这可能看起来很奇怪。然而，抽象类和接口使开发人员能够自然而优雅地表达许多常见的软件设计模式。让我们考虑一个例子。*

*想象你正在写一个游戏。玩家在二维地图上放置不同类型的军事基地(例如“陆军”、“海军”)。碱基有一些共同的特征，如“名字”，但在重要的细节上彼此不同。陆军基地由士兵组成，而海军基地由船只组成。最后，在运行时，玩家可以“激活”一个基地。这就触发了基地在游戏中做一些有意义的事情。这里有一个简单的建模方法:*

```
*interface Activatable {
    ActivateSelf: () => void;
}class NaiveBase {
    private _myName: string;
    public get Name() { return this._myName; }
    constructor (name: string) {
        this._myName = name;
    }
}class NaiveArmyBase extends NaiveBase implements Activatable{
    private _totalSolders: number;
    public get TotalSolders() { return this._totalSolders; } constructor(name: string, totalSolders: number) {
        super(name);
        this._totalSolders = totalSolders;
    } public ActivateSelf() {
        throw "Not yet implemented";
    }
}class NaiveNavyBase extends NaiveBase implements Activatable {
    private _totalShips: number;
    public get TotalShips() { return this._totalShips; } constructor(name: string, totalShips: number) {
        super(name);
        this._totalShips = totalShips;
    } public ActivateSelf() {
        throw "Not yet implemented";
    }
}const naiveArmyBase = new NaiveArmyBase("First army base", 100);
const naiveNavyBase = new NaiveNavyBase("First navy base", 3);// This is allowed but makes no sense:
const someOtherBase = new NaiveBase("what kind of base is this?");*
```

*到目前为止，这已经很简单了。一个`NaiveBase`类保存一个私有属性`_myName`，并提供一个 get 访问器来检索该值。另外两个类扩展了它并添加了它们自己的属性:`NaiveArmyBase`和`NaiveNavyBase`。*

*陆军和海军基类都实现了`Activatable`接口，尽管在这个例子中，每个类的`ActiveSelf()`方法都抛出了一个异常。*

*这种建模方法有一个问题:没有简单朴素的基础。玩家从不创造普通的基地，他们总是创造一种特殊的基地。然而，没有什么可以阻止代码这样做。*

*这里还有另一个问题。这种方法迫使我们在每个类上实现`Activatable`接口。我们可以在基类上实现它，但这只会使第一个问题复杂化——现在我们已经在一个不应该实例化的类上实现了一个接口。*

*抽象类为我们解决了这个问题。下面是使用抽象类重写的代码:*

```
*interface Activatable {
    ActivateSelf: () => void;
}abstract class AbstractBase implements Activatable{
    private _myName: string;
    public get Name() { return this._myName; } constructor (name: string) {
        this._myName = name;
    } abstract ActivateSelf(): void;
}class ArmyBase extends AbstractBase {
    private _totalSolders: number;
    public get TotalSolders() { return this._totalSolders; } constructor(name: string, totalSolders: number) {
        super(name);
        this._totalSolders = totalSolders;
    } public ActivateSelf() {
        throw "Not yet implemented";
    }
}class NavyBase extends AbstractBase {
    private _totalShips: number;
    public get TotalShips() { return this._totalShips; } constructor(name: string, totalShips: number) {
        super(name);
        this._totalShips = totalShips;
    } public ActivateSelf() {
        throw "Not yet implemented";
    }
}const armyBase = new ArmyBase("First army base", 100);
const navyBase = new NavyBase("First navy base", 3);
const anotherArmyBase: Activatable = new ArmyBase("Second army base", 250);// Compiler throws an error - abstract classes can not be instantiated:
const someOtherKindOfBase = new AbstractBase("what kind of base is this?");*
```

*这个例子介绍了`abstract`关键字。我们现在有一个抽象类，`Base`。这个抽象类实现了`Activatable`接口。这样做，您可以看到 TypeScript 抽象功能的另一个特征:您可以将类和*类成员*标记为抽象。(事实上，如果该类包含任何抽象成员，则必须将其标记为 abstract)。可激活的接口需要一个方法，`ActiveSelf`。然而，这种方法只对“真正的”基地——陆军和海军基地有意义。因此，我们将 ActivateSelf 方法本身标记为抽象的:*

```
*abstract ActivateSelf(): void;*
```

*这个抽象的 ActivateSelf 方法满足可激活接口的要求。这是完美的，因为一个普通的“基地”不能有意义地激活自己——只有陆军和海军基地可以做到这一点。同时，它强制子类实现该方法。这有两个好处:*

1.  *你不能忘记这么做，因为 IDE 和编译器不允许你这么做。*
2.  *由于子类实现了接口，我们可以在需要的时候编写代码来利用它们的类型`Activatable`。*

*抽象基类展示了另一个特性:抽象类可以定义非抽象类成员。因为每个基类都有一个名字，不管基类是什么类型，定义一个具体的`_myName`属性和相关的 getter 是有意义的。子类继承这些具体的类成员(属性和方法),就像它们继承具体的类一样。*

*陆军和海军基地使用相同的`extends`关键字扩展抽象类，就像它是一个具体类一样。*

*总结这个例子，您可以看到新建陆军和海军基地的工作方式与简单例子中的方式相同:*

```
*const armyBase = new ArmyBase("First army base", 100);
const navyBase = new NavyBase("First navy base", 3);*
```

*由于两种类型的基都实现了 Activatable，因此您可以这样做:*

```
*const anotherArmyBase: Activatable = new ArmyBase("Second army base", 250);
const activatableNavyBase = <Activatable> navyBase;*
```

*让我们把这一切放在一个视频里:*