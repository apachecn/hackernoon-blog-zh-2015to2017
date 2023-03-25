# 对象与数据结构

> 原文：<https://medium.com/hackernoon/objects-vs-data-structures-e380b962c1d2>

![](img/7e4ca9e2be0327984e53ae3f2c2f3161.png)

在 Java 开发的世界里，有对象就有对象。前者更重要。

困惑了吗？

面向对象编程(OOP)向世界介绍了对象的概念(小“o”)。OOP 对象是围绕状态的封装边界，提供某种公共行为。对象的概念不限于任何特定的语言。事实上，我们看到对象是用基于类的语言表示的，比如 Java/C#/Ruby/Python，还有基于原型的语言，比如 JavaScript。

Java 引入了一个叫做“Object”的 Java 类(带一个大大的“O”)。Java 对象是 Object 类的实例(包括所有子类)。这些对象是语言结构，而不是概念结构。

因此，问题来了:Java 对象有资格成为 OOP 对象吗？嗯，这要视情况而定。本文着眼于使用数据结构对象和 OOP 对象的具体情况。

## 数据结构

考虑一个表示一个人的数据结构，它包括名、姓和电话号码。这种数据结构在各种过程语言中会是什么样子？

C 语言中的个人数据结构:

```
struct Person {
  char firstName[20];
  char lastName[20];
  char phoneNumber[10];
};
```

Pascal 中的个人数据结构:

```
type
  Person = record
    firstName : string;
    lastName : string;
    phoneNumber : string;
  end
```

在 Java 中，相同数据结构可能如下所示:

```
public class Person {
  public String firstName;
  public String lastName;
  public String phoneNumber;
}
```

Java 数据结构在“技术上”不同于 C 和 Pascal 版本，因为 Java 数据结构是一个类，而不是结构或记录。但是 Java 人在功能上和 C struct 或者 Pascal 记录有区别吗？不。功能上是一样的。所有 3 种数据结构都提供了 3 个可读写的字符串字段。

重要的一点是，Java Person 对象根本不是一个“对象”，它是一个数据结构。Person 对象的存在是为了将一些数据组织成一个单一的实体，可以作为一个整体传递和管理，就像 C 结构和 Pascal 记录一样。

但是如果 Java Person 对象看起来像这样会怎么样呢:

```
public class Person {
  private String mFirstName;
  private String mLastName;
  private String mPhoneNumber; public String getFirstName() {
    return mFirstName;
  } public void setFirstName(String firstName) {
    mFirstName = firstName;
  } public String getLastName() {
    return mLastName;
  } public void setLastName(String lastName) {
    mLastName = lastName;
  } public String getPhoneNumber() {
    return mPhoneNumber;
  } public void setPhoneNumber(String phoneNumber) {
    mPhoneNumber = phoneNumber;
  }
}
```

现在，Java 对象是真正的对象吗？现在它有私有数据和公共方法。一定是 OOP 对象吧？

即使有公共的 getters 和 setters，Person 仍然是一个数据结构。它的目的改变了吗？它的行为改变了吗？不会。Person 对象仍然会以与它的 public-property 前身相同的方式和样式使用。Person 的 getter/setter 版本仍然是 100%的数据结构。

好，如果我们开始给人添加一些行为，像这样:

```
public class Person { //... same getters/setters as before, except one: public void setPhoneNumber(String phoneNumber) throws FormatException {
    validatePhoneNumber(phoneNumber);
    mPhoneNumber = phoneNumber;
  } private void validatePhoneNumber(String phoneNumber) throws FormatException {
    // Do validation here to ensure we have a legit phone number.
    // Throw an exception if its invalid.
  }}
```

现在我们有真正的 OOP 对象了吗？Person 的最新版本现在包含了验证行为，它甚至使用私有方法来实现验证。

甚至这个 Person 的最新化身也不完全是 OOP 对象。它更像是一个弗兰肯物体。电话号码验证行为实际上是一种行为——它是一种服务——这是 OOP 对象应该有的。但是请注意，我们仍然有所有这些方法来读取和写入状态。这里真的没有任何状态隐藏，也根本没有太多行为。Person 仍然主要是一个公开其整个状态的数据结构，几乎不做任何事情，并将在整个代码库中用作具体类型。

不管你给这头猪涂了多少口红，Person 都是数据结构，不是对象。

## OOP 对象

OOP 对象看起来像什么？它看起来像一种服务。OOP 对象是做事情的构造——它表现和行动。

人员数据结构有名字、姓氏和电话号码。一个人物体行走、奔跑、跳跃和说话。一个人对象做事情。

以下是一些 OOP 对象示例:

```
public interface PhoneNumberValidator { boolean validate(String phoneNumber);}
```

PhoneNumberValidator 验证给定的字符串是否代表格式正确的电话号码。验证程序中没有内部状态的指示。也许验证器有状态，也许没有，但我们知道它提供电话号码验证服务。

```
public interface PersonDataStore { Person getPeople(PeopleQuery query); void addPerson(Person person); void removePerson(Person person);}
```

PersonDataStore 提供了一种存储和查询个人数据结构的机制。在这个例子中，Person 和 PeopleQuery 都是数据结构——它们只是组织信息。然而，PersonDataStore 是一个提供服务的对象。也就是说，PersonDataStore 可以接受 PeopleQuery 数据结构，并找到所有与查询中的标准匹配的人员数据结构。

