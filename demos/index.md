# Basic Demo | 基础 Demo

This basic demo will guide you through using Spark to build and export an ML pipeline to an MLeap Bundle and later use it to transform a data frame using the MLeap Runtime.

基础 Demo 会引导你使用 Spark 来构建 ML Pipeline，导出 Pipeline 为 MLeap Bundle，以及随后在 MLeap Runtime 中使用它来转换 Data Frame。

## Build and Export an MLeap Bundle | 构建和导出 MLeap Bundle

In this section we will programmatically create a simple Spark ML pipeline then export it to an MLeap Bundle. Our pipeline is very simple, it performs string indexing on a categorical feature then runs the result through a binarizer to force the result to a 1 or 0. This pipeline has no real-world purpose, but illustrates how easy it is to create MLeap Bundles from Spark ML pipelines.

本章节我们会通过编码来创建一个简单的 Spark ML Pipeline，然后将其导出成 MLeap Bundle。我们的 Pipeline 非常简单，它在一个离散特征上进行字符串索引，然后使用一个二分器将结果转为 0 或 1。这个 Pipeline 没有实际的用途，但能够展示出从 Spark ML Pipeline 构建得到 MLeap Bundle 是多么容易。

```scala
import ml.combust.bundle.BundleFile
import ml.combust.mleap.spark.SparkSupport._
import org.apache.spark.ml.Pipeline
import org.apache.spark.ml.bundle.SparkBundleContext
import org.apache.spark.ml.feature.{Binarizer, StringIndexer}
import org.apache.spark.sql._
import org.apache.spark.sql.functions._
import resource._

  val datasetName = "./mleap-docs/assets/spark-demo.csv"

  val dataframe: DataFrame = spark.sqlContext.read.format("csv")
    .option("header", true)
    .load(datasetName)
    .withColumn("test_double", col("test_double").cast("double"))

  // User out-of-the-box Spark transformers like you normally would
  val stringIndexer = new StringIndexer().
    setInputCol("test_string").
    setOutputCol("test_index")

  val binarizer = new Binarizer().
    setThreshold(0.5).
    setInputCol("test_double").
    setOutputCol("test_bin")

  val pipelineEstimator = new Pipeline()
    .setStages(Array(stringIndexer, binarizer))

  val pipeline = pipelineEstimator.fit(dataframe)

  // then serialize pipeline
  val sbc = SparkBundleContext().withDataset(pipeline.transform(dataframe))
  for(bf <- managed(BundleFile("jar:file:/tmp/simple-spark-pipeline.zip"))) {
    pipeline.writeBundle.save(bf)(sbc).get
  }
```

Dataset used for training can be found [here](../assets/spark-demo.csv).

训练数据集可以从 [这里](../assets/spark-demo.csv) 获取。

NOTE: right click and "Save As...", Gitbook prevents directly clicking on the link.

注意：由于 GitBook 不允许用户直接点击链接下载，请右键另存为。

## Import an MLeap Bundle | 导入 MLeap Bundle

In this section we will load the MLeap Bundle from the first section into the MLeap Runtime. We will then use the MLeap Runtime transformer to transform a leap frame.

本节中我们会加载上一节生成的 MLeap Bundle 到 MLeap Runtime 中。我们将会使用 MLeap Runtime 来转换一帧 Leap Frame。

```scala
import ml.combust.bundle.BundleFile
import ml.combust.mleap.runtime.MleapSupport._
import resource._
// load the Spark pipeline we saved in the previous section
val bundle = (for(bundleFile <- managed(BundleFile("jar:file:/tmp/simple-spark-pipeline.zip"))) yield {
  bundleFile.loadMleapBundle().get
}).opt.get

// create a simple LeapFrame to transform
import ml.combust.mleap.runtime.frame.{DefaultLeapFrame, Row}
import ml.combust.mleap.core.types._

// MLeap makes extensive use of monadic types like Try
val schema = StructType(StructField("test_string", ScalarType.String),
  StructField("test_double", ScalarType.Double)).get
val data = Seq(Row("hello", 0.6), Row("MLeap", 0.2))
val frame = DefaultLeapFrame(schema, data)

// transform the dataframe using our pipeline
val mleapPipeline = bundle.root
val frame2 = mleapPipeline.transform(frame).get
val data2 = frame2.dataset

// get data from the transformed rows and make some assertions
assert(data2(0).getDouble(2) == 1.0) // string indexer output
assert(data2(0).getDouble(3) == 1.0) // binarizer output

// the second row
assert(data2(1).getDouble(2) == 2.0)
assert(data2(1).getDouble(3) == 0.0)
```

That's it! This is a very simple example. Most likely you will not be manually constructing Spark ML pipelines as we have done here, but rather you will be using estimators and pipelines together to train on your data and produce useful models. For a more advanced example, see our [MNIST Demo](../demos/minst.md).

搞定！这个例子非常简单。你很可能不会像我们那样手动去构建 Spark ML Pipeline，而是使用 Estimator 和 Pipeline 基于你的数据来训练得到有用的模型。更高级的例子，可以参见我们的 [MNIST Demo](../demos/minst.md) 章节。

