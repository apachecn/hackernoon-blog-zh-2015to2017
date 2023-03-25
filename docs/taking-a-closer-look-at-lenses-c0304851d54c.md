# 仔细观察镜片

> 原文：<https://medium.com/hackernoon/taking-a-closer-look-at-lenses-c0304851d54c>

所以当我们第一次学习创建我们自己的数据类型时，我们回顾了记录语法的概念。记录语法的概念非常简单。我们希望创建带有命名字段的对象。这使得我们可以避免为了从对象中获取任何类型的数据而一直在对象上进行模式匹配的乏味工作。记录语法允许我们创建从对象中提取单个字段的函数。除了从对象中检索字段，我们还可以创建一个修改过的对象。我们只指定想要更改的记录。

```
data Task = Task
  { taskName :: String
  , taskExpectedMinutes :: Int
  , taskCompleteTime :: UTCTime }

truncateName :: Task -> Task
truncateName task = task { taskName = take 15 originalName }
  where
    originalName = taskName task
```

我们在这个小代码片段中看到了这两种想法的例子。请注意，这与 Java 或 Javascript 等语言中的语法完全不同。在 Javascript 中，我们将编写一个功能类似的函数，如下所示:

```
function truncateName(task) {
  task.taskName = task.taskName.substring(0,15);
  return task;
}
```

这更符合大多数程序员对访问器和设置器字段的看法。我们将字段的名称放在对象本身之后，而不是之前。假设我们在数据模型之上添加了另一层。我们开始看到记录语法变得有点笨拙的方式:

```
data Project = Project
  { projectName :: String
  , projectCurrentTask :: Task
  , projectRemainingTasks :: [Task] }

truncateCurrentTaskName :: Project -> Project
truncateCurrentTaskName project = project { projectCurrentTask = modifiedTask }
  where
    cTask = projectCurrentTask project
    modifiedTask = cTask { taskName = take 15 (taskName cTask) }
```

在这个例子中，我们会发现 Javascript 代码看起来更加简洁。不可否认，它执行的是就地更新对象的“更简单”的操作。

那么在 Haskell 中我们能做些什么呢？我们注定要一辈子使用记录语法并对对象进行奇怪的更新吗？当然不是！有一个很棒的工具可以让你获得更自然的语法。它还使我们能够在代码中执行一些很酷的功能。工具是透镜和棱镜。透镜和棱镜为我们的物体提供了不同的获取和设置方法。制作镜头有几种不同的方法，但我们将集中使用`Control.Lens`库。

# 镜头

镜头是获取对象并从对象上“剥离”层的功能。它们允许我们进入更深的潜在领域。语法可能有点混乱，所以一开始很难编写自己的镜头。幸运的是，`Control.Lens.TH`图书馆为我们提供了帮助。首先，按照惯例，我们将更改数据类型，以便所有字段名都以下划线开头:

```
data Task = Task
  { _taskName :: String
  , _taskExpectedMinutes :: Int
  , _taskCompleteTime :: UTCTime }

data Project = Project
  { _projectName :: String
  , _projectCurrentTask :: Task
  , _projectRemainingTasks :: [Task] }
```

现在我们可以使用指令模板 Haskell 函数“makeLenses”它将生成我们的数据类型所需的 getter 和 setter 函数:

```
data Task = Task
  { _taskName :: String
  , _taskExpectedMinutes :: Int
  , _taskCompleteTime :: UTCTime }

makeLenses ‘’Task

data Project = Project
  { _projectName :: String
  , _projectCurrentTask :: Task
  , _projectRemainingTasks :: [Task] }

makeLenses ‘’Project
```

