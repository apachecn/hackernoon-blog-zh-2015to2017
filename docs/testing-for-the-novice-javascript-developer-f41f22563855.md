# 面向 JavaScript 开发新手的测试

> 原文：<https://medium.com/hackernoon/testing-for-the-novice-javascript-developer-f41f22563855>

> “如果建筑者像程序员写程序那样建造房屋，那么第一只啄木鸟就会毁灭文明。”~杰拉尔德·温伯格

我喜欢测试。在我学习编码的早期，我听说过“测试驱动开发”这个东西。大约在同一时间，我开始扩大我的项目范围，并遇到了非常棘手的错误。有时有用的东西会停止工作，我不知道为什么。测试只是揭示了更多的问题；我的代码是一个无法测试的巨大混乱。测试我的代码从根本上改进了我的编码，我希望这篇介绍也能帮助你。

我希望你能从这篇文章中得到什么:

*   设置 Mocha(测试框架)和 chai(断言库)。
*   编写和运行您的测试。
*   同步测试。
*   异步测试(回调)。
*   有些功能我用了很多(只是让你知道它们的存在)。

我希望对命令行和 npm 有非常基本的了解。

## 设置

首先让我们做一个新项目。创建一个文件夹，在命令行中输入`npm init -y`，用 npm 初始化项目。

用下面的命令安装 mocha。

`npm install mocha -g`或`sudo npm install mocha -g`

并安装 chai 与:

`npm install chai --save-dev`

Mocha 是一个框架，用于设置测试、运行测试、命名测试和进行测试管理。Chai 是一个断言库，意思是你可以用它来检查事情是否如你所期望的那样进行。通过下面的例子，这将变得更加清楚。

在你的根目录下创建一个名为`index.js`的文件。这将是我们要测试的文件。还要创建一个名为`test`的文件夹，并将一个名为`index.spec.js`的文件放入其中。这个文件是我们为`index.js`编写测试的地方。

您的项目应该是这样的:

```
├── yourAwesomeProject/
│   ├── index.js
│   ├── test
│   │   ├── index.spec.js
│   ├── package.json
```

好吧！我们什么也没做，但现在是确保摩卡正常工作的最佳时机。在命令提示符下键入`mocha test`。

希望你得到这样的东西-> `0 passing (3ms)`

你刚刚跑完了测试赛！那里没有测试，但那不重要。我们很快就会用测试来填充它。

# 编写您的第一个测试！

将以下代码写入 index.js。

```
// Stub
exports.addTwoNumbers = function (a, b) {
    return 0;
}
```

这个函数被称为“存根”函数。这段代码没有任何逻辑，但它仍然返回一个数字。这使得我们可以为它编写一个测试，而不需要费力去实现函数逻辑。

跳到您的 index.spec.js 文件。我给你看代码，然后再分解。一定要自己打出来。

```
var index = require("../index");
var chai = require("chai");var expect = chai.expect;describe("index.js tests", function() {
    it("addTwoNumbers returns a number", function() {
        expect(index.addTwoNumbers(0, 0)).to.be.a("number");
    });
});
```

为了确保一切正常，再次输入`mocha test`。您应该在终端中得到这个很酷很时髦的输出:

```
index.js tests
    ✓ addTwoNumbers returns a number1 passing (11ms)
```

一切正常，让我们来理解一下我们写了什么。

前两行导入您的文件`index.js`和`chai`，前者包含您想要测试的函数，后者是您之前使用 npm 安装的断言库。`var expect = chai.expect;`只是给了我们一个简单的称呼`chai.expect`。

## describe()——对一组单独的测试进行分组

`describe(<Heading of Tests>,testsInCallback);`

描述就像一个标题。这是一种将一组测试组合在一起的方法。你会注意到在上面的例子中，“index.js tests”是标题，“addTwoNumbers returns a number”是与它组合在一起的**I**n individual**t**est(**it**)。

## it() —“个人测试”

`it(<Name of Test>, testInCallback);`

每个测试断言都需要它自己的单独测试。例如，为了测试我们的函数“addTwoNumbers”实际上可以将两个数字相加，我们应该至少添加一个其他测试。

我们将添加另一个测试:

