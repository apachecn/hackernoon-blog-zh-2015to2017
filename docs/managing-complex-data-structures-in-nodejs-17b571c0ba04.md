# 在 NodeJS 中管理复杂的数据结构。

> 原文：<https://medium.com/hackernoon/managing-complex-data-structures-in-nodejs-17b571c0ba04>

当开始一个新项目时，如何正确组织数据的问题通常是首先要解决的问题之一。假设需要存储用户的数据。使用 Mongo 并将数据存储为集合，我们有几个选择。例如，假设我们需要存储每个用户的以下数据:

## 用户信息:

1.  使用者辩证码
2.  西方人名的第一个字
3.  姓
4.  现地址
5.  电子邮件地址
6.  住宅电话
7.  工作电话
8.  手机
9.  如果电话号码已经被验证

处理这些数据的一种方法是为列表中的每一项创建一个单独的模式。然后，我们可以做一个模式引用来引用其他模式中的数据。看起来大概是这样的:

```
const mongoose = require('mongoose');let Schema = mongoose.Schema;const userIDSchema = new Schema({UserID: {
    type: mongoose.Schema.Types.Mixed,
  },
  })const UserID = mongoose.model('UserID', userIDSchema);module.exports = UserID;
```

然后，在我们的用户名字模式中，我们将这样做:

```
const mongoose = require('mongoose');let Schema = mongoose.Schema;const FirstNameSchema = new Schema({UserID: {
    type: mongoose.Schema.Types.Mixed, ref: 'UserId', 
},First_Name: { type: String,},
  })const FirstName = mongoose.model('Firstname', FirstNameSchema);module.exports = FirstName;
```

然后，对于列表中的每个后续项目，我们会做一些类似的事情。这样，每个模式都是独立的，但是可以访问其他数据。然而，当创建这么多不同的模型和路线时，这会变得很麻烦。当决定如何在后端组织数据时，这通常是主要的考虑因素之一。您预计数据将如何增长，当前的数据组织是否允许将来轻松扩展和操作数据？就我们的用户数据而言，我们当前的模型可能不是最好的。或者，我们可以将所需的数据嵌套在模型的对象和数组中，而不是在模式之间引用数据。让我们像这样重新组织我们的数据:

```
const User = mongoose.model('Story', userSchema);module.exports = User;const mongoose = require('mongoose');let Schema = mongoose.Schema;const userSchema = new Schema({UserID: {
    type: mongoose.Schema.Types.Mixed,
  },
  User_Info: {
    First_Name: {
      type: String,
    },
    Last_Name: {
      type: String,
    },
    Current_Address: {
      type: String,
    },
    Email_Address: {
      type: String,
    },
  },
      Phone_Numbers: [{
        Home_Phone: {
          type: Number,
        },
        Work_Phone: {
          type: Number,
        },
        Cell_Phone: {
          type: Number,
        },
            Phone_verified: [{
              Home: Boolean, Work: Boolean, Cell: Boolean,
            }],
      }],})const User = mongoose.model('User', userSchema);module.exports = User;
```

这里，我们将所有用户信息存储在一个对象中。然后，我们的三个电话号码作为对象存储在一个名为“Phone_Numbers”的数组中。在“Phone_Numbers”数组中，我们有一个名为“Phone-Verified”的子文档，它是一个对象数组，指示这些电话号码是否已经过验证。作为 mongoose 中的一个模型，很容易看出这些数据是如何组织和嵌套的。然而，正确发布数据可能会更加困难。如果我们将这些数据提交给一个 API，我们的响应将是 JSON 格式的，我们的 API 调用可能如下所示:

