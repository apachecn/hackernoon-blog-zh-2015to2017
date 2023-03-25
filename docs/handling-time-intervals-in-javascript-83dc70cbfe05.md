# 在 JavaScript 中处理时间间隔

> 原文：<https://medium.com/hackernoon/handling-time-intervals-in-javascript-83dc70cbfe05>

# 问题

在开发[电子](https://www.amitmerchant.com/Handling-Time-Intervals-In-Javascript/electron.atom.io)应用[pomolecon](https://github.com/amitmerchant1990/pomolectron)的时候，我需要通过 [JavaScript](https://hackernoon.com/tagged/javascript) 的 [setInterval()](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setInterval) 功能来处理不同的时间间隔。我基本上需要在我的应用程序中实现三个定时器:

*   25 分钟的波莫多罗
*   5 分钟的短暂休息
*   10 分钟的长时间休息

我可以想出两种方法来实现所有三种情况下的倒计时。第一，使用三种不同的`setInterval()`实现不同的定时器。第二，找到另一种方式对所有三个定时器使用相同的`setInterval()`。

# 解决办法

所以，我尝试了第二种方法。我可以对所有三个计时器使用相同的`setInterval()`方法，将它分配给一个变量，如下所示..

```
var pomodoroIntervalId;function startTimer(duration, display) {
  timer = duration;
  pomodoroIntervalId = setInterval(function(){
    if (--timer < 0) {
        timer = duration;
    }

    minutes = parseInt(timer/60, 10);
    seconds = parseInt(timer%60, 10); minutes = minutes < 10 ? '0'+minutes : minutes;
    seconds = seconds < 10 ? '0'+seconds : seconds; display.textContent = minutes+ ":" + seconds; if(minutes == 0 && seconds == 0){
      notifyUser();
    }
  }, 1000);
}
```

然后通过传递间隔 ID 的 [clearInterval()](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/clearInterval) 方法首先清除当前时间间隔，从而在其他定时器之间使用相同的`startTimer()`。在我们的例子中，我把它分配给了一个全局变量`pomodoroIntervalId`。这基本上清除了当前运行的[时间间隔](https://hackernoon.com/tagged/time-interval)，以便下次设置另一个定时器时使用。代码如下所示..

```
function resetTimer() {
  clearInterval(pomodoroIntervalId);
}
```

这给了我们在不同的用例中使用相同的`setInterval()`函数的好处，这使得代码干净利落。我认为这是 JavasScript 的一个非常方便的特性。

快乐编码！

> [黑客午间](http://bit.ly/Hackernoon)是黑客们下午的开始。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受提交](http://bit.ly/hackernoonsubmission)并很高兴[讨论广告&赞助](mailto:partners@amipublications.com)的机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！