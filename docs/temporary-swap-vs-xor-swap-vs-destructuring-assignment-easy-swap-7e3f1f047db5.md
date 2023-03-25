# 带有气泡的临时交换与销毁分配(简单交换)rt

> 原文：<https://medium.com/hackernoon/temporary-swap-vs-xor-swap-vs-destructuring-assignment-easy-swap-7e3f1f047db5>

这些类型的变量赋值是所有(如果不是大多数)编程语言的共同主题。每一个都带来了各种性能和可读性问题。

直到我尝试用一个大数组运行冒泡排序，我才意识到交换的这些性能问题。冒泡排序已经是一种弱排序算法(O(N)的时间复杂度)，但是您交换变量的方式会使该过程花费更长的时间。

```
function bubbleSort(array) {
 for(let i = 0; i < array.length; i++) {
  for(let j = i; j < array.length; j++) {
   if (array[i] > array[j]) {

    //let temp = array[i];
    //array[i] = array[j];
    //array[j] = temp;

    // Easy Swap #1 [array[i], array[j]] = [array[j], array[i]];
    // Easy Swap #1A array[j] = [array[i], array[i] = array[j]][0];
   }
  }
 }
return array;
}sorting([3,6,7,2,4,5,8,1,11,52,73,23,45,22,12,15,7,26,32,66])
```

将它们运行到基准测试中后:
测试 1:温度分配
测试 2:破坏分配

*   测试 1 x 1，068，020 ops/sec 1.61% (58 次采样)
*   测试 2 x 27，651 ops/sec 3.25%(采样 57 次运行)

临时分配要快得多。

> [黑客中午](http://bit.ly/Hackernoon)是黑客们开始他们下午活动的方式。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受提交](http://bit.ly/hackernoonsubmission)并很高兴[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如需了解更多信息，请[阅读我们的关于页面](https://goo.gl/4ofytp)、[类似/在脸书](http://bit.ly/HackernoonFB)上留言给我们，或简单地, [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果您喜欢这个故事，我们建议您阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实视为理所当然！