PersonDataStore 是否持久存储在内存中？它会持续到磁盘吗？它索引数据吗？作为 PersonDataStore 的客户，我们不知道这些事情，也不关心。我们只关心 PersonDataStore 做什么，因为 PersonDataStore 是一个 OOP 对象。

## 混淆对象和数据结构

再次考虑人员数据结构。在最后一个例子中，我们添加了电话号码验证行为。可以想象，我们可能希望对名字和姓氏进行类似的验证。此外，当我们向这个数据结构添加更多的字段时，这些字段可能也需要被验证。

在我们的 Person **数据结构**上验证**行为**的问题是，我们已经创建了一个候选，它将经常违反[打开/关闭原则](https://en.wikipedia.org/wiki/Open/closed_principle)、[单一责任原则](https://en.wikipedia.org/wiki/Single_responsibility_principle)和[接口分离原则](https://en.wikipedia.org/wiki/Interface_segregation_principle):

**开启/关闭原理:**

每个新字段都需要打开 Person 类并添加代码进行验证。

**单一责任原则:**

Person 类现在负责结构化数据、验证名字、验证姓氏和验证电话号码。这是许多可以独立变化的职责。

**界面分离原理:**

假设您只关心收集一个电话号码。当使用 Person 的实例时，不仅依赖于 validatePhoneNumber()方法，还依赖于 validateFirstName()和 validateLastName()方法。因此，您依赖于您不需要的方法，因此您违反了接口分离原则。

## 数据与行为

我们可以从这个人的例子中学到的一般经验是，数据和行为不会一起变化。数据按 I/O 考虑因素分组，如 web API 输入格式和数据库模式。然而，行为是按用例分组的，这些用例代表了客户希望对应用程序做什么。

当我们获取数据结构并开始添加行为时，我们会邀请 Person 示例中看到的所有 OOP 违例。

相反，根据您的用例定义行为，根据您的 I/O 需求定义数据结构，然后定义连接两者的对象。例如，一个 Android 活动可能会定义如下所示的方法:

```
// Called when the user finishes entering a phone number.
private void onPhoneNumberSet() {
  String phoneNumber = mPhoneNumberTextView.getText().toString();
  boolean isValid = mPhoneNumberValidator.validate(phoneNumber);
  if (isValid) {
    mPerson.setPhoneNumber(phoneNumber);
  } else {
    // Notify user of problem.
  }
}
```

在这个例子中，Person 保持它自己的数据结构，不知道验证行为。然后，有一个 PhoneNumberValidator 对象，它知道如何验证电话号码字符串，但是不知道任何关于 Person 数据结构的信息。最后，包含活动编排电话号码验证，然后在 Person 数据结构上设置电话号码数据。

当需要向数据源(web 或本地数据库)提交个人数据时，您可以想象这样的情况:

```
private void doSubmit() {
  mPersonDataStore.addPerson(mPerson);
}
```

我们不知道 PersonDataStore 是在与本地数据库对话，还是与 web 服务器对话，甚至是与本地内存对话。我们只知道我们的用例责任是收集个人数据并存储它。我们使用 OOP 对象来验证输入，现在我们使用 OOP 对象来存储数据。在这个过程中，数据是在 Person 数据结构中收集的。

## 为什么这么大惊小怪？

为什么有人会关心数据结构和对象之间的细微差别呢？

数据结构是状态。

让我重复一遍。

数据结构是状态。

因此，传递数据结构意味着共享状态，而共享状态是万恶之源。发明 OOP 对象的原因是为了提供一个共享状态可以被最小化和控制的范例(这就是为什么我们应该[明智地打包](/@mattcarroll.name/package-wisely-4899f9580d3#.7em3f8gel))。

把数据结构想象成你的代码中，你的 OOP 对象之间的一种交换格式。考虑以下伪代码:

```
objectA.doThing1();
objectA.doThing2();myDataStructure = objectA.extractState();objectB.setState(myDataStructure);
objectB.doThing3();
objectB.doThing4();
```

我们使用 objectA 做一些工作。然后我们通过获得一个数据结构来提取 objectA 的状态。我们通过发送从 A 获得的数据结构来初始化 objectB，现在 objectB 做一些工作。在这个例子中，myDataStructure 有一个隐含的不变性，因此更改 myDataStructure 不会对 objectA 或 objectB 产生任何隐藏的内部影响。

数据结构只是提供了一种在有组织的状态中移动的机制。它本身不提供任何行为。这就是我们应该如何在代码中利用数据结构。

认识到状态提取应该是相对低频的操作也很重要。大多数时候，你应该让对象接受并返回其他对象，而不是数据结构。

只有在应用程序跨越不同领域时，才应该使用数据结构。例如，当您在 I/O 域中接收输入时，您可以使用数据结构将该数据注入到您的业务域中。同样，当您需要向用户可视化地展示某些东西时，您可以使用数据结构从您的业务领域中提取信息，并将其发送到您的可视化领域中。数据结构是应用程序域之间的交换格式。

在开发应用程序时，请记住，Java 对象不一定是 OOP 对象，数据结构与 OOP 对象不是一回事，您应该确保识别和区分这些不同的结构。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)