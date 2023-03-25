# 我如何将我的 spark 文档分类(逻辑回归)模型部署为独立的实时预测应用程序

> 原文：<https://medium.com/hackernoon/how-i-deployed-my-spark-document-classification-logistic-regression-model-s-as-a-standalone-app-64b05b44e102>

TLDR —使用管道保存从训练集生成的 TF-IDF 模型，以及用于预测的 SVM 模型。所以本质上保存两个模型，一个用于特征提取和输入转换，另一个用于预测。

当你开发一个文本分类模型时，最大的挑战之一是，如果你的计划是离线训练，在某些情况下只部署模型进行预测，那么你得到的训练模型不足以进行预测。特别是在我们使用 [**【哈希技巧】**](http://en.wikipedia.org/wiki/Feature_hashing) 从训练集中提取特征，并使用**【逆文档频率】**将特征/术语对文档的重要性归一化的情况下，文档中最频繁出现的术语实际上对整个语料库的重要性较低。根据 spark 网站，这通常被标记为“[术语频率-逆文档频率(TF-IDF)](http://en.wikipedia.org/wiki/Tf%E2%80%93idf) 是一种广泛用于文本挖掘的特征矢量化方法，以反映术语对语料库中文档的重要性”

如果我们使用 TF-IDF 通过 spark 进行特征矢量化，我们通常会这样实现它

```
**import** **org.apache.spark.mllib.feature.**{**HashingTF**, **IDF**}
**import** **org.apache.spark.mllib.linalg.Vector**
**import** **org.apache.spark.rdd.RDD**

*// Load documents (one per line).*
**val** documents**:** RDD[Seq[String]] **=** sc.textFile("data/mllib/kmeans_data.txt")
  .map(**_**.split(" ").toSeq)

**val** hashingTF **=** **new** **HashingTF**()
**val** tf**:** RDD[Vector] **=** hashingTF.transform(documents)

*// While applying HashingTF only needs a single pass to the data, applying IDF needs two passes:*
*// First to compute the IDF vector and second to scale the term frequencies by IDF.*
tf.cache()
**val** idf **=** **new** **IDF**().fit(tf)
**val** tfidf**:** RDD[Vector] **=** idf.transform(tf)
```

因此，正如你所看到的，仅仅训练好的模型不足以进行独立的预测，因为我们必须从输入文档中提取特征，并归一化它们的词频，这一切都取决于训练集，我们不想将训练集包括在实时预测中(耗时，并增加应用程序的内存消耗)。

因此，从 spark 1.3 开始，引入了管道，我们可以使用管道自动化我们的提取、转换和预测工作流。从 spark 1.6 开始，我们能够保存包含所有工作流的管道模型。因此，如果我们想使用管道离线训练模型并预测某个地方，它们就是 goto 解决方案。因此，如果我们想使用逻辑回归来训练和预测，这就是我们可以做的(摘自[http://spark.apache.org/docs/latest/ml-pipeline.html](http://spark.apache.org/docs/latest/ml-pipeline.html))。

```
**import** **org.apache.spark.ml.**{**Pipeline**, **PipelineModel**}
**import** **org.apache.spark.ml.classification.LogisticRegression**
**import** **org.apache.spark.ml.feature.**{**HashingTF**, **Tokenizer**}
**import** **org.apache.spark.ml.linalg.Vector**
**import** **org.apache.spark.sql.Row**

*// Prepare training documents from a list of (id, text, label) tuples.*
**val** training **=** spark.createDataFrame(**Seq**(
  (0L, "a b c d e spark", 1.0),
  (1L, "b d", 0.0),
  (2L, "spark f g h", 1.0),
  (3L, "hadoop mapreduce", 0.0)
)).toDF("id", "text", "label")

*// Configure an ML pipeline, which consists of three stages: tokenizer, hashingTF, and lr.*
**val** tokenizer **=** **new** **Tokenizer**()
  .setInputCol("text")
  .setOutputCol("words")
**val** hashingTF **=** **new** **HashingTF**()
  .setNumFeatures(1000)
  .setInputCol(tokenizer.getOutputCol)
  .setOutputCol("features")
**val** lr **=** **new** **LogisticRegression**()
  .setMaxIter(10)
  .setRegParam(0.01)
**val** pipeline **=** **new** **Pipeline**()
  .setStages(**Array**(tokenizer, hashingTF, lr))

*// Fit the pipeline to training documents.*
**val** model **=** pipeline.fit(training)

*// Now we can optionally save the fitted pipeline to disk*
model.write.overwrite().save("/tmp/spark-logistic-regression-model")

*// We can also save this unfit pipeline to disk*
pipeline.write.overwrite().save("/tmp/unfit-lr-model")

*// And load it back in during production*
**val** sameModel **=** **PipelineModel**.load("/tmp/spark-logistic-regression-model")

*// Prepare test documents, which are unlabeled (id, text) tuples.*
**val** test **=** spark.createDataFrame(**Seq**(
  (4L, "spark i j k"),
  (5L, "l m n"),
  (6L, "mapreduce spark"),
  (7L, "apache hadoop")
)).toDF("id", "text")

*// Make predictions on test documents.*
model.transform(test)
  .select("id", "text", "probability", "prediction")
  .collect()
  .foreach { **case** **Row**(id**:** Long, text**:** String, prob**:** Vector, prediction**:** Double) **=>**
    println(s"($id, $text) --> prob=$prob, prediction=$prediction")
  }
```

这是完美的解决方案，但是并不是所有的逻辑回归算法都被支持，只有**逻辑回归**和**朴素贝叶斯**被支持。

所以，如果我们想用 SVM 的或者 T2 的，我们就没那么幸运了。为了使这些算法库支持流水线，它们必须实现一种称为“fit”的方法。更准确地说，管道基于转换器和评估器的概念工作，无论我们在管道工作流中放入什么，都必须是其中之一。我们的算法模型是估值器，因为它们训练或拟合数据。fit()方法接受 dataframe 并返回“pipelineModel”。SVM 不支持这种方法。

因此，我试图使现有的 SVM 成为一个估计器，但没有太大的成功，因为似乎完全缺乏关于如何创建我们自己的估计器和转换器的文档。我很乐意听到任何能够做到这一点的人的意见。

因此，经过一周的深思熟虑，我想到了一个办法，那就是使用管道。听起来令人困惑——我知道。我来细说一下。

如果我们将管道升级设置为仅生成 IDF 模型，会怎么样？。这将输出一个管道模型，该模型可以与经过训练的 SVM 模型一起保存。

因此，我没有只保存一个 SVM 模型用于预测，而是使用管道生成一个提取和转换模型，包括“标记、提取和转换”阶段，以生成一个 **pipelineModel** 并保存它。

下面是我用来保存 SVM 和管道模型的最终代码片段。

```
import org.apache.spark.ml.{Pipeline, PipelineModel}
import org.apache.spark.ml.feature.{HashingTF, Tokenizer}
val tokenizer = new Tokenizer()
 .setInputCol(“text”)
 .setOutputCol(“words”)
val hashingTF = new HashingTF()
 .setInputCol(tokenizer.getOutputCol)
 .setOutputCol(“rawFeatures”)
val idf = new IDF().setInputCol(“rawFeatures”).setOutputCol(“features”)val pipeline = new Pipeline()
 .setStages(Array(tokenizer, hashingTF, idf))
val pipelineModel = pipeline.fit(training_df)
pipelineModel.save(“somewhere”) # Saving the pipeline model
val t = pipelineModel.transform(training_df).select(“features”, “label”).map( row => LabeledPoint(
 row.getAs[Double](“label”), 
 row.getAs[org.apache.spark.mllib.linalg.Vector](“features”)
))val svm_model = new SVMWithSGD().run(t)
svm_model.clearThreshold()
svm_model.save(sc,”somewhere”) // Saving SVM model for prediction
```

对于没有训练数据的独立预测，加载两个模型，将输入文本转换为提取特征的数据帧，并将转换后的输入 DF 传递给 SVM 模型。

**注意** :-前两个代码片段引用自[http://spark.apache.org/docs/latest/ml-pipeline.html](http://spark.apache.org/docs/latest/ml-pipeline.html)

PS:-如果有的话，我想听听替代的解决方案和更正。谢谢

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请阅读我们的“关于”页面、[在脸书](http://bit.ly/HackernoonFB)上给我们点赞/发消息，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！