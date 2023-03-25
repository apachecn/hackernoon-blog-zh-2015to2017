# JavaScript 中的尾部调用优化(TCO)

> 原文：<https://medium.com/hackernoon/es6-tail-call-optimization-43f545d2f68b>

## TCP 如何改善内存占用的逐帧堆栈分解

![](img/f697fbde496b1dfce15b70ad74c4278b.png)

Every time we call a function, we stack another rock on top. This obviously does not scale.

ES6 带来的幕后变化之一是对尾部调用优化(TCO)的支持。尾部调用优化意味着，如果一个函数中的最后一个表达式是对另一个函数的调用，那么引擎将优化调用堆栈…