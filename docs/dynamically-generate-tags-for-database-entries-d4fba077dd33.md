# 为数据库条目动态生成标签

> 原文：<https://medium.com/hackernoon/dynamically-generate-tags-for-database-entries-d4fba077dd33>

![](img/378475da8227505ad15c317f1f7d335c.png)

基于我最近在博客上收到的一个问题，我决定写这篇文章。让我简单解释一下这个想法的背景和上下文。我最近创建了一个网站，在后端保存了许多唐纳德·特朗普的演讲。在前端，用户可以搜索任何单词，以查看该单词是否包含在任何演讲中。如果有匹配的，它会给你统计特朗普最经常谈论用户搜索的任何内容的方式、时间和地点。如果你感兴趣的话，这里有一个这个网站的例子:[https://trumpspeechdata.herokuapp.com/](https://trumpspeechdata.herokuapp.com/)

在最近的一篇文章中，我提到，如果你有一个类似的项目，你可能会试图让用户搜索数据库，以及如何经常出现这样的问题，搜索结果与用户预期的结果不匹配。有一种方法可以帮助缓解这种情况，那就是使用单词的映射对象，它将相似的单词和搜索分组到一个关键字伞中。基本上，如果用户的搜索匹配映射对象中的任何单词，它将被替换为“雨伞”单词，以便返回的搜索结果更接近匹配主题，而不仅仅是用户搜索的特定单词。想了解更多信息，这里有一个帖子:[https://hacker noon . com/creating-more-accurate-search-results-for-small-sites-436 e 64 da 79 b 6](https://hackernoon.com/creating-more-accurate-search-results-for-small-sites-436e64da79b6)

所以今天的问题是:如果你的数据库结构看起来像这样:

```
{
   "speechtitle": "Remarks by President Trump on TaxReform",
   "speechdate": "September 2017",
   "speechlocation": "Missouri",
   "text": "blah, blah, blah, text here",
}
```

还没有标签字段。如何动态地向数据库添加标签？首先，您可以手动完成，方法是阅读每篇演讲，并手动将字段和标签添加到每篇演讲中。但是，让我们面对它，这在大多数情况下是不可行的。我们想要做的是向每个数据库条目添加最相关的标签，而不必手动完成。我想到了一个解决办法。为了一窥我在这篇文章中所做的工作，你可以点击这里，并参考它，看看它如何与我正在编写的代码相匹配:

【https://trumpspeechdata.herokuapp.com/taginput 

让我先承认，我确信使用 Python 或 PHP 可能会有更好的解决方案。我的专长是 Node。JS 和 JavaScript，所以这就是我的解决方案，如果你想跟进的话。我认为古老的格言是正确的，“如果你只有一把锤子，一切看起来都像钉子。”不管怎样，我们开始吧。这是我对此的思考过程:

# 步骤 1:从语音文本中提取标签

我需要整理每篇演讲的文本，找到最常用的词。哪些单词出现得最频繁，我将提取它们作为该数据库条目的标签。然而，如果我只是这样做，我会发现“和”、“the”、“it”和“if”是最常用的词。因此，我创建了一个可能主题的数组来循环遍历并匹配演讲文本…这样，我就不会得到很多“非主题”文本，并且我的结果更相关。以下是我使用的标签，我将它们存储在一个数组中:

```
let tags = [ "women's rights", "women", "deport", "border", "security", "immigration", "extremism", "terrorism", "tuition", "healthcare", "tax reform", "taxes", "citizenship", "abortion", "religion", "lgbt", "gay rights", "transgender", "military", "marriage", "gun control", "surveillance", "net neutrality", "drugs", "social security", "obamacare", "medicaid", "israel", "military spending", "north korea", "isis", "equal rights", "minimum wage", "welfare", "student loans", "education", "climate change", "transportation", "loans", "student", "school", "teacher", "jobs", "salary"];
```

接下来，我将创建一个函数，将这些单词与每个演讲中的所有单词进行匹配。为此我将使用 for 循环。在使用 fetch 并将数据存储在一个名为“api”的变量中之后，我就可以开始了。

```
response.json().then(function(data) {let api = data;

            let tagsOutput = [];
            for (var i = 0; i < api.length; i++) {
              let tags = [ "women's rights", "women", "deport", "border", "security", "immigration", "extremism", "terrorism", "tuition", "healthcare", "tax reform", "taxes", "citizenship", "abortion", "religion", "lgbt", "gay rights", "transgender", "military", "marriage", "gun control", "surveillance", "net neutrality", "drugs", "social security", "obamacare", "medicaid", "israel", "military spending", "north korea", "isis", "equal rights", "minimum wage", "welfare", "student loans", "education", "climate change", "transportation", "loans", "student", "school", "teacher", "jobs", "salary"];

              if(api[i].text.length > 1) {
                let index = api.indexOf(api[i]);
                let stringX = api[i].text.split(" ");
                for (var j = 0; j < tags.length; j++) {
                  for (var k = 0; k < stringX.length; k++) {
                    if (tags[j] == stringX[k]) {
                      tagsOutput.push([
                        index,
                        tags[j],
                        api[i].title,
                        api[i].location,
                        api[i].date,
                        api[i].text,
                    ])
                   }
                 }
               }
             }
```

我将“标签”数组放在 for 循环中。然后，我创建一个 if 语句，确保文本在那里。如果是，我执行一个嵌套的 for 循环，首先将原始文本拆分成单词，并将其存储在变量 stringX 中。然后我循环遍历我的标签，并在其中嵌套第二个 for 循环，在这里我循环遍历 StringX，然后如果文本中的单词和标签中的单词匹配，我将几个东西放入一个名为“tagsOutput”的新数组中。下面是我将什么放入该阵列，以及原因:

1.  index —我已经在这个变量中存储了 api[i]的索引。因为我将创建一个新的数组，如果我以后需要访问原始 api 中的数据，我可以通过匹配索引号，轻松地将新数组中的项与 api 中的项进行匹配。
2.  标签—这些是我将提取并最终发布到我的数据库中的标签。
3.  标题——我想把标题推过来，这样以后当我找到最常用的词时，我就可以把它们和具体的演讲联系起来。
4.  地点、日期和文本——我不一定需要所有这些东西。因为我有索引，所以如果以后需要的话，我可以从 api 数组中获取这些项目。然而，我经常发现，如果可能的话，将我需要的所有东西放在一个数组中会更容易。就像去露营一样。我可能用不到我的雨衣、4 个手电筒和 8 套换洗的内衣，但是如果我以后需要的话，拥有这些东西是很好的。

# 步骤 2:按频率对标签进行排序

接下来，我需要统计某些词在某些演讲中出现的频率。我将使用一个函数来计算项目频率，去掉重复的条目，并返回项目和频率。我将把磁贴和标签都交给函数。这样，如果演讲的标题是“亚利桑那州的集会”，标签“战争”在演讲中出现了 6 次，“妇女”出现了 8 次，我应该会得到如下结果:

```
{women, rally in Arizona, 8}
{war, rally in Arizona, 6}
```

这样，我不仅得到了标签的频率，或讲话的频率…而且把它们结合起来，得到了那个标签在讲话中的频率。下面是这个函数的样子:

```
Array.prototype.byCount= function(){
            var itm, a= [], L= this.length, o= {};
            for(var i= 0; i<L; i++){
              itm= this[i];
              if(!itm) continue;
              if(o[itm]== undefined) o[itm]= 1;
              else ++o[itm];
            }
            for(var p in o) a[a.length]= {item: p, frequency: o[p]};
            return a.sort(function(a, b){
              return o[b.item]-o[a.item];
            });
          }let tagFreq = tagsOutput.byCount();
```

# 步骤 3:细化标签

现在我要以很多频率为 1，2 或 3 的标签结束。我觉得，在一个 45 分钟的演讲中，如果“预算”只出现两次，那么它与演讲的相关性不足以作为一个标签。所以我要创建一个新的数组，遍历“tagFreq ”,只添加频率高于 6 的对象。这应该确保这些标签在语音中足够频繁地出现，以被添加为相关标签。

```
let arrayOfTags = [];
for (var i = 0; i < tagFreq.length; i++) {
  if (tagFreq[i].frequency > 6) {
    arrayOfTags.push([
      tagFreq[i].item
    ])
  }
}
```

下一部分完全没有必要，但我会把它包括进来。事后我意识到，这样做将返回一个巨大的字符串，后跟频率。我需要将该字符串的每个部分分隔到一个数组中，这样标题、日期、位置和文本都有自己的数组索引。有更好的方法来做到这一点。但是我使用了一系列逗号分隔的字符串来提取特定的元素。

# 步骤 4:重构数组中的数据

```
let matchedTags = [];
for (var i = 0; i < arrayOfTags.length; i++) {let index = arrayOfTags[i][0].split(',', 1)[0];
  let remainingString1 = arrayOfTags[i][0].split(/,(.+)/)[1];
  let tag = remainingString1.split(',', 1)[0];
  let remainingString2 = remainingString1.split(/,(.+)/)[1];
  let title = remainingString2.split(',', 1)[0];
  let remainingString3 = remainingString2.split(/,(.+)/)[1];
  let location = remainingString3.split(',', 1)[0];
  let remainingString4 = remainingString3.split(/,(.+)/)[1];
  let date = remainingString4.split(',', 1)[0];
  let text = remainingString4.split(/,(.+)/)[1];

  matchedTags.push([
    index,
    tag,
    title,
    location,
    date,
    text
  ])
}
```

我害怕演讲文本中的逗号太多，我害怕 JavaScript 会不小心不正确地将其拆分。所以在这里，我所做的基本上是，在第一个逗号处分割字符串，这给了我“索引”我分别保存“index”之后的所有内容，然后做同样的事情，拆分它，并在下一个逗号处获取标记，冲洗并重复。

# 步骤 5:在节点中设置后端。射流研究…

“匹配标签”数组或多或少地给了我想要的数据结构。现在到了棘手的部分。我要在这里换挡，进入后端。带节点。JS 我正在使用 MongoDB 来创建一个基于文档的数据库。我不是简单地在我的数据中添加一个“tags”字段，而是实际上返回添加两个字段。在这篇文章中，我不打算介绍在 Node 中设置后端的整个过程，而只是解释一下我在做什么。

最初，我使用 Mongoose 为我的数据创建模型。我只有一个模式，“演讲”。现在，我将返回并添加第二个名为“speechID”的模型。该模型的结构如下所示:

```
let Schema = mongoose.Schema;const speechIDSchema = new Schema({speechID: {
    type: String,
  },})
speechIDSchema.plugin(timestamps);
const SpeechID = mongoose.model('speechID', speechIDSchema);module.exports = SpeechID;
```

在我的第二个模型中，我引用了第一个模型。这类似于在关系数据库中创建表关联。这里，我的语音文档将与我的 speechID 文档相关联。我这样做的原因是，稍后，我将需要在我的演讲中添加我的标签。通过在我的“speech”模型中包含一个语音 ID，我可以在我的 post 方法中使用它来确保我添加的标签被添加到正确的语音中。下面是我的语音模型的样子:

```
let Schema = mongoose.Schema;const speechSchema = new Schema({

  speechID: {
  type: String,
  ref: 'SpeechID',
  },
  title: {
    type: String,
  },
  date: {
    type: String,
  },
  location: {
    type: String,
  },
  text: {
    type: String,
  },
  tags: [{
    type: String,
  }],})
speechSchema.plugin(timestamps);
const Speech = mongoose.model('speech', speechSchema);module.exports = Speech;
```

正如您在这里看到的，每个语音都将获得一个 speechID，但我不需要输入它，它只是从 speechID 模型中获得 ID。在我的 app.js 中，下面是我如何构建我的 app.post 来适应这种变化:

```
//====APP POST SPEECH===//app.post('/speechnew/:speechID', function(req, res) {
  Speech.create({
  speechID: req.params.speechID,
  title: req.body.title,
  date: req.body.date,
  location: req.body.location,
  text: req.body.text,
  tags: req.body.tags,
}).then(speechs => {
  res.json(speechs)
});
});//==========================//
```

app.post 操作将是/speechnew/:speechID。然而，我将在创建表单时给它实际的 speechID，再次确保这样标记更新正确的文档。您可能会注意到这是“create”方法，但是当我添加标签时，我将使用“findOneAndUpdate”方法。猫鼬里也差不多，基本上就改了那一个字。下面是帖子请求外观:

```
//====APP UPDATE SPEECH===//app.post('/speechupdate/:speechID', function(req, res) {
  Speech.findOneAndUpdate({
  speechID: req.params.speechID,
  title: req.body.title,
  date: req.body.date,
  location: req.body.location,
  text: req.body.text,
  tags: req.body.tags,
}).then(speechs => {
  res.json(speechs)
});
});//==========================//
```

# 第 6 步:动态创建一个表单，将反馈后端。

同样，我不会在这里深入讨论 Node.js 的内容。我需要做的就是确保我的表单动作与这里的路径匹配，并且我的表单输入名称与这里的 req.body 名称匹配。好了，让我们回到前端的 JavaScript。我放弃了“matchedTags”数组，它保存了我想要的数据结构。我将一步一步地分解下一部分。基本上，接下来要做的是，我要循环遍历我的数组，为每个数组项创建一个表单和表单输入。我将为我创建的每个元素动态地设置唯一的 ID，通过创建变量来用作元素 ID。那我就要用。值，将这些表单输入的值设置为数组中的数据。我的表单操作将是我在后端创建的更新路由，所以当我更新时唯一会改变的应该是“标签”。

```
for (var i = 0; i < api.length; i++) {
let speechID = api[i].speechID;
```

我再次遍历 api，并将每个数组元素的 ID 保存在一个变量中。

```
for (var k = 0; k < matchedTags.length; k++) {
    let formID = "form"+matchedTags[k][0]+"";
    let inputID1 = "input1"+matchedTags[k][0]+"";
    let inputID2 = "input2"+matchedTags[k][0]+"";
    let inputID3 = "input3"+matchedTags[k][0]+"";
    let inputID4 = "input4"+matchedTags[k][0]+"";
    let inputID5 = "input5"+matchedTags[k][0]+"";
```

在我最初的 for 循环中，我开始了第二个 for 循环，在这里我循环了我的 matchedTags 数组。我创建的每个元素都需要有一个唯一的 ID。因为我不知道我的数组会有多大，所以我必须让我的 for 循环来决定。我唯一知道的是，将有一个表格，和 5 个输入字段“标题”，“日期”，“地点”，“文本”，和“标签”。每个元素基于 speechID 获得一个惟一的 ID，这个 ID 包含在“matchedTags[k][0]”中。

```
if(matchedTags[k][0] == api.indexOf(api[i])) {
```

接下来，通过一个 if 语句，我要确保我之前提取的并存储在 matchedTags[k][0]中的 IndexOf 与 api 数组的 index 相匹配。如果有匹配，我将启动一个新函数，创建我需要的所有表单元素，并使用我之前创建的那些 id。

```
let newForm = document.createElement('form');newForm.id = formID;
              document.body.appendChild(newForm);
              document.getElementById('data').appendChild(newForm);
              newForm.action = "/speechupdate/"+speechID+"";
              newForm.method = "post";
```

这是我的新表单，再次使用 formID 变量。在 newForm.action 中，我使用我在后端创建的路由/speechupdate/:speechID，只是在这里，speechID 是一个包含语音 ID 的变量。这就是将表单连接到正确的数据库条目的原因。接下来，我将创建一些输入并将它们追加到表单中，就像我将表单追加到 HTML 主体中的 and 元素一样:

```
let newInput1 = document.createElement('input');
          newInput1.id = inputID1;
          document.body.appendChild(newInput1);
          document.getElementById(formID).appendChild(newInput1);
          newInput1.name = "title";
          newInput1.value = matchedTags[k][2];

          let newInput2 = document.createElement('input');
          newInput2.id = inputID2;
          document.body.appendChild(newInput2);
          document.getElementById(formID).appendChild(newInput2);
          newInput2.name = "date";
          newInput2.value = matchedTags[k][4];

          let newInput3 = document.createElement('input');
          newInput3.id = inputID3;
          document.body.appendChild(newInput3);
          document.getElementById(formID).appendChild(newInput3);
          newInput3.name = "location";
          newInput3.value = matchedTags[k][3];

          let newInput4 = document.createElement('input');
          newInput4.id = inputID4;
          document.body.appendChild(newInput4);
          document.getElementById(formID).appendChild(newInput4);
          newInput4.name = "text";
          newInput4.value = matchedTags[k][5];

          let newInput5 = document.createElement('input');
          newInput5.id = inputID5;
          document.body.appendChild(newInput5);
          document.getElementById(formID).appendChild(newInput5);
          newInput5.name = "tags";
          newInput5.value = matchedTags[k][1];

          let submitButton = document.createElement('input');
          submitButton.id = "submitbtn";
          document.body.appendChild(submitButton);
          document.getElementById(formID).appendChild(submitButton);
          submitButton.type = "submit";
```

每个表单输入都被附加到表单中，并有自己唯一的 ID。input.name 也匹配我们在后端使用的 req.body 名称。下面是整个函数的整体外观:

```
for (var i = 0; i < api.length; i++) {
  let speechID = api[i].speechID;
  for (var k = 0; k < matchedTags.length; k++) {
    let formID = "form"+matchedTags[k][0]+"";
    let inputID1 = "input1"+matchedTags[k][0]+"";
    let inputID2 = "input2"+matchedTags[k][0]+"";
    let inputID3 = "input3"+matchedTags[k][0]+"";
    let inputID4 = "input4"+matchedTags[k][0]+"";
    let inputID5 = "input5"+matchedTags[k][0]+"";
    if(matchedTags[k][0] == api.indexOf(api[i])) {

      let newForm = document.createElement('form');newForm.id = formID;
              document.body.appendChild(newForm);
              document.getElementById('data').appendChild(newForm);
              newForm.action = "/speechupdate/"+speechID+"";
              newForm.method = "post";

          let newInput1 = document.createElement('input');
          newInput1.id = inputID1;
          document.body.appendChild(newInput1);
          document.getElementById(formID).appendChild(newInput1);
          newInput1.name = "title";
          newInput1.value = matchedTags[k][2];

          let newInput2 = document.createElement('input');
          newInput2.id = inputID2;
          document.body.appendChild(newInput2);
          document.getElementById(formID).appendChild(newInput2);
          newInput2.name = "date";
          newInput2.value = matchedTags[k][4];

          let newInput3 = document.createElement('input');
          newInput3.id = inputID3;
          document.body.appendChild(newInput3);
          document.getElementById(formID).appendChild(newInput3);
          newInput3.name = "location";
          newInput3.value = matchedTags[k][3];

          let newInput4 = document.createElement('input');
          newInput4.id = inputID4;
          document.body.appendChild(newInput4);
          document.getElementById(formID).appendChild(newInput4);
          newInput4.name = "text";
          newInput4.value = matchedTags[k][5];

          let newInput5 = document.createElement('input');
          newInput5.id = inputID5;
          document.body.appendChild(newInput5);
          document.getElementById(formID).appendChild(newInput5);
          newInput5.name = "tags";
          newInput5.value = matchedTags[k][1];

          let submitButton = document.createElement('input');
          submitButton.id = "submitbtn";
          document.body.appendChild(submitButton);
          document.getElementById(formID).appendChild(submitButton);
          submitButton.type = "submit";

              console.log(newInput3.value);}
  }
}
```

现在，这个解决方案是可行的，但它可能更干净，而且在其他地方可能有更好的解决方案。但是如果你像我一样，真的喜欢 node.js 和 JavaScript，如果你需要用数据库内容生成的标签动态地更新数据库，这个解决方案应该是可行的。如果您有任何问题或反馈，请联系我们。谢谢！