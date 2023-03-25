# Java 默认方法

> 原文：<https://medium.com/hackernoon/java-8-default-methods-37fc65c6a85a>

![](img/5a1cb5ce2b3b5590272043efacecf1d1.png)

Photo by [Christian Kaindl](https://unsplash.com/photos/xnUQO2DwXOo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

作为一名多年的 Java 开发人员，您可能对 Java 应该是什么样子有非常强烈的感觉。面向对象、单继承、多接口实现、泛型等。但是自从 Java 8 诞生以来，情况略有变化。你试过如何在接口中声明可选函数吗？

# 历史

Java 8 带来了许多现代的、强大的新特性——、流等等。尽管全新的特性伴随着我们不熟悉的全新语法而来，但 Java 的基础依然牢固。但是如果我们想在接口层支持的话，这将是一个突破性的改变。比方说，如果我们想在现有的接口中添加新的功能，所有的实现都应该更新，否则就会中断。对我们来说，将会有一个很大的兼容问题，所以这里出现了默认方法的想法。它允许我们将功能实现添加到接口中。所以升级到 Java 8 不会让我们头疼。

> 如果你对 Lambda 感兴趣，可以在这里查看 Java 中的 [Lambda 表达式](/@jintin/lambda-expression-in-java-ca9e1222eb7c)

# 默认方法

可以想象，在现有的接口中添加新的功能是很困难的，因为我们需要在世界各地添加实现。所以我们决定将实现直接添加到接口本身。下面是一个基本的例子:

```
public interface A {

  **default** void a() **{**
    // add implementation here
  **}**
}
```

是的，就这么简单。如果你把关键字`default`放在接口中函数声明的前面，那么你可以像普通函数一样添加实现。

有了这个新功能，我们可以实现一些以前做不到的事情，甚至是一些邪恶的事情。

1.  可选功能。
2.  多重遗传。
3.  共同实施。

# 任选功能

如果我们在接口中有一个函数，我们想把它标记为可选的，我们可以给它添加一个空的实现。

```
public interface A {

  **default void a() {
  }** }public Class realA implement A {
  // no need to implement a() again
}
```

除了多余的气味之外，你应该问问你自己，你的界面是否承担了太多我们可以分成不同部分的责任。没有对错之分，但是在你动笔之前，一定要多考虑一秒钟。

# 多重遗传

Java 不支持多重继承，但由于接口现在可以实现，它几乎像一种可多重继承的语言。

```
public interface A {
  **default void common() {
    System.out.println("This is common a.");
  }**
}public interface B {
  **default void common() {
    System.out.println("This is common b.");
  }**
}public Class realAB implement A, B {
  public void test() {
    **common();**
  }
}
```

`common()`会执行哪一个？a 还是 B？结果是编译失败。多重继承不仅仅是 Java 8 的问题，这里有一个通用的解决方案:我们应该向编译器提供上下文。

```
public Class realAB implement A, B {
  public void test() {
    **A.super.common(); // execute A
    B.super.common(); // execute B**
  }
}
```

问题解决了。但是如果可能的话，我们应该尽量避免重复命名的情况。

# 通用实现

如果一个接口包含一些通用的实现。这是默认方法的最佳匹配。这里有一个简单的例子。

```
public interface Callback<T> {
  void success(T t); default void fail(String msg) {
    LogUtils.log(msg);
    UIUtils.showError(msg);
  }
}public Class ClassA implement Callback<A> { public void success(A a) {
    // success A
  }
}public Class ClassB implement Callback<B> { public void success(B b) {
    // success B
  }
}
```

如果每个`fail()`功能都有相同的逻辑，就没有必要重复。

# 摘要

默认方法为开发 Java 应用程序带来了一个新世界。但是我们仍然需要区分类、抽象类和接口的不同角色。我们应该利用默认方法的能力，但仍然遵守我们的基本规则——接口是接口，而不是类或抽象类。

# 参考

 [## 默认方法(Java 教程>学习 Java 语言>接口和继承)

### 这篇 Java 初学者教程描述了 Java 编程语言的编程基础

docs.oracle.com](https://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html)