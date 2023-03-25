# 代码挑战:找出给定的表达式是否平衡

> 原文：<https://medium.com/hackernoon/code-challenge-find-whether-the-given-expression-is-balanced-d9cb9793bcf0>

Stacks!!

嘿，伙计们

最近，我在 hackerrank.com 解决一些算法难题。

我只是有一个想法，与媒体社区分享一个简单的挑战。:)

# 问题陈述

给定一个字符串表达式，找出给定的表达式是否平衡。

**样品-1**

```
Input:
{ [ ( ) ] }Output:
True
```

**样品-2**

```
Input:
{ [ } ]Output:
False
```

**样品-3**

```
Input:
{ { [ ]Output:
False
```

在寻找解决方案之前，试着解决这个问题。

# 解决办法

这个问题就是这么简单。对于每个左括号，您必须检查相应的右括号是否按顺序出现。

我们可以使用堆栈来解决这个问题。如果您想回顾一下 stack 没问题，请看一下[这个](https://www.youtube.com/watch?v=wjI1WNcIntg)。

首先，我们从遍历字符串开始，如果我们找到一个开括号，我们就把它推到堆栈中。如果我们找到一个右括号，我们就从堆栈中弹出顶部元素，检查右括号是否与左括号相关联(顶部元素弹出)。如果不是，我们返回 false，否则继续。

记住，在遍历整个字符串之后，我们必须检查堆栈的大小。如果堆栈的大小不为零(即不为空)，这意味着缺少一些右括号。

如果你不明白，请和我在一起:)

**伪代码:**

```
expression = { [ ( ) ] }for each element in the string
i=0
We have an opening braces so we push it to the stack. 
Stack: {i=1
We have an opening braces so we push it to the stack.
Stack:  [
        {
i=2
We have an opening braces so we push it to the stack.
Stack: (
       [
       {
i=3
We have an closing braces so we pop the element from stack.
Popped element: (
Closing braces: )
We continue to iterate because the opening braces is associated with the closed braces.
Stack: [
       {
i=4
We have an closing braces so we pop the element from stack.
Popped element: [
Closing braces: ]
We continue to iterate because the opening braces is associated with the closed braces.
Stack: {i=5
We have an closing braces so we pop the element from stack.
Popped element: {
Closing braces: }
We continue to iterate because the opening braces is associated with the closed braces.
Stack: EmptyThe stack is empty which means that we have an equal equal number of closing braces for all opening braces. 
```

**Java 中的代码实现:**

```
import java.util.*;class BalancedExpression {
 public static void main(String args[]) {
  System.out.println("Balanced Expression");
  Scanner in = new Scanner(System.in);
  String input = in.next();
  if (isExpressionBalanced(input)) {
   System.out.println("The expression is balanced");
  }
  else {
   System.out.println("The expression is not balanced");
  }static boolean isExpressionBalanced(String input) {
   Stack stack = new Stack();
   for (int i=0; i<input.length(); i++) {
    if (input.charAt(i) == '(' || input.charAt(i) == '{'|| input.charAt(i) == '[') {
     stack.push(input.charAt(i));
    }
    if (input.charAt(i) == ')' || input.charAt(i) == '}'|| input.charAt(i) == ']') {
     if (stack.empty()) {
      return false;
     }
     char top_char = (char) stack.pop();

     if ( (top_char == '(' && input.charAt(i) != ')') || (top_char == '{' && input.charAt(i) != '}') || (top_char == '[' && input.charAt(i) != ']') ) {
      return false;
     }   
    }
   }
   return stack.empty();
  }
 }
}
```

你可以在这里看到[源代码](https://github.com/srebalaji/Data-Structures-And-Algorithms/blob/master/stacks/BalancedExpression.java)

如果你对更具竞争性的挑战感兴趣，你可以订阅我的时事通讯 [DS Weekly](https://www.getrevue.co/profile/srebalaji) 来获得每周解决的竞争性编程挑战。:) :)