```
var index = require("../index");
var chai = require("chai");var expect = chai.expect;describe("index.js tests", function() {
    it("addTwoNumbers returns a number", function() {
        expect(index.addTwoNumbers(0, 0)).to.be.a("number");
    }); it("addTwoNumbers can add 1 + 2", function() {
        expect(index.addTwoNumbers(1,2)).to.equal(3);
    });
});
```

我们将它添加到由 describe 函数划分的组“index.js tests”中，并将其添加到前面的单个测试的下面。您会注意到 expect 使用的语法非常易读。

`expect(<your function>).to.equal(<something>);`就是你如何检查平等。如果你正在检查深度相等，例如检查对象内部的字段是否相等，你可以像这样把“深度”加到链中:

`expect(<yourObject>).to.deep.equal(<anotherObject>);`

柴*期望*的文档清晰明了，所以我[推荐你看一下](http://chaijs.com/api/bdd/)。

如果你现在运行`mocha tests`，你会发现测试失败了。

```
index.js tests
    ✓ addTwoNumbers returns a number
    1) addTwoNumbers can add 1 + 21 passing (19ms)
  1 failing1) index.js tests addTwoNumbers can add 1 + 2:AssertionError: expected 0 to equal 3
      + expected - actual -0
      +3at Context.<anonymous> (test/index.spec.js:9:45)
```

我发现这个信息非常有价值，因为它可以让您看到您的期望实际返回了什么(在本例中是零)。

既然您的测试失败了，您可以通过修复您的`index.js`中的代码来解决它。我通常会编写更多的测试，但是现在你有了一种方法来确保你正在做的工作。这并不能使你的代码完美，但至少可以让你安心一些。

简单地说，这种技术叫做“红绿重构”。我们只讨论了“红色、绿色”的部分，但是我们的想法是编写一个失败的测试。然后修改你的代码以通过测试。最后，您可以安心地重构代码，因为您已经进行了测试来检查您的重构没有破坏所有东西。

到目前为止，我们一直在进行同步测试。但是如果你需要测试回调或者承诺呢？

# 异步测试

测试异步代码并不困难，但是需要一段额外的代码，这段代码还没有介绍过，叫做`done()`。如果没有`done`，mocha 将分派您的异步代码并继续执行，而不等待您的回调的断言或执行。我用一个具体的例子给你看。

```
describe("Broken Async Code", function() {
    it('Async test', function() {
        setTimeout(function(){
            //failing test
            expect(true).to.be.false;
        }, 1000);
    });
});
```

以上测试通过。这是因为`it`只有在捕捉到来自`expect`的错误时才会测试失败。因为这是异步执行的(延迟 1 秒)，所以 mocha 跳过这一步作为通过测试。我们需要通过添加 done 来告诉 Mocha 等待。`done`必须作为论据加在断言之后。

```
describe("Fixed Async Code", function() {
    it('Async test', function(done) {     // Done added here.
        setTimeout(function(){
            //failing test
            expect(true).to.be.false;
            done();     // Tells mocha to run next test.
        }, 1000);
    });
});
```

你会注意到，当你运行`mocha test`时，它会暂停 1 秒，然后给出一个失败。遗憾的是，这并不是 mocha 在前面的同步示例中给出的真正详细的错误。这可以通过 mocha 的内置承诺来解决，但这是另一个话题，不在本文讨论范围之内。如果你感兴趣，我推荐[看看这个](http://tobyho.com/2015/12/16/mocha-with-promises/)(这是一个编写带有详细响应的异步测试的好方法)。

# 我使用的一些超级有用的功能

*   要运行单个测试或测试组，只需将`.only`添加到`describe`或`it`的末尾。示例:`describe.only("index.js tests", function () {`或`it.only("addTwoNumbers returns a number", function () {`
*   研究可在`describe`模块内使用的以下功能。`before()`、`beforeEach()`、`after()`、`afterEach()`。这些允许事情发生在你的每一次个人尝试之前和之后。(适合设置和清理)
*   浏览器里可以用摩卡！它加载了一个很酷的 web 页面，带有 todo 列表样式的格式，包含所有的测试。(这使得测试网页更加容易)。

祝你好运。记住测试是一种技能，所以熟能生巧。:)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！