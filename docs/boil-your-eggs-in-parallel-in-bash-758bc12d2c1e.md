# 在 BASH 中并行煮鸡蛋

> 原文：<https://medium.com/hackernoon/boil-your-eggs-in-parallel-in-bash-758bc12d2c1e>

我们正处于一个机器拥有众多内核的时代。我们也有能以简单的方式使用这些核心的语言。

想象一下你想用 Go 这样的新语言并行煮鸡蛋:

```
var wg sync.WaitGroupfunc boilAnEgg(i int) {
    time.Sleep(time.Second)
    fmt.Println("Your egg is ready: ",i)
    wg.Done()
}func main(){
   wg.Add(10)
   for i:=1;i<=10;i++ {
      go biolAnEgg(i) # Adding "go" here simply will run the function as a goroutine here
   }
   wg.Wait()
   fmt.Println("All eggs are ready")
}
```

但是我们 40 岁的狂欢会怎么样呢？

让我们在 bash 中并行煮鸡蛋。

```
boil_an_egg() {
    sleep 1
    printf "Your egg is ready: $1\n"
}for i in $(seq 1 10)
do
    boil_an_egg $i &
done
wait
printf "All eggs are ready\n"
```

在命令末尾添加“&”会将它推入后台并行运行。

命令“wait”等待所有后台进程完成。

就这么简单。

例如，当我出于某种原因需要在 [travis-ci](https://travis-ci.com/) 中克隆大约 40 个 github 库时，仅仅使用这种方法，我就能够将克隆时间从 120 秒降低到大约 18 秒。

这是多么简单:

```
OUT=$(mktemp)
for i in garbage queue prime
do
  (git clone git@github.com:kavehmz/$i.git || echo "cloning failed for: $i" >> $OUT ) &
done
wait
[ -s $OUT ] && cat $OUT && exit 255
echo "Git repositories are ready"
```

# 奖金

列出正在运行的作业:

```
sleep 10 &
sleep 15 &
jobs[2] + Running                    sleep 15
[1] - Running                    sleep 10
```

下一个通知第一个不在列表中！你可以利用大括号:

```
(sleep 10 &)
sleep 15 &
jobs[1] + Running                    sleep 15
```

但是这次包括了第一份工作！：

```
(sleep 10) &
sleep 15 &
jobs[2] + Running                    sleep 15
[1] - Running                    sleep 10
```

现在让我们只等待第二个作业完成(%2):

```
sleep 10 & 
sleep 5 & 
sleep 15 & 
wait %2[2] - Done                       sleep 5
```

当然你不能用 bash 走得太远，但是作为一个简单的工具，它在很多地方都为你准备好了，不需要努力或者安装，你就可以在很多任务中使用它。

更多信息请访问:[https://www . GNU . org/software/bash/manual/html _ node/Job-Control-builtins . html](https://www.gnu.org/software/bash/manual/html_node/Job-Control-Builtins.html#Job-Control-Builtins)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！