如果你没有阅读[上周关于数据的文章](https://www.mmhaskell.com/blog/2017/6/5/flexible-data-with-aeson)。艾森，你可能会想“哇，哇，哇，停。这是什么模板 Haskell 胡说八道？”模板 Haskell 是一个系统，我们可以让编译器为我们生成样板代码。它在许多情况下都很有用，但是也有一些权衡。

好处显而易见。模板 Haskell 允许我们避免编写非常乏味且不需要动脑筋的代码。缺点更隐蔽一点。首先，它有增加编译时间的趋势。第二，我们最终将使用的许多函数不会在我们的源代码中的任何地方定义。对于我们的镜头来说，这不会是太大的问题。生成的函数将使用字段名或它们的派生名称。但是对于新的 Haskell 开发人员来说，这仍然令人沮丧。此外，镜头的类型错误可能会非常混乱。这增加了新手可能遇到的困难。即使是经验丰富的 Haskell 开发人员也经常被它们搞糊涂。所以模板 Haskell 在可访问性方面有一定的代价。

关于镜头需要记住的一点是，我们不必使用模板 Haskell 来生成它们。事实上，我会在这里展示我们如何创建这些镜头函数的定义。不要费力去理解语法。我只是在演示生成的代码量并不是特别大:

```
data Task = Task
  { _taskName :: String
  , _taskExpectedMinutes :: Int
  , _taskCompleteTime :: UTCTime }

taskName :: Lens’ Task String
taskName func task@Task{ _taskName = name} =
  func name <&> \newName -> task {_taskName = newName }

taskExpectedMinutes :: Lens’ Task Int
taskExpectedMinutes func task@Task{_taskExpectedMinutes = expMinutes} =
  func expMinutes <&> \newExpMinutes -> task {_taskExpectedMinutes = newExpMinutes}

taskCompleteTime :: Lens’ Task UTCTime
taskCompleteTime func task@Task{_taskCompleteTime = completeTime} =
  func completeTime <&> \newCompleteTime -> task{_taskCompleteTime = newCompleteTime}

data Project = Project
  { _projectName :: String
  , _projectCurrentTask :: Task
  , _projectRemainingTasks :: [Task] }

projectName :: Lens’ Project String
projectName func project@Project{ _projectName = name} =
  func name <&> \newName -> project {_projectName = newName }

projectCurrentTask :: Lens’ Project Task
projectCurrentTask func project@Project{ _projectCurrentTask = cTask} =
  func cTask <&> \newTask -> project {_projectCurrentTask = newTask }

projectRemainingTasks :: Lens’ Project [Task]
projectRemainingTasks func project@Project{ _projectRemainingTasks = tasks} =
  func tasks <&> \newTasks -> project {_projectRemainingTasks = newTasks }
```

编写自己的镜头可能会很乏味。但它也能让你更精确地控制你的镜头类型实际输出的镜头。例如，您可能根本不想让特定的字段成为公共的，或者您可能希望它们是只读的。这在编写自己的镜头时更容易。因此，我们可以从这段代码中观察到的一件事是，我们为对象中的每个不同字段都提供了一个函数。这个函数实际上封装了 getter 和 setter。我们将根据镜头的用途使用其中的一种。

# 经营者

Haskell 库因使用奇怪的操作符而臭名昭著。Lens 可能是这里最大的罪犯之一。但是我们将尽量把自己限制在几个最基本的操作符上。这将使我们对镜头在获取和设置方式上的操作有所了解。

我们关心的第一个操作符是“视图”操作符，`(^.)`。这是一个简单的“get”操作符，允许您访问特定对象的字段。所以现在我们可以重新编写第一个代码片段来展示这个操作符。它被称为“视图”操作符，因为它是`view`函数的同义词，这就是我们如何将它表达为非操作符:

```
truncateName :: Task -> Task
truncateName task = task { _taskName = take 15 originalName }
  where
    originalName = task ^. taskName
    -- equivalent to: `view taskName task`
```

下一个操作符是“集合”操作符，`(.~)`。如您所料，这允许我们返回一个变异的对象，带有一个或多个更新的字段。让我们更新我们的简单截断函数的定义，以使用它:

```
truncateName' :: Task -> Task
truncateName' task = task & taskName .~ take 15 (task ^. taskName)
```

通过引入`%~`操作符，我们甚至可以做得更好。它允许我们在镜头上应用任意函数。在这种情况下，我们希望使用字段的当前值，只是对其应用了`take 15`函数。我们将用它来完成我们的函数定义。

```
truncateName’’ :: Task -> Task
truncateName’’ task = task & taskName %~ take 15
```

注意`&`本身就是反函数应用运算符。在这种情况下，它充当一个简单的优先运算符。我们可以用它来组合同一个物品的不同镜头操作。例如，在下面的例子中，我们同时更改了任务名称和预期时间:

```
changeTask :: Task -> Task
changeTask task = task 
  & taskName .~ “Updated Task”
  & taskExpectedMinutes .~ 30
```

关于镜片，需要注意的一点是，你把镜片放得越深，它们就越强大。用功能构图很容易构图镜头。例如，还记得截断项目的当前任务名称有多烦人吗？嗯，有了透镜就容易多了！

```
truncateCurrentTaskName :: Project -> Project
truncateCurrentTaskName project = project
  & projectCurrentTask . taskName %~ take 15
```

在这种情况下，我们可以用`currentTask.taskName`访问任务的名称。这几乎看起来像 javascript 语法！它允许我们毫不费力地修改任务的名称！

# 棱镜

现在我们已经了解了透镜的基本原理，我们可以更深入地看一下棱镜。镜头让我们得以窥视一个产品类型的不同领域。但是棱柱让我们可以看到一个和类型的不同分支。我在这个博客上不太使用这个术语，所以这里有一个简单的例子来解释总和与产品类型的关系:

```
-- 
-- “Product” type...one constructor, many fields
data OriginalTask = OriginalTask
  { taskName :: String
  , taskExpectedMinutes :: Int
  , taskCompleteTime :: UTCTime }

-- “Sum” type...many constructors
data NewTask =
  SimpleTask String |
  HarderTask String Int |
  CompoundTask String [NewTask]
```

所以最上面的例子是一个“产品”类型，有几个字段和一个构造函数。因为我们已经使用记录语法命名了字段，所以我们称它为“杰出的”产品。底部类型是“sum”类型，因为它有不同的构造函数。我们可以在这种类型上生成棱镜，就像我们为`Task`类型生成透镜一样。我们将使用`makePrisms`功能，而不是`makeLenses`:

```
data NewTask =
  SimpleTask String |
  HarderTask String Int |
  CompoundTask String [NewTask]

makePrisms ''NewTask
```

请注意透镜和棱镜之间的惯例差异。对于镜头，我们给字段名加下划线，然后镜头名没有下划线。使用棱镜，构造函数看起来“干净”,棱镜名有下划线。

从根本上说，棱镜涉及到探索一个对象的可能分支。因此它们可能失败，所以它们返回`Maybe`值。因为这些值可能不在那里，我们用`^?`操作符访问它们。这将移除构造函数本身，并从对象中提取值本身。它将每个对象中的字段转换为“无区别”的产品。这意味着如果只有一个字段，我们得到那个字段，如果有许多字段，我们得到一个元组。

```
>> let a = SimpleTask "Clean"
>> let b = HarderTask "Clean Kitchen" 15
>> let c = CompoundTask “Clean House” [a,b]
>> a ^? _SimpleTask
Just “Clean”
>> b ^? _HarderTask
Just (“Clean Kitchen”, 15)
>> a ^? _HarderTask
Nothing
>> c ^? _SimpleTask
Nothing
```

如果我们在每个不同的类型上使用记录语法，这种行为不会改变。因为每当我们有两个或更多的字段时，我们都会得到一个元组，所以我们实际上可以使用透镜来进一步研究这个元组。这提供了一些很酷的可组合性。注意`_1`是一个镜头，允许我们访问一个元组的第一个元素。同样的还有`_2`和`_3`等等。

```
>> b ^? _HarderTask._2
Just 15
>> c ^? _CompoundTask._1
Just “Clean House”
>> a ^? _HarderTask._1
Nothing
```

最好的部分是，我们仍然可以在棱镜上有“设置器”，而且这些设置器甚至没有错误条件！默认情况下，如果您尝试设置某项内容并使用“错误的”分支，您将会重新获得原始对象:

```
>> let b = HarderTask "Clean Kitchen" 15
>> b & _SimpleTask .~ "Clean Garage"
HarderTask “Clean Kitchen" 15
>> b & _HarderTask._2 .~ 30
HarderTask “Clean Kitchen" 30
```

# 折叠和横向底漆

我留给你们的最后一个例子是我们可以用棱镜做的一些更高级的事情。我们来看看`Folds`和`Traversals`的概念。棱镜解决了一个可能存在也可能不存在的结构的一部分。遍历和折叠是处理许多可能存在也可能不存在的部分的函数。

假设我们有一份`NewTask`商品的清单。我们不关心复合任务或基本任务。我们只想知道我们在`HarderTask`项目上的总时间。我们可以定义这样一个函数来执行手动模式匹配:

```
sumHarderTasks :: [NewTask] -> Int
sumHarderTasks = foldl timeFromTask 0
  where
    timeFromTask accum (HarderTask _ time) = time + accum
    timeFromTask accum _ = accum

{- In GHCI:

>> let tasks = [SimpleTask "first", HarderTask "second" 10, SimpleTask "third", HarderTask "fourth" 15, CompoundTask [SimpleTask "last"]]
>> sumHarderTasks tasks
25

-}
```

但是我们也可以用棱镜更容易地做到这一点。使用`traverse`功能进行折叠和遍历。这个功能如此强大，它值得自己的文章。

在这个例子中，我们将“遍历”我们的任务列表。我们将使用棱镜挑出所有带有`HarderTask`的。然后我们将通过应用`_2`镜头得到的值相加。厉害！

```
sumHarderTasks :: [NewTask] -> Int
sumHarderTasks tasks = sum (tasks ^.. traverse . _HarderTask . _2)
```

所以如果我们分解它，`tasks ^.. Traverse`会给我们原始列表。然后添加`_HarderTask` prism 将过滤它，只给我们留下使用`HarderTask`构造函数的任务。最后，应用`_2`镜头将把这个过滤后的列表变成任务元素的时间列表。最后，我们对这个列表求和。

# 结论

所以在这篇文章中，我们看到了透镜和棱镜的基本介绍。我们看到了如何使用模板 Haskell 在我们的类型上生成这些函数。我们对一些相关的操作者有了一个简短的感觉。我们还看到了这些概念如何让我们更容易处理嵌套结构。如果你有时间更全面地介绍镜头，你应该看看来自 BayHac 的 John Wiegley 的演讲！这是我写这篇文章的主要灵感。它极大地帮助了我理解我在这里提出的想法。特别是，如果你想要更多关于遍历和折叠的想法，他有一些超级酷的例子。

如果你是 Haskell 的新手，不要担心这些高级的东西！查看我们的[入门清单](https://www.mmhaskell.com/checklist)。它会教你一些工具和资源，让你开始用 Haskell 编码！

也许你已经做了一点 Haskell，但是在你深入像透镜这样的东西之前，想要更多的基础练习。如果是这样，你应该下载我们的[递归工作簿](https://www.mmhaskell.com/workbook)。它将带你了解递归和高阶函数的基础。你还会得到一些练习题来测试你的技能！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)，[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！