```
app.post('/api/user', function(req, res) {
  User.create({
    UserID: req.body.userid,
    User_Info: req.body.userinfo,
    First_Name: req.body.firstname,
    Last_Name: req.body.lastname,
    Current_Address: req.body.currentaddress,
    Email_Address: req.body.emailaddress,
    Phone_Numbers: req.body.phonenumbers,
    Home_Phone: req.body.homephone,
    Work_Phone: req.body.workphone,
    Cell_Phone: req.body.cellphone,
    Phone_Verified:
    req.body.phoneverified,
    Home: req.body.home,
    Work: req.body.work,
    Cell: req.body.cell,
  }).then(user => {
    res.json(user)
  });
});
```

您可能已经注意到，在我们的 app.post 中，我们有“Phone_Numbers:”和“Phone_Verified”以及“User_Info”字段，尽管我们模型中的这些字段只是不包含实际数据的数组的名称。但是，有必要在我们的 app.post 中包含这一点。否则，帖子将不起作用。

现在，如果我们要创建应用程序的前端，我们需要在 app.js 文件中包含这个依赖项:

```
const bodyParser = require('body-parser');app.use(bodyParser.urlencoded({
  extended: true
}));
```

当我们调用 app.use 时，确保我们将它设置为“extended: true ”,这将允许我们从前端表单向数据库中的嵌套数据发送数据。这就把我们带到了谜题的最后一部分，前端表单。让我们首先处理我们的 UserID 字段。这是最简单的，因为它不是嵌套的。无论您使用 react、mustache 还是其他什么，我们输入的命名都是一样的。它可能看起来像这样:

```
<input autocomplete="off" class="input" required type="text" name="userid"></input>
```

我们需要做的就是确保“name”与我们在 app.js 文件中使用的“req.body.userid”相匹配。现在，我们如何发布到用户信息的嵌套对象？我们会做如下的事情:

```
<input autocomplete="off" class="input" required type="text" name="userinfo[firstname]"></input>
```

因为我们使用的是主体解析器，并且我们将它设置为 urlencoded extended: true，所以主体解析器将把这个名称字段解释为嵌套对象。我们将对嵌套对象中的“姓氏”和其他字段做同样的事情，如下所示:

```
<input autocomplete="off" class="input" required type="text" name="userinfo[lastname]"></input>
```

现在更复杂的是我们的嵌套数组。我们该怎么发布呢？嗯，我们会做一些非常类似的事情，这将再次被我们的身体解析器解释。假设只有一个用户。并且该用户只有一部住宅电话、一部手机和一部工作电话。在这种情况下，我们的表单输入将如下所示:

```
<input autocomplete="off" class="input" required type="text" name="phonenumbers[homephone]"></input><input autocomplete="off" class="input" required type="text" name="phonenumbers[workphone]"></input>
```

但是，如果在我们的数据结构中，每个文档中有多个子用户，或者多个家庭，或者需要为其添加电话的家庭成员，但是仍然将它们嵌套在用户中，那会怎么样呢？在这种情况下，您可以轻松地添加一组电话号码。在我们的前端，输入会有轻微的变化。

```
<input autocomplete="off" class="input" required type="text" name="phonenumbers[0][homephone]"></input>
```

那么我们的第二个输入应该是这样的:

```
<input autocomplete="off" class="input" required type="text" name="phonenumbers[1][homephone]"></input>
```

主体解析器将其解释为数组的索引，并将它们作为电话号码添加到用户集合中的电话号码数组中。现在，如您所见，我们的验证电话号码字段嵌套在电话号码中。这看起来和我们在这里做的非常相似。它可能看起来像这样:

```
<input autocomplete="off" class="input" required type="text" name="phonenumbers[1][phoneverified][0]"></input>
```

如你所见，我们在这里发布深层嵌套数据，并可以继续这种格式，甚至更深层的嵌套数据。尽管有其他方法可以做到这一点，但是这个解决方案是优雅的、简单的，并且有望节省一些时间。顺便说一下，这种格式也适用于 Postman，如果你以这种方式发布数据的话。

希望这有所帮助，如果您有任何反馈或意见，请随时联系我们。谢谢！