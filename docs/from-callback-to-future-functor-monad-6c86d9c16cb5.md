# 从回调到未来->仿函数->单子

> 原文：<https://medium.com/hackernoon/from-callback-to-future-functor-monad-6c86d9c16cb5>

## 动机

函数式编程的一个基本概念是**复合**。它只是简单地描述了一种机制，通过这种机制，我们将简单的事物组合起来构建更复杂的事物，然后将新的结果组合起来构建更复杂的事物，等等。

有了作文，你永远不会碰壁，因为你必须一直问自己*“这个函数的返回值是多少？”*，或者换一种说法*“这个东西是什么意思？”*

如果你用过 Node.js，你肯定知道下面的代码

```
fs.readFile('...', function (err, data) {
  if (err) throw err;
    ....
});
```

快速提醒一下，上面的代码是*延续传递式(CPS)* 函数的典型用法。CPS 函数“fs.readFile”带有一个额外的参数，这是一个表示*延续*的回调。一旦完成，CPS 不是返回一个值给调用者，而是调用延续函数，回调，传递它自己的计算结果。

我不介意在代码中使用回调。事实上，回调对于表示副作用或事件通知是很好的，但是一旦你开始使用它们来管理你的控制流，你就被困住了。为什么？因为他们不作曲。

所以我们就问“什么意思，*外延*还是上面函数的返回值？”。答案是“未定义”。除了测试它实际上是…未定义的，我们不能对‘未定义’做任何有用的事情。

问题是，在回调过程中，你被传送到了另一个执行流，这是一张单程票。

