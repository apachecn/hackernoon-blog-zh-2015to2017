# 当您在同一个 MySQL 表中插入数十万个条目时，如何避免重复

> 原文：<https://medium.com/hackernoon/how-to-avoid-duplicates-when-you-insert-hundreds-of-thousands-of-entries-into-the-same-mysql-table-bf8a6e1e905>

[![](img/52bf9e84126f483254abfde8427668f0.png)](https://goo.gl/RDUt4v)

假设您正在开发一个应用程序，它要求一个表中的所有条目(姑且称之为`entries`)都是惟一的。
如果我们用 PHP/Laravel 编写应用程序，表的迁移(更准确地说是它的`up()`方法)将如下所示:

```
public function up()
    {
        Schema::create('entries', function (Blueprint $table) {
            $table->increments('id');
            $table->string('parameters_001')->nullable();
            $table->string('parameters_002')->nullable();
            $table->string('parameters_003')->nullable();
            $table->string('parameters_004')->nullable();
            $table->timestamps();
        });
    }
```

解决这个问题的一种方法(如果不是想到的最明显的方法)如下:

使用一个简单的检查，创建一个名为`isDuplicated($entry)`的方法来搜索表中的条目，如果不存在就插入，否则抛出一个异常。

这个方法可能看起来像这样(它应该被添加到`Entry`模型类中):

```
public static function isDuplicated($inputs)
    {
        $exists = self::where('parameter_001', $inputs['parameter_001'])
                       ->where('parameter_002', $inputs['parameter_002'])
                       ->where('parameter_003', $inputs['parameter_003'])
                       ->where('parameter_004', $inputs['parameter_004'])
                       ->count(); return $exists ? true : false;
    }
```

PS:我们可以在插入中添加一个监听器，并在使用`Entry::create()`方法时自动进行检查，但为了简单起见，我们不会在本教程中这样做。

这个解决方案工作得很好，对于大多数情况来说可能是一个好的解决方案，但是让我们假设您的应用程序很大，每天/每小时要插入数百万个条目。过一会儿，你肯定会注意到一些重复的条目。

这是怎么发生的？您可能会问，在插入之前，您已经在检查表格了。事实证明，当应用程序试图在完全相同的时刻两次插入相同的条目时，问题就出现了。因为应用程序同时通过`isDuplicated($entry)`方法检查表中是否已经存在条目，所以这两个操作都将返回 false，我们最终两次插入相同的条目。

现在想到的第一个解决方案如下:使用某种锁定机制(在 MySql 级别):

要么通过使用事务:

或者悲观锁定
[https://laravel.com/docs/5.5/queries#pessimistic-locking](https://laravel.com/docs/5.5/queries#pessimistic-locking)

在这种情况下，我们将确保不能两次插入同一行，对吗？

这似乎是一个非常好的解决方案，但是如果你想一想，当你试图插入一个新的条目时，首先没有要锁定的内容，我们不是在更新一个现有的行(在这种情况下，锁定可以很好地工作)，而是在插入一个全新的行。

因此，在这种情况下，锁机制将毫无价值。

您可能想到的另一个解决方案是为整个表添加一个索引，一个由所有字段组成的键。

这里出现了一个问题(记住，我们讨论的是成千上万的条目),我们可能会很快遇到一些性能问题，在整个表上建立一个索引会大大降低对它的操作速度。

即使不考虑性能问题，我们还有另一个问题。

假设我们试图插入这个条目两次:

```
['parameter_001'=> 'value_001',
'parameter_002'=> 'value_002',
'parameter_003'=> 'value_003',
'parameter_004'=> 'value_004'
];
```

不出所料，第一个将被插入，第二个将被拒绝(也不出所料),因为违反了我们使用的索引/键。

但是，如果我们尝试将以下条目插入两次:

```
['parameter_001'=> 'value_001',
'parameter_002'=> 'value_002',
'parameter_003'=> 'value_003'
];
```

它将被插入两次，没有任何问题。但是为什么会这样呢？

请记住，我们在这个表中字段是可空的，这意味着，当我们试图插入一个新条目时，我们的“全局”索引将防止所有子键都存在且已经插入的条目。但是如果省略了一个子键，他们的检查就会失败，我们就会得到一个重复的条目。

尽管这个解决方案并不正确，但它让我们接近了我们应该做的事情，从某种意义上说，我们应该让 MySql 来处理这个问题，而不是在代码中处理，我们可以使用相同的概念，而不会影响性能，也不会因为省略了“子键”而失败。

这个问题的解决方案是创建一个额外的行，表示条目的散列，我们称之为`hash`，这个散列将扮演唯一键的角色，所以当我们试图插入条目时，我们将它的散列添加到其中，并尝试插入，如果操作成功，即 MySql 没有抛出任何异常，那么条目确实是唯一的，否则，Mysql 将抛出一个异常，因为它不能插入具有相同唯一键的两行。

首先我们需要将这一行添加到条目表迁移的`up()`方法中:

```
$table→string('hash')→unique();
```

现在，在这种情况下，还有一件事我们应该注意，我们需要确保散列是唯一的，并且没有共谋。换句话说，我们不能使用像 md5 这样的单向散列法，因为我们最终会得到两个具有相同密钥的条目，但是像 base64(或者甚至连接所有字段)这样的方法也可以

PS:不要忘记将`fillable`属性添加到模型类中

`protected $fillable = ['parameter_001', 'parameter_002', 'parameter_003', 'parameter_004', $hash];`

当我们创建散列时，我们应该明确考虑的另一件事是，在我们生成散列之前，在字段之间添加一个分隔符，换句话说，当我们尝试插入以下条目时:

```
['parameter_001'=> 'value_001',
'parameter_002'=> 'value_002',
'parameter_003'=> 'value_003'
];
```

我们应该为类似`value_001-value_002-value_003`的东西生成散列

PS:这里不要使用简单的`implode()`方法，因为这会忽略条目中不存在的字段。

这背后的原因是，如果我们不添加任何分隔符，当我们有一些丢失的参数时，我们会有假阳性，并且我们对不同的字段使用相同的值。
例如，以下条目不是重复的(即插入没有任何问题),但 MySql 会拒绝它们:

```
['parameter_001'=> 'value_001',
'parameter_002'=> 'value_002',
 'parameter_003'=> 'value_003'
];
```

和

```
['parameter_001'=> 'value_001',
'parameter_002'=> 'value_002',
'parameter_004'=> 'value_003',
];
```

因为如果我们在没有任何分隔符的情况下连接，我们将在两种情况下以`value_001value_002value_003`的散列结束，而当我们添加分隔符时，我们将为第一个条目生成`value_001-value_002-value_003`的散列，为第二个条目生成`value_001value_002--value_003`(注意双`-`符号)，这将生成与第一个不同的散列。

总而言之，每次我们想要插入一个新条目时，我们都会为这个条目生成一个唯一的散列，我们将这个散列和这个条目一起插入，由于这个散列上有一个索引，所以重复的条目不会被插入，Mysql 会抛出一个异常。

# mysql 可能抛出的另一个与重复数据删除无关的异常呢？

如果我们想要捕获与这种重复机制相关的异常(我们可能想要在某个地方记录它，或者在发生这种情况时执行一些逻辑)，那么捕获所有异常并不是一个好的解决方案，因为 MySQL 可能会因为完全不同的原因而抛出异常，而不是重复异常。

为了解决这个问题，我们需要分析抛出的异常，并相应地执行正确的逻辑。
如果你看一下关于异常的 Mysql 文档，你可以找到关于我们想要处理的复制异常的信息:
[https://dev . Mysql . com/doc/ref man/5.7/en/error-messages-server . html # error _ er _ dup _ entry](https://dev.mysql.com/doc/refman/5.7/en/error-messages-server.html#error_er_dup_entry)

如您所见，当我们试图插入一个重复的条目时，MySQL 抛出一个异常，该异常具有 SQL 状态`23000`和错误代码`1062`，因此我们可以编写一个方法来验证这一点:

```
private function isDuplicateEntryException(QueryException $e)
  { $sqlState = $e->errorInfo[0];
      $errorCode  = $e->errorInfo[1];
      if ($sqlState === "23000" && $errorCode === 1062) { return true;
      } return false;
  }
```

然后我们可以在代码中添加这种检查，如下所示:

```
try{
// insert the entry
 }
    catch (QueryException $e) {
           if ($this->isDuplicateEntryException($e)) {
            throw new DuplicateEntryException('Duplicate Entry');
           } throw $e; }
```

本文首次发表于此:[当你在同一个 MySQL 表中插入数十万个条目时如何避免重复](http://youghourta.com/2017/11/21/avoid-duplicates-insert-hundreds-thousands-entries-mysql-table/)

[![](img/4c66888dbc35e2b0486de57a3abf0178.png)](https://goo.gl/1ohA17)