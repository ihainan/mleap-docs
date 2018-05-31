# TRANSFORMING A LEAP FRAME | 转换 Leap Frame

Transformers are a useful abstraction for computing data in a data frame, whether it is a leap frame in MLeap or a Spark data frame. Let's see how to transform a data frame using a simple `StringIndexer` transformer.

无论是 MLeap 还是 Spark，Transformer 对于 Data Frame 的计算都是一种非常有用的抽象模型。让我们看看如何使用一个简单的  Transformer `StringIndexer` 来转换一帧 Data Frame。

```scala
// Create a StringIndexer that knows how to index the two strings
// In our leap frame
val stringIndexer = StringIndexer(
  shape = NodeShape.scalar(inputCol = "a_string", outputCol = "a_string_index"),
  model = StringIndexerModel(Seq("Hello, MLeap!", "Another row")))

// Transform our leap frame using the StringIndexer transformer
val indices = (for(lf <- stringIndexer.transform(leapFrame);
                   lf2 <- lf.select("a_string_index")) yield {
  lf2.dataset.map(_.getDouble(0))
}).get.toSeq

// Make sure our indexer did its job
assert(indices == Seq(0.0, 1.0))
```

## TRANSFORMING A LEAP FRAME WITH A PIPELINE | 使用 Pipeline 来转换 Leap Frame

The above example isn't very interesting. The real power of data frames and transformers comes when you build entire pipelines out of them, going all the way from raw features to some sort of predictive algorithm. Let's create a dummy pipeline that takes our indices from the string indexer, runs them through a one hot encoder, then executes a linear regression.

上面的例子可能不是很有趣。当你使用 Leap Frame 和 Transformer 一起来构建一个包含从原始特征到某些预测算法在内的完整 Pipeline 时，它们的真正威力才开始体现。让我们构造一个 Pipeline，其先通过 String Indexer 来生成索引，并把索引传给 One Hot Encoder，而后执行线性回归算法。  

```scala
// Create our one hot encoder
val oneHotEncoder = OneHotEncoder(shape = NodeShape.vector(1, 2, 
                     inputCol = "a_string_index",
                     outputCol = "a_string_oh"),
  model = OneHotEncoderModel(2, dropLast = false))

// Assemble some features together for use
// By our linear regression
val featureAssembler = VectorAssembler(
     shape = NodeShape().withInput("input0", "a_string_oh").
     withInput("input1", "a_double").withStandardOutput("features"),
     model = VectorAssemblerModel(Seq(TensorShape(2), ScalarShape())))

// Create our linear regression
// It has two coefficients, as the one hot encoder
// Outputs vectors of size 2
val linearRegression = LinearRegression(shape = NodeShape.regression(3),
  model = LinearRegressionModel(Vectors.dense(2.0, 3.0, 6.0), 23.5))

// Create a pipeline from all of our transformers
val pipeline = Pipeline(
      shape = NodeShape(),
      model = PipelineModel(Seq(stringIndexer, oneHotEncoder, featureAssembler, linearRegression)))

// Transform our leap frame using the pipeline
val predictions = (for(lf <- pipeline.transform(leapFrame);
                       lf2 <- lf.select("prediction")) yield {
  lf2.dataset.map(_.getDouble(0))
}).get.toSeq

// Print our predictions
//   > 365.70000000000005
//   > 166.89999999999998
println(predictions.mkString("\n"))
```

This is the task that MLeap was meant for, executing machine learning pipelines that were trained in Spark, PySpark, Scikit-learn or Tensorflow.

这个任务体现了 MLeap 的意义在于执行我们通过 Spark、PySpark、Scikit-Learn 或者 Tensorflow 等机器学习框架训练得到的 Pipeline。  