把回调想象成物理学中著名的[黑洞](https://en.wikipedia.org/wiki/Black_hole):

> 黑洞是一个数学定义的时空区域，表现出如此强大的引力，以至于没有粒子或电磁辐射可以逃离它

特别

> *在许多方面，黑洞就像一个理想的黑体，因为它不反射光*

回调函数也不反映程序流中的光线。

如果稍后，在第一个回调函数中，您使用了另一个回调风格的函数，那么您将丢失第二个*流*，并且您将落入另一个洞。回调用的越多，就越沉入地狱。

那么，我们如何在代码中前进而不陷入黑洞呢？

答案是*作文*。但是要使用 composition，我们必须从一个函数中得到一些东西，我们知道 CPS 函数并不反映任何东西。所以我们必须，以某种方式，让他们返回一些东西。但是它们应该返回什么值呢？这就是这篇文章的动机。

即使你已经知道现有的 [JavaScript](https://hackernoon.com/tagged/javascript) 解决方案。我强烈建议你阅读这篇文章。您将看到指称(即功能)思维的力量，以及它如何允许我们为这些解决方案获得简洁明了的语义。

## 欢迎来到未来

如果我们考虑这些函数:文件读取操作、网络请求、DOM 事件……它们有什么共同点？

它们描述了不能在*立即*中实现的操作，我的意思是我们不能在当前的程序流程中等待它们的完成(就像我们对正常功能所做的那样)。所以他们描述*未来*的结果。

所以让我们让它们返回一个特殊的类型: *Future* ，来表示它们的最终结果。关键思想是要有一个可以传递给其他函数的第一类值。

一个[未来](https://hackernoon.com/tagged/future)的意义是什么？它只是表示一个值，该值将在特定时间后出现(可以是 0)。时间的含义可以是一个明确的事件，就像我们说 x 秒后，但也可以用相对的术语给出，就像我们说两个未来同时完成，或者一个未来在另一个之后完成。

这很重要:

> 未来的结果总是表示一个不变的值。

这意味着，无论如何我们都不能改变完成值。这个约束不仅简化了我们的实现，还简化了我们对其语义的推理。

我们的未来可以用一个简单的死状态机来实现。机器从*等待*开始，然后发展到*完成*并在此停止。一旦完成，它将永远存在。

在内部,‘Future’类型仍然依赖于回调，但是它们不会服务于任何控制流机制。相反，我们将只把它们用于正确的目的:事件通知。

```
function Future() {
    // a list to store pending subscribers
    this.slots = [];
}// notify completion
Future.prototype.ready = function(slot) {        
    if(this.completed)
        slot(this.value);
    else
        this.slots.push(slot);
}//a simple log utility
function logF(f) {
    f.ready( v => console.log(v) );
}
```

我们为外部实现者提供了完成未来的方法

```
Future.prototype.complete = function(val) {
    // ensure immutability
    if(this.completed)
        throw "Can't complete an already completed future!"     this.value = val;
    this.completed = true; // notify subscribers
    for(var i=0, len=this.slots.length; i<len; i++) {
        this.slots[i](val);
    } // release all, we don't need this anymore
    this.slots = null;
}
```

未来最简单的例子，是用一个给定的值立即完成*。为此，我们将定义一个名为 unit 的方法*

```
// unit : Value -> Future<Value>
Future.unit = function(val) {
    var fut = new Future();
    fut.complete(val);
    return fut;
}logF( Future.unit('hi now') );
```

上面，我使用了一个*类型注释*来简化对代码的推理:

单位:价值→未来<value>简单来说就是:</value>

1.  “单位”是一个函数
2.  它接受一个泛型类型“值”作为输入
3.  返回保存此泛型类型值的“Future”实例。

使用通用术语“值”意味着我们实际上不关心类型，因为类型信息在这里是不相关的。

另一个例子是延迟了一段时间的值

```
// delay: (Value, Number) -> Future<Value>
Future.delay = function(v, millis) {
    var f = new Future();
    setTimeout(function(){
        f.complete(v);
    }, millis);
    return f;
}logF( Future.delay('hi, it\'s been 5 seconds', 5000) );
```

“延迟”的结果是在给定的持续时间之后以给定的值完成的未来。

回到“readFile”示例，我们现在可以使用返回未来值的函数来代替 CPS 函数:

```
var fs = require('fs');// readFileF: (String, Object) -> Future<String>
function readFileF(file, options) {
    var f = new Future();
    fs.readFile(file, options, function (err, data) {
      // We'll deal with errors later in the post
      if (err) throw err;
      f.complete(data);
    });
    return f;
}logF( readFileF('test.txt', { encoding: 'utf8'}) )
```

“readFileF”的结果表示保存文件内容的未来，该文件的名称作为参数给出。

## 期货交易:见见我们的第一位客人

你可以把“未来”想象成一个魔盒，里面装着一个函数的最终结果。

但是为了做任何有用的事情，我们必须为我们的未来类型提供一些有用的操作。否则我就像我们创造了另一个无用的“未定义”一样。

那么我们可以用期货来描述哪些操作呢？

让我们从计算未来盒子里的值开始，我们称之为“fmap”(功能图的缩写，或者更好的未来图)

这里有一个“fmap”的例子。给定一个保存文本文件内容的未来，我们想要计算这个内容的长度。

```
var textF = readFileF('test.txt', { encoding: 'utf8'});// fmap: ( Future<String>, (String -> Number) ) -> Future<Number>
var lengthF = textF.fmap( text => text.length )
logF( lengthF )
```

“lengthF”的意思是保存文件长度的未来，该文件的内容由给定的未来保存。

概括地说，“fmap”有两个输入:

1.  未来值
2.  正常值上的映射函数。

它的输出是一个 Future，保存应用于输入 Future 的结果的映射函数的结果。请注意，输入和输出同时完成。

非正式地，我们可以这样描述它

```
fmap( Future<value>, func ) = Future< func(value) >
```

实现“fmap”只有几行代码(我们将把它定义为未来原型上的一个方法)

```
Future.prototype.fmap = function(fn) {
    var fut = new Future();
    this.ready(function(val) {
        fut.complete( fn(val) );
    });
    return fut;
}
```

当输入期货完成时，我们完成输出期货。我们负责应用映射函数。

回到上面的例子，我们将保存文件内容的未来转换为保存该内容长度的未来。

听起来很熟悉？这非常类似于众所周知的 JavaScript 数组的 map 方法。实际上，这是完全相同的概念:

*   “数组”类型是一个包含多个值的盒子
*   “未来”类型是保存未来值的盒子
*   Array.map(…)'转换数组框中的值，并给出另一个封装了转换后的值的数组框
*   ' Future.fmap(…)'转换 Future 框中的值，并给出另一个封装了转换后的值的 Future 框。

来见见我们的第一位客人，我们说数组和未来类型都是**函子**。这是因为它们可以使用一个普通的函数，将其应用于内部的任何内容，然后返回另一个表示转换结果的实例。

数组和未来不是唯一的函子，每当你遇到

*   一个类型充当其他类型的*上下文*
*   知道如何在其中应用普通函数

那么这个类型很有可能是函子。

既然我们能够将未来映射到另一个未来。如果我们能像处理正常价值一样，将函数直接应用于期货，那将会很酷。

所以与其打电话

```
textF.fmap( c => c.length )
```

我们希望有一种特殊的函数“lengthF ”,它直接作用于期货

```
// lengthF : Future<String> -> Future<Number>
function lengthF(strF) {
   return strF.fmap( s => s.length )
}
```

看起来微不足道，但我们现在可以像这样重写文件示例

```
nbCharsF = lengthF( readFileF('...') )
```

我们说‘length f’是一个*提升的*函数。*提升*一个函数到*箱式*类函子上，将函数应用从正常值提升到*箱式*值。在我们的例子中，我们将作用于字符串的函数“length: String - > Number”提升为作用于期货的函数“length f:Future<String>->Future<Number>”。

概括地说，我们定义了一个名为“lift1”的函数(因为它只提升 arity 为 1 的函数，即接受 1 个输入)

```
Future.lift1 = function(fn) {
    return fut => fut.fmap(fn);
}
```

这个简单的抽象允许我们将异步调用转换成普通的函数调用。在我们的示例“lengthF( readFileF(…))”中。

通过组合' readFileF '和' lengthF ',我们能够在不离开主程序流的情况下表示异步计算。

## 多参数函数怎么样？(或者见见我们的第二位客人)

在我们回答这个问题之前，让我们花一点时间来思考一个基本事实:什么类型的人被允许生活在我们的未来盒子里？未来对所有类型的人都有同样的意义吗？

“Future <string>”的含义很明显:一个将来可用的字符串类型的值。同样的推理可以推广到所有其他类型吗？数字，对象，数组？当然……期货本身怎么样？‘Future<future>’是什么意思？</future></string>

为了直观地理解这个想法，让我们选择一个简单的例子，我们想检查一个目录，然后读取里面第一个文件的内容(为了简单起见，我们假设没有嵌套的目录)。

在 Node 中，我们可以使用异步函数“fs.readdir”来获取给定目录中的文件名数组。我们将首先推导出我们的未来函数

```
// readDirF : String -> Future< Array<String> >
function readDirF(path) {
    var f = new Future();
    fs.readdir(path, (err, files) => {
      // hold on, it's coming
      if (err) throw err;
      f.complete(files);
    });
    return f;
}
```

“readDirF”的意思是在给定的目录中保存一个文件名数组。

为了解决我们的问题，我们需要

1.  读取一个目录的内容，这个目录给了我们一个保存代表文件名的字符串数组的未来。
2.  选择第一个文件名，然后读取其内容。

我们可以在这里使用“fmap”吗？让我们看看如果在 Node 中运行这段代码会发生什么

```
var resultF = readDirF("testdir")
               .fmap(files => readFileF(files[0]))logF( resultF )
```

我们得到…哎呀

```
{ slots: [] }
```

显然，这里有问题。我们得到的不是控制台中的文件内容，而是一个对象的文本表示，它显然是一个未来的实例。

这是因为“fmap”接受其映射函数返回的任何结果，并用它来完成输出 Future。由于上面的映射函数返回了另一个 Future(' read filef '的结果)，' fmap '只是盲目地用它完成了' resultF '。

但是一个未来不能与另一个未来一起完成，它仍然要等待产生的、*嵌套的*未来完成后才能继续。

所以我们需要一个守卫函数来监视这种情况。它不是立即完成，而是等待嵌套的未来完成。

我们称之为“扁平化”，因为它将一个“未来”<future>(一个两层的未来)扁平化为一个简单的未来</future>

```
// flatten: Future< Future<Value> > -> Future<Value>
Future.prototype.flatten = function() {
    var fut = new Future();
    this.ready(function(fut2) {
        fut2.ready( function(val){
            fut.complete(val);
        } );
    });
    return fut;
}
```

我们可以得到这样的结果

```
var result = 
  readDirF("testdir")
    .fmap(files => readFileF(files[0], {encoding: 'utf8'}))logF( result.flatten() )
```

我们不会每次都调用“fmap”和“flatten ”,而是将这个用例打包在一个单独的调用中:一个将完成两个操作的特设函数:映射到一个 2 层的未来，然后将其扁平化。为了保持有意义，我们将把它命名为“平面地图”(我知道这很糟糕)

```
Future.prototype.flatMap = function( fn ) {
    return this.fmap(fn).flatten();
}
```

从概念上讲，我们上面描述的是一种对 2 相关计算进行排序的方式，因为第一个函数从第二个函数(文件名列表)的结果中获取输入(文件名)，所以“readFileF”依赖于“readDirF”。

现在让我介绍我们的第二位客人，除了是一个函子，未来也是一个**单子**，因为它定义了一种将计算描述为一系列步骤的方式。使用上面的“平面图”方法，我们可以将多个函数调用链接在一起，每一步都从上一步获取输入。

至于函子，单子有很多用例；技术上来说，一个单子需要的只是

*   一种将正常值提升为一元的*方法:在我们的例子中，是“Future.unit”从正常值得到未来值。*
*   一种链接两个连续操作的方法:每个单子都有其适当的链接逻辑。在我们的例子中，逻辑是用“平面图”来处理的，即在前进之前等待未来的完成。

我们已经看到，第二个操作(flatMap)也可以用另外两个操作(“fmap”和“flatten”)来定义，如果你有一个定义“fmap”函数的函子，你所需要的就是一个 flatten 操作来简化一个 2 层结构。

那么现在，让我们回到主要问题，我们如何提升一个需要多个参数来作用于期货的函数？

让我们再次选择文件示例，这一次我们想要连接给定目录中所有文件的内容，我们的代码看起来像这样

```
// concatF : (Future<String>, ...) -> Future<String>
var resultF = concatF( text1F, text1F, ...)
```

上面的函数是什么意思？结果表示期货持有输入期货持有的所有字符串的串联。从逻辑上来说，“concatF”需要等待所有输入期货才能继续，因此当所有输入竞争时，输出完成。

为了实现，我们将从两个参数的情况开始

```
// lift2: ( (a, b) -> c) -> ( (Future<a>, Future<b>) -> Future<c>)
Future.lift2 = function(fn) {
    return (fut1, fut2) => {
        fut1.flatMap( value1 =>
            fut2.flatMap( value2 => 
                Future.unit( fn(value1, value2) );
            )
        )
    };
}
```

除了外表，代码的逻辑相当简单，让我们一步一步来:

*   “Future.lift2”采用“作用于 2 个正常值的函数”并返回“作用于 2 个未来值的函数”
*   返回(提升)的函数实际上做的是
*   使用“平面图”对 2 个操作进行排序(使用嵌套调用)，
*   第一个操作本身什么也不做，它只是将第一个结果绑定到变量“value1 ”,该变量在作用域中仍然可见
*   第二个嵌套操作将函数 fn 应用于两个结果:“值 1”和“值 2”
*   因为“fn”返回一个正常值，而“flatMap”期望一个未来值，所以我们使用“Future.unit”将正常结果提升为未来结果。

这里的技巧是:在所有的期货上按顺序运行“flatMap”允许函数等待所有的期货，嵌套的“flatMap”调用为函数提供了所有完成值的范围。

最后一次使用“Future.unit”提升结果表明，多参数提升函数不同于描述顺序操作的*一元*(如“readDir”中的“readFile”)。

下面是一个连接两个文件内容的例子

```
var concat2F = Future.lift2( (str1, str2) => str1+' '+str2 );var text1F = readFileF('test1.txt', {encoding: 'utf8'});
var text2F = readFileF('test2.txt', {encoding: 'utf8'});logF( concat2F(text1F, text2F) );
```

请注意，即使在第二个未来“文本 2F”在第一个“文本 1F”之前完成的情况下，该功能仍然需要等待“文本 1F”。完成后，该功能将立即继续，因为“text2F”已经完成。

我们可以得出结论，一个多自变量函数不依赖于其输入的完成顺序，也不依赖于它们之间的相互依赖。事实上，我们也可以这样重新表达这个意思:

*   如果“fmap”表示单一操作，且
*   “平面图”表示顺序操作，那么
*   多变量提升函数表示*并行*操作。

这是有意义的，因为，就像上面的例子一样，我们一次运行所有的期货，并在继续计算之前等待它们完成。

我们可以很容易地将 lift2 中使用的模式扩展到 lift3 或 lift4，但是我们只是推广到任意数量的参数，我们需要的只是上面的两个技巧:嵌套和作用域

```
function toArray(args) {
  return Array.prototype.slice.call(args);
}Future.lift = function(fn) {
  return function() {            
    var futArgs = toArray(arguments), // our future arguments
        ctx = this; // save context ('this') return bindArg(0, []); function bindArg(index, valArgs) {        
      // wait the current Future argument        
      return futArgs[index].flatMap(function(val) {  
        // collect completion values               
        valArgs= valArgs.concat(val); // not yet the last Future argument ?
        return (index <  futArgs.length - 1) ?                  
          // flatMap (wait) the next argument
          bindArg(index+1, valArgs) : 

          // last reached, apply the collected vales
          Future.unit( fn.apply(ctx, valArgs) ); 
      });           
    }
  }
}
```

“lift”重复使用“lift2”中使用的相同技巧。因为我们事先不知道输入的确切数量，所以我们使用递归调用来迭代所有输入，等待它们完成，然后在途中收集结果(在索引处等待/平面映射未来，然后保存完成值，并对下一个未来输入重复该过程，直到所有输入都被处理)。一旦我们到达链中的最后一个输入 Future，我们就继续函数应用，然后我们返回提升后的结果。

> 我们可以使用另一种叫做“适用函子”的结构来实现 n 元提升。但是之后我们还需要解释一些术语，比如 lambdas 和 currying，所以今天我们就跳过这个。

## 错误处理

回想一下在我们转换“fs.readFile”的过程中，我们是如何忽略错误值的

```
function readFileF(file, options) {
    var f = new Future();
    fs.readFile(file, options, function (err, data) {          
      if (err) throw err;
      ...
```

我们不能在真实的代码中实现。因为我们不在主程序流中(最外面)，所以我们没有办法捕捉错误。在上面的例子中，错误向上传播，并且没有发现处理程序，Node 只是中止整个程序。

如果我们需要捕捉错误以便有机会修复它，或者可能将它重定向到某个地方，就像对用户有意义的错误消息一样，那该怎么办？

一个可能的解决方案是用*失败*的概念来扩充未来的语义。到目前为止，我们没有给未来的结果赋予任何意义，但现在我们将未来视为具有两种可能结果(完成或失败)的上下文，因此我们必须在可能失败的上下文中回顾语义。

首先，我们有一个通知完成的方法，所以我们还定义了一个通知失败的方法。

```
function Future() {
    this.slots = [];
    this.failslots = [];
}Future.prototype.failed = function(slot) {
    if(this.hasFailed)
        slot(this.error);
    else
        this.failslots.push(slot);
}
```

我们还定义了一种让未来失败的方法。

```
Future.prototype.fail = function(err) {  
    if(this.completed || this.hasFailed)
        throw "Can't fail an already settled future!"        
    this.hasFailed = true;
    this.error = err;
    for(var i=0, len=this.failslots.length; i<len; i++) {
        this.failslots[i](err);
    }
}
```

“fmap”操作的新含义是什么？

在示例“readFileF(…)中。fmap( s => s.length)'，不存在的文件的长度没有任何意义。从逻辑上讲，我们希望只转换有效的结果，这样，只有当输入未来成功完成时，输出未来才会以转换后的值完成，否则，输出未来会以相同的错误失败。同样，如果映射函数在其转换中失败，我们也必须使输出失败。

```
Future.prototype.fmap = function(fn) {
    var fut = new Future();
    this.ready( val => {            
        try        { fut.complete( fn(val) );    }
        catch(err) { fut.fail(err);              }          
    }); this.failed( err => fut.fail(err) ); return fut;
}
```

对于“展平”来说，这要复杂一些。我们有两种未来:外在的和内在的，每一种都有两种可能的结果:完成或者失败。这样我们就有 4 个案子要处理(2x2)

```
Future.prototype.flatten = function() {
    var fut = new Future(); // 1- outer fails && inner fails => output fails
    // 2- outer fails && inner completes => output fails
    this.failed( _ => fut.fail(err) ); // 3- outer completes && inner fails => output fails 
    this.ready( fut2 =>
        fut2.failed( err => fut.fail(err) );           
    );    // 4- outer completes && inner completes => output completes
    this.ready( fut2 =>
        fut2.ready( val => fut.complete(val) );
    );      
    return fut;
}
```

在“展平”的情况下，仅当内部和外部输入都完成时，输出才完成。

我们不必修改“平面地图”或“电梯”。因为它们是从“fmap”和“flatten”派生的，所以它们自动继承了它们的错误处理语义。

好吧，我们让我们的未来跳过所有后续的计算，以防失败，这留给我们一个失败的未来。那么对于一个失败的未来，我们能做什么操作呢？

只有一个，我们可以*捕捉*未来的错误并尝试修复它们，如何？通过定义一个可以将失败的未来转化为完整的未来的操作，然后可以在我们的计算中重新整合。

我们必须为“fmap”定义一个*对等*函数，我们称之为“fmapError”。这就像是 fmap 的翻转版

```
Future.prototype.fmapError = function(fn) {
    var fut = new Future();
    this.ready( val => fut.complete(val)  );
    this.failed( err => {
        try         { fut.complete( fn(err) );    }
        catch(err1) { fut.fail( err1 );           }            
    });
    return fut;
}
```

“fmapError”充当异步 catch 语句，有效结果通过该函数传递，而错误被捕获并发送到映射函数。

这里有一个简单的例子

```
readFileF('unknown file').fmapError( err => 'alternate content' )
```

但是如果我们想以一种单一的方式捕捉错误，比如在顺序操作的流水线中，该怎么办呢？

为此，我们将定义 flatMapError，它只不过是 flatMap 的*对等*版本。

```
Future.prototype.flatMapError = function( fn ) {
    return this.fmapError(fn).flatten();
}
```

作为一个使用的例子，假设我们试图从一些有时不可用的 web URL 获取一些内容，当我们的请求失败时，我们将尝试一个替代的 URL，我们可以使用“flatMapError”来捕捉第一个失败并返回替代的请求

```
resultF = requestF('/url1').flatMapError( err => requestF('/url2') )
```

“resultF”的含义是一个未来，如果成功，其内容是对“url1”的第一次请求的结果，如果第一次请求失败，则是对“url2”的第二次请求的结果。

## 副作用

我们已经定义了所有需要的操作来以可组合的方式描述计算。使用到目前为止定义的函数，我们可以以简单的同步方式处理异步函数，传递我们的未来，就像它们是正常值一样。

但是每个计算都有一个终点。这正是我们进行副作用操作的时候:更新我们的 UI，登录控制台，保存到数据库…

一个简单的方法是定义一个特殊的方法“do”来表示这种类型的操作。但是我们必须像往常一样问*do 是什么意思，它的返回值是什么？*

如果我们简单地让它返回一个没有值转换的 Future，那么它将具有与' future.fmap( Id)'相同的外延(Id 是恒等函数 x = > x)；但是我们知道实际上它们是不一样的。在我们的抽象模型中，我们不应该有这种扭曲:表示同一事物的事物就应该是同一事物。

另一个问题与时间的意义有关:“做”和输入的未来在同一时间内完成吗？如果我们的副作用操作包括将更新发送到远程服务器会怎么样？我们应该等待服务器的响应。此外，服务器的响应可能(而且经常)与我们的应用程序相关。

事实上，从上面可以清楚地看到，副作用操作只不过是被*链接*到一个给定的未来并返回给自己另一个未来的函数。这正是“平面地图”和“平面地图错误”的外延。换句话说，副作用操作只是一元函数(如果你有一些 Haskell 背景，你可能已经链接到了 *IO 一元函数*)。

## 那么承诺有什么意义呢

如果你已经意识到了承诺，那么你显然注意到了未来和承诺之间的相似之处，你可能也注意到了我是如何一路避免提及它们的。

如果你愿意，可以使用 Future 类，将 fmap、flatMap、mapError 和 flatMapError 混合成一个大的整体方法，最终你会得到或多或少类似于“Promise.then”方法的东西。

当然，总会有人认为实现一个完全符合规范的承诺要复杂得多。他甚至可能会告诉我类似于[你忽略了承诺的要点](https://blog.domenic.me/youre-missing-the-point-of-promises)。

如果您花一点时间阅读上面的 Promise/A+规范，您会注意到它并没有根据它们所表示的值来描述 Promise 操作，而是描述了任何兼容实现所遵循的过程。换句话说，规范是操作性的，而不是指示性的。操作语义与它们的*执行模型*紧密相关。这就是为什么在规范中，您会注意到对类似

> 在执行上下文堆栈仅包含平台代码之前，不得调用 onFulfilled 或 onRejected

也可以指承诺的内部状态

> 承诺必须处于三种状态之一:待定、履行或拒绝。当未决时，承诺:可以转换到履行或拒绝状态。

与操作语义相反，*指称语义*不关注应该执行什么步骤，也不关注内部表示以实现某些计算。它更侧重于事物的意义，这个意义必须根据其他意义来定义，这些意义或者是定义明确的，或者是相当明显的(如价值或功能)。

如果你从上面理解承诺规范是无用的，或者承诺本身是无用的，那么**你就没有理解我这篇文章的要点。或许你也(部分地)忽略了承诺的要点。**

你总是只能把承诺看作是模仿“；”的命令式结构(用于对同步语句进行排序)，然后专门使用它们对异步语句进行排序。另一种方法是从更实用的角度考虑它们；即未来价值。然后，您将开始更多地使用它们作为函数求值的异步结果。你将越来越多地使用提升来推导未来函数，然后像处理普通函数一样组合这些函数。

这篇文章的重点不是提出任何替代承诺的方法，而是从本质上展示指称思维的力量。一路上，我们不断询问事物的含义，这使我们对我们的概念有了一个相当清晰的说明，(即使语义是以非正式的术语呈现的)。

使用众所周知的抽象，如函子和单子，已经有了坚实的数学基础，使得推理期货的属性和导出一些操作的实现变得非常容易。这可能是一个风格的问题，但我怀疑如果我们采取纯粹的操作推理，我们可能已经实现了这一点。

[源代码可在此获得](https://gist.github.com/yelouafi/40aeb2a70a368acb6e45)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)