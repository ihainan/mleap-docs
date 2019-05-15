# MLeap Spark 集成

MLeap 与 Spark 的集成带来了如下特性：

* 将 Transformer 和 Pipeline 序列化为 Bundle.ML，或者将 Bundle.ML 反序列化回 Transformer 和 Pipeline。
* 额外的特征 Transformer 和模型（例如 SVM、OneVsRest、MapTransform 等）。
* 支持自定义 Transformer。

使用 MLeap 无需去修改你现在构建 Pipeline 的方式，因此本文后面重点会描述如何在 Pipeline 和 Bundle.ml 之间序列化和反序列化。你可以参见 [MLeap Runtime](../mleap-runtime/index.md) 章节了解如何脱离 Spark 执行你的 Pipeline。

# Spark 序列化

Spark 的序列化和反序列化操作基本与 MLeap 一致，唯一的区别是：在序列化和反序列化 Spark Pipeline 的时候，我们需要导入不同的隐式支持类（Implicit Support Classes）。

## 创建一个简单的 Spark Pipeline

```scala
import ml.combust.bundle.BundleFile
import ml.combust.bundle.serializer.SerializationFormat
import org.apache.spark.ml.feature.{StringIndexerModel, VectorAssembler}
import org.apache.spark.ml.mleap.SparkUtil
import org.apache.spark.ml.bundle.SparkBundleContext
import ml.combust.mleap.spark.SparkSupport._
import resource._

// Create a sample pipeline that we will serialize
// And then deserialize using various formats
val stringIndexer = new StringIndexerModel(labels = Array("Hello, MLeap!", "Another row")).
  setInputCol("a_string").
  setOutputCol("a_string_index")
val featureAssembler = new VectorAssembler().setInputCols(Array("a_double")).
  setOutputCol("features")

// Because of Spark's privacy, our example pipeline is considerably
// Less interesting than the one we used to demonstrate MLeap serialization
val pipeline = SparkUtil.createPipelineModel(Array(stringIndexer, featureAssembler))
```

## 序列化为 Zip 文件

为了序列化为 Zip 文件，需要确保 URL 以 `jar:file` 开头，以 `.zip` 结尾。

例如： `jar:file:/tmp/mleap-bundle.zip`.

### JSON 格式

```scala
implicit val context = SparkBundleContext().withDataset(sparkTransformed)

for(bundle <- managed(BundleFile("jar:file:/tmp/mleap-examples/simple-json.zip"))) {
  pipeline.writeBundle.format(SerializationFormat.Json).save(bundle)(context)
}
```

### Protobuf 格式

```scala
implicit val context = SparkBundleContext().withDataset(sparkTransformed)

for(bundle <- managed(BundleFile("jar:file:/tmp/mleap-examples/simple-protobuf.zip"))) {
  pipeline.writeBundle.format(SerializationFormat.Protobuf).save(bundle)(context)
}
```

## 序列化为目录

为了序列化为目录，需要确保 URL 以 `file` 开头。

例如： `file:/tmp/mleap-bundle-dir`

### JSON 格式

```scala
implicit val context = SparkBundleContext().withDataset(sparkTransformed)

for(bundle <- managed(BundleFile("file:/tmp/mleap-examples/simple-json-dir"))) {
  pipeline.writeBundle.format(SerializationFormat.Json).save(bundle)(context)
}
```

### Protobuf 格式

```scala
implicit val context = SparkBundleContext().withDataset(sparkTransformed)

for(bundle <- managed(BundleFile("file:/tmp/mleap-examples/simple-protobuf-dir"))) {
  pipeline.writeBundle.format(SerializationFormat.Protobuf).save(bundle)(context)
}
```

## 反序列化

反序列化和序列化一样简单，你无需事先知道 MLeap Bundle 的序列化格式，唯一需要了解的，是这个包的路径。

### 反序列化 Zip Bundle

```scala
// Deserialize a zip bundle
// Use Scala ARM to make sure resources are managed properly
val zipBundle = (for(bundle <- managed(BundleFile("jar:file:/tmp/mleap-examples/simple-json.zip"))) yield {
  bundle.loadSparkBundle().get
}).opt.get
```

### 反序列化目录 Bundle

```scala
// Deserialize a directory bundle
// Use Scala ARM to make sure resources are managed properly
val dirBundle = (for(bundle <- managed(BundleFile("file:/tmp/mleap-examples/simple-json-dir"))) yield {
  bundle.loadSparkBundle().get
}).opt.get
```
