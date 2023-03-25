# 让你的单元测试更上一层楼

> 原文：<https://medium.com/hackernoon/unit-test-naming-given-when-then-java-84407911b858>

![](img/1baf0f58c1d507fc541abafef9c37db4.png)

[http://www.flickr.com/photos/16446716@N05/4652680633](http://www.flickr.com/photos/16446716@N05/4652680633)

这篇文章的目的不是强调编写单元测试或者测试驱动开发过程的重要性。有大量的文章证明了这一点，并介绍了各种单元测试方法。相反，我将分享我写我认为有效的和*自我记录*单元测试的经验。代码示例在 [Java](https://hackernoon.com/tagged/java) 和 JUnit 4 中。

# 编写单元测试很有趣，如果…

1.  您遵循一致的*方法，这使您能够更快地编写测试。*
2.  您的测试代码被分解成易于管理的小块(工作单元)。
3.  您的测试方法使用有效的、易于遵循的命名约定和结构。
4.  您可以通过像 [JaCoCo](https://github.com/jacoco/jacoco) 这样的工具生成代码覆盖报告来量化您的进度。

本文集中讨论这个列表中的前三项。

让我们测试一个简单的`Customer`类，它在构造时执行自己的验证:

我看到了 3 个潜在的测试候选人:

1.  如果传入了空参数，则测试构造函数。
2.  如果传入了非空参数，则测试构造函数。
3.  测试`getName()`方法。

一旦我们测试了上面的所有内容，这个类将被完全测试(100%代码覆盖率)。让我们来实现这些测试:

g

# 测试方法名称

我建议您的测试方法名称遵循*methodnameundest _ given condition _ expected behavior*的命名约定，其中 *givenCondition* 是可选的。让我们看看它如何适用于`CustomerTest`。

`ctor_givenNameIsNull_throwsException()`包括:

1.  ctor 表示我们正在测试构造函数的行为
2.  *givenNameIsNull* 指定空值`name`传入构造函数时的条件
3.  *throwsException* 表示当条件满足时(`name`为 null)，我们将抛出一个异常。注意`@Test(expected = IllegalArgumentException.class)`定义了我们期望的具体异常。

`ctor_givenNameIsValid_setsName()`包括:

1.  ctr 表示我们正在测试构造函数的行为
2.  *givenNameIsValid* 指定将非空值`name`传递给构造函数时的条件
3.  *setsName* 表示当条件满足时(`name`非空)，应该设置我们测试`Customer`的类成员`name`。

`getName_returnsName()`包括:

1.  getName 表示我们正在测试`getName()`方法的行为
2.  *returnsName* 表示应该返回一个有效的非空值`name`。

> 您的测试方法名称应该提供一个关于被测试类的功能的好主意。

另外，请注意，每个测试只有几行代码，并且只验证一部分功能(被测试的代码的一个*单元)。*

# 当时给定的

让我们看看测试方法本身的内容。注意，所有的测试都遵循 [*给定-何时-然后*](https://martinfowler.com/bliki/GivenWhenThen.html) 的风格来表示测试步骤。这使得每个单独的测试成为一个逻辑的、易于理解的、一致的步骤序列。

让我们更详细地描述一下每个给定时间步骤:

**给定** —在执行核心测试逻辑之前，设置特定条件或创建执行测试所需的对象。这实际上是测试的先决条件。

```
// GIVEN
String expectedName = "linda";
testSubject = new Customer(expectedName);
```

注意，给定的**并不总是必要的——有时某个特定的测试不需要设置，或者在用`@Before`注释的方法内部的每个测试之前都执行了普通的设置。**

**When** —这是我们测试的动作被触发的地方。

```
// WHEN
String actualName = testSubject.getName();
```

**然后**——这是我们断言一旦测试动作发生，应用程序的状态就像预期的那样(我们测试中的代码按照我们预期的方式运行)。

```
// THEN
*assertTrue*(expectedName.equals(actualName));
```

有些情况下，显式*则*是不必要的。例如，如果*当*步预计抛出异常(如上例所示)。

# 活文档

有效地应用以上建议，为您的项目创建简洁、易读的*活* *文档*。当您的产品代码发生变化时，相应的测试也会发生变化，从而更新您的项目文档。此外，这个*活文档*对于你的整个技术团队来说是一个重要的**知识资源**，尤其是对于那些刚刚加入你的项目的开发人员和测试人员。

# 结论

我希望您看到在您的单元测试中应用这些约定的价值。如果这对你来说是新的，我相信一旦你尝试采用这些指导方针，写测试会突然变得更容易，更快，我敢说很有趣！除了导致更少的错误之外，您将发现未来的代码重构也明显更容易，因为现在您的代码由一致的自我记录测试支持。

附注:编写好的测试是一项非常抢手的技能！；)

**访问我的** [**Android 博客**](http://www.valueof.io) **阅读关于 Jetpack Compose 和其他 Android 主题**