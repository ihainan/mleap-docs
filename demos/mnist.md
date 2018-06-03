# MNIST Demo

本教程会向你展示如何使用 MLeap 和 Bundle.ML 组件来导出一个 Spark ML Pipeline，并在完全不依赖 Spark Context 的前提下，使用 MLeap 来转换新数据。

我们会构建一个基于 MNIST 数据集训练，包含一个 Vector Assembler、一个 Binarizer、一个 PCA 以及一个 Random Forest Model，用于手写图像分类的 Pipeline。这个练习的目的不是为了训练得到一个最优模型，而是演示在 Spark 中训练一个 Pipeline 然后在 Spark 之外部署这个 Pipeline（数据处理 + 算法）是多么得简单。

本教程的代码分为两个部分：

* Spark ML Pipeline 代码：原生 Spark 代码，用于训练 ML Pipeline，而后把它序列化成 Bundle.ML。
* MLeap 代码：加载一个序列化后的 Bundle 到 MLeap，然后用其转换 Leap Frame。

开始之前，我们先来了解一些术语：

#### 名词

* Estimator：真正意义上的机器学习算法，基于 Data Frame 训练 Transformer 并产生一个模型。
* 模型：在 Spark 里面，模型是代码和元数据，它基于训练过的算法对新数据进行评分。
* Transformer：任何用于转换 Data Frame 的都被叫做 Transformer，对于训练一个 Estimator 来说 Transformer 不是必须的（例如一个 Binarizer）。
* LeapFrame：一种 Data Frame 的数据结构，用于存储数据以及相关联的 Schema。

### 训练一个 Spark Pipeline

#### 加载数据
```scala
// Note that we are taking advantage of com.databricks:spark-csv package to load the data
import org.apache.spark.ml.feature.{VectorAssembler,StringIndexer,IndexToString, Binarizer}
import org.apache.spark.ml.classification.{RandomForestClassificationModel, RandomForestClassifier}
import org.apache.spark.ml.evaluation.{MulticlassClassificationEvaluator}
import org.apache.spark.ml.{Pipeline,PipelineModel}  
import org.apache.spark.ml.feature.PCA

// MLeap/Bundle.ML Serialization Libraries
import ml.combust.mleap.spark.SparkSupport._
import resource._
import ml.combust.bundle.BundleFile
import org.apache.spark.ml.bundle.SparkBundleContext

val datasetPath = "./mleap-demo/data/mnist/mnist_train.csv"
var dataset = spark.sqlContext.read.format("com.databricks.spark.csv").
                 option("header", "true").
                 option("inferSchema", "true").
                 load(datasetPath)

val testDatasetPath = "./mleap-demo/data/mnist/mnist_test.csv"
var test = spark.sqlContext.read.format("com.databricks.spark.csv").
                 option("inferSchema", "true").
                 option("header", "true").
                 load(testDatasetPath)
```

你可以下载[训练](https://s3-us-west-2.amazonaws.com/mleap-demo/mnist/mnist_train.csv.gz)和[测试](https://s3-us-west-2.amazonaws.com/mleap-demo/mnist/mnist_test.csv.gz)数据集（存放在 S3 上），当然你必须要修改成自己的 `datasetPath` 和 `testDatasetPath`。

原始数据托管在 [Yann LeCun 的网站上](http://yann.lecun.com/exdb/mnist/)

#### 构建 ML Data Pipeline

```scala
// Define Dependent and Independent Features
val predictionCol = "label"
val labels = Seq("0","1","2","3","4","5","6","7","8","9")  
val pixelFeatures = (0 until 784).map(x => s"x$x").toArray

val layers = Array[Int](pixelFeatures.length, 784, 800, labels.length)

val vector_assembler = new VectorAssembler()  
  .setInputCols(pixelFeatures)
  .setOutputCol("features")

val stringIndexer = { new StringIndexer()  
  .setInputCol(predictionCol)
  .setOutputCol("label_index")
  .fit(dataset)
}
  
val binarizer = new Binarizer()  
  .setInputCol(vector_assembler.getOutputCol)
  .setThreshold(127.5)
  .setOutputCol("binarized_features")
  
val pca = new PCA().
  setInputCol(binarizer.getOutputCol).
  setOutputCol("pcaFeatures").
  setK(10)

val featurePipeline = new Pipeline().setStages(Array(vector_assembler, stringIndexer, binarizer, pca))

// Transform the raw data with the feature pipeline and persist it
val featureModel = featurePipeline.fit(dataset)

val datasetWithFeatures = featureModel.transform(dataset)

// Select only the data needed for training and persist it
val datasetPcaFeaturesOnly = datasetWithFeatures.select(stringIndexer.getOutputCol, pca.getOutputCol)
val datasetPcaFeaturesOnlyPersisted = datasetPcaFeaturesOnly.persist()
```

我们本想让 Pipeline 包含随机森林模型，但目前有一个 Bug ([SPARK-16845](https://issues.apache.org/jira/browse/SPARK-16845)) 让我们暂时没法这么做（这个问题会在 2.2.0 中得到修复）。

#### 训练一个随机森林模型
```scala
// You can optionally experiment with CrossValidator and MulticlassClassificationEvaluator to determine optimal
// settings for the random forest

val rf = new RandomForestClassifier().
      setFeaturesCol(pca.getOutputCol).
      setLabelCol(stringIndexer.getOutputCol).
      setPredictionCol("prediction").
      setProbabilityCol("probability").
      setRawPredictionCol("raw_prediction")

val rfModel = rf.fit(datasetPcaFeaturesOnlyPersisted)
```

#### 序列化 ML Data Pipeline 和 RF Model 为 Bundle.ML
```scala
import org.apache.spark.ml.mleap.SparkUtil

val pipeline = SparkUtil.createPipelineModel(uid = "pipeline", Array(featureModel, rfModel))

val sbc = SparkBundleContext().withDataset(rfModel.transform(datasetWithFeatures))
for(bf <- managed(BundleFile("jar:file:/tmp/mnist-spark-pipeline.zip"))) {
        pipeline.writeBundle.save(bf)(sbc).get
}
```

### 反序列化为 MLeap 和评分新数据

这一步的目的是展示如何反序列一个 `bundle` 然后使用它来对 Leap Frame 进行评分，而无需任何 Spark 依赖。你可以从我们的 S3 存储桶下载这个 [mnist.json](https://s3-us-west-2.amazonaws.com/mleap-demo/mnist/mnist.json)。

```scala
import ml.combust.mleap.runtime.MleapSupport._
import ml.combust.mleap.runtime.MleapContext.defaultContext
import java.io.File

// load the Spark pipeline we saved in the previous section
val mleapPipeline = (for(bf <- managed(BundleFile("jar:file:/tmp/mnist-spark-pipeline.zip"))) yield {
      bf.loadMleapBundle().get.root
    }).tried.get

```

从我们的 mleap-demo Git 仓库中加载一个样例 Leap Frame（data/mnist.json）。

```scala
import ml.combust.mleap.runtime.serialization.FrameReader

val s = scala.io.Source.fromURL("file:///./mleap-demo/mnist.json").mkString

val bytes = s.getBytes("UTF-8")
val frame = FrameReader("ml.combust.mleap.json").fromBytes(bytes)

// transform the dataframe using our pipeline
val frame2 = mleapPipeline.transform(frame).get
val data = frame2.dataset
```

接下来你可以从[这里](https://github.com/combust/mleap-demo)拿到更多的示例和 Notebook。