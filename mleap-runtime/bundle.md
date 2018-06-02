# MLeap 序列化

MLeap 中序列化和反序列化都非常简单。你可以选择序列化 MLeap Bundle 到文件系统中的一个目录，或者是序列化为一个 Zip 压缩包以便用于后期分发。

## 创建一个简单的 MLeap Pipeline

```scala
import ml.combust.bundle.BundleFile
import ml.combust.bundle.serializer.SerializationFormat
import ml.combust.mleap.core.feature.{OneHotEncoderModel, StringIndexerModel}
import ml.combust.mleap.core.regression.LinearRegressionModel
import ml.combust.mleap.runtime.transformer.Pipeline
import ml.combust.mleap.runtime.transformer.feature.{OneHotEncoder, StringIndexer, VectorAssembler}
import ml.combust.mleap.runtime.transformer.regression.LinearRegression
import org.apache.spark.ml.linalg.Vectors
import ml.combust.mleap.runtime.MleapSupport._
import resource._

// Create a sample pipeline that we will serialize
// And then deserialize using various formats
val stringIndexer = StringIndexer(
  shape = NodeShape.scalar(inputCol = "a_string", outputCol = "a_string_index"),
  model = StringIndexerModel(Seq("Hello, MLeap!", "Another row")))
val oneHotEncoder = OneHotEncoder(
  shape = NodeShape.vector(1, 2, inputCol = "a_string_index", outputCol = "a_string_oh"),
  model = OneHotEncoderModel(2, dropLast = false))
val featureAssembler = VectorAssembler(
  shape = NodeShape().withInput("input0", "a_string_oh").
          withInput("input1", "a_double").withStandardOutput("features"),
  model = VectorAssemblerModel(Seq(TensorShape(2), ScalarShape())))
val linearRegression = LinearRegression(
  shape = NodeShape.regression(3),
  model = LinearRegressionModel(Vectors.dense(2.0, 3.0, 6.0), 23.5))
val pipeline = Pipeline(
  shape = NodeShape(),
  model = PipelineModel(Seq(stringIndexer, oneHotEncoder, featureAssembler, linearRegression)))
```

## 序列化为 Zip 文件

In order to serialize to a zip file, make sure the URI begins with `jar:file` and ends with a `.zip`.

为了序列化为 Zip 文件，需要确保 URL 以 `jar:file` 开头，以 `.zip` 结尾。

For example `jar:file:/tmp/mleap-bundle.zip`.

例如： `jar:file:/tmp/mleap-bundle.zip`。

### JSON 格式

```scala
for(bundle <- managed(BundleFile("jar:file:/tmp/mleap-examples/simple-json.zip"))) {
  pipeline.writeBundle.format(SerializationFormat.Json).save(bundle)
}
```

### Protobuf 格式

```scala
for(bundle <- managed(BundleFile("jar:file:/tmp/mleap-examples/simple-protobuf.zip"))) {
  pipeline.writeBundle.format(SerializationFormat.Protobuf).save(bundle)
}
```

## 序列化为目录

为了序列化为目录，需要确保 URL 以 `file` 开头。

例如： `file:/tmp/mleap-bundle-dir`

### JSON 格式

```scala
for(bundle <- managed(BundleFile("file:/tmp/mleap-examples/simple-json-dir"))) {
  pipeline.writeBundle.format(SerializationFormat.Json).save(bundle)
}
```

### Protobuf 格式

```scala
for(bundle <- managed(BundleFile("file:/tmp/mleap-examples/simple-protobuf-dir"))) {
  pipeline.writeBundle.format(SerializationFormat.Protobuf).save(bundle)
}
```

## 反序列化

反序列化和序列化一样简单，你无需事先知道 MLeap Bundle 的序列化格式，唯一需要了解的，是这个包的路径。

### 反序列化 Zip Bundle

```scala
// Deserialize a zip bundle
// Use Scala ARM to make sure resources are managed properly
val zipBundle = (for(bundle <- managed(BundleFile("jar:file:/tmp/mleap-examples/simple-json.zip"))) yield {
  bundle.loadMleapBundle().get
}).opt.get
```

### 反序列化目录 Bundle

```scala
// Deserialize a directory bundle
// Use Scala ARM to make sure resources are managed properly
val dirBundle = (for(bundle <- managed(BundleFile("file:/tmp/mleap-examples/simple-json-dir"))) yield {
  bundle.loadMleapBundle().get
}).opt.get
```

