# 动画内容的闪屏技巧—勘误表

> 原文：<https://medium.com/hackernoon/the-splash-screens-trick-with-animated-content-14f4f990c0c0>

放大屏幕布局需要一些时间。在冷启动时，它会导致设备在第一个活动的布局之前先显示一个空白屏幕。一种常见的技术是使用应用程序的窗口背景主题来填充这个空白屏幕(例如，带有品牌图标)。在本文中，我将进一步展示如何制作这个图标的动画。

## **问题**

## 窗口背景技术

您的布局在一个称为装饰视图的系统视图中展开。当应用程序在你的第一个活动的布局之前启动时，你看到的就是这个视图。这里的技术是通过用一个 drawable 样式化这个视图来填充这个“空白屏幕”。

## 动画背景

你必须使用一个可绘制的动画，但它不会单独启动，这里的技巧是通过编程访问装饰视图来启动它。

## **勘误表**:动画不会像我们想的那样马上开始(参见:[https://plus . Google . com/u/0/102434905935244657491/posts/991 fzaikgc 6？cfem=1](https://plus.google.com/u/0/102434905935244657491/posts/991fZAikgC6?cfem=1)

## 逐步地

定义一个可绘制的动画作为背景

使用它作为特定风格的窗口背景主题

在清单中，配置闪屏活动以使用它

在闪屏活动中，从装饰视图中恢复动画并启动它

## 结果

我的 github 上有一个示例应用程序，祝您回顾愉快:)

[](https://github.com/jacquesgiraudel/AnimatedSplashScreen) [## jacquesgiraudel/animated splash screen

### 这是一个动画闪屏的样例应用程序(冷启动时没有黑屏)

github.com](https://github.com/jacquesgiraudel/AnimatedSplashScreen) 

*参考文献:*

*—* [*闪屏正确方式*](https://www.bignerdranch.com/blog/splash-screens-the-right-way/)

*—*[*Android 中的帧动画*](https://www.bignerdranch.com/blog/frame-animations-in-android/)

**【jacquesgiraudel.com】**[***安卓开发者***](http://jacquesgiraudel.com)