# Swift 代码片段:从可选的新值中有选择地更新对象

> 原文：<https://medium.com/hackernoon/selectively-update-object-from-an-optional-new-value-91f7ef59973d>

[后面的](https://hackernoon.com/tagged/following)运算符可用于根据新的可选值更新对象。当我们不想让[对象](https://hackernoon.com/tagged/object)从新的可选值更新为零时，可以使用这个方法。

在映射服务器响应时经常使用，因为我们不会在每个响应中获取所有参数，并且通过使用该运算符，我们不必一次又一次地检查所有非可选值。

```
infix operator ?= : MultiplicationPrecedencefunc ?= <T> ( property: inout T?, newValue: T?) { if let value = newValue { property = value }}func ?= <T> ( property: inout T, newValue: T?) { if let value = newValue { property = value }}
```

> 使用

```
var name: String? = nilscreenName ?= name
```