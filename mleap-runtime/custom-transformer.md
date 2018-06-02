# 自定义 Transformer

MLeap 中的每一个内置 Transformer 都可以被视为自定义 Transformer。你所写的和我们所写的 Transformer / Bundle 集成代码（Bundle Integration Code）的唯一区别在于，我们的代码被内置到发布的 Jar 包中。我们欢迎用户通过提交 PR 来为 MLeap 项目添砖加瓦。

我们的 [MLeap 源码](https://github.com/combust/mleap) 中有大量的例子用来示范如何编写自己的 Transformer，以及如何实现 Transformer 在 Spark 和 MLeap 之间的序列化转换。

让我们通过一个简单的例子来完整过一遍自定义 Transformer 的过程，这个例子使用了一个包含我们所有待转换数据的  `Map[String, Double]` 对象，来将输入的字符串通映射成浮点值。

我们把这个自定义 Transformer 命名为 `StringMap`。你可以从 [StringMapModel.scala](https://github.com/combust/mleap/blob/master/mleap-core/src/main/scala/ml/combust/mleap/core/feature/StringMapModel.scala) 获取到它的源代码。  

## 概述

简单概述所需步骤：

1. 构建我们的核心模型逻辑（Core Model Logic）用于在 Spark 和 MLeap 之间共享数据。
2. 构建 MLeap Transformer
3. 构建 Spark Transformer
4. 为 MLeap 编写 Bundle 序列化器
5. 为 Spark 编写 Bundle 序列化器
6. 注册 MLeap Transformer 和 Spark Transformer 到 MLeap Bundle 注册表（Registry）中


## 核心模型

核心模型包含转换输入数据的相关逻辑。核心模型不包含任何对 Spark 和 MLeap 的依赖。在我们 `StringMapModel` 的例子中，核心模型是一个类，它知道如何将字符串映射到浮点值。让我们看看它的 Scala 实现。

[StringMapModel.scala](https://github.com/combust/mleap/blob/master/mleap-core/src/main/scala/ml/combust/mleap/core/feature/StringMapModel.scala)
```scala
case class StringMapModel(labels: Map[String, Double]) extends Model {
  def apply(label: String): Double = labels(label)

  override def inputSchema: StructType = StructType("input" -> ScalarType.String).get

  override def outputSchema: StructType = StructType("output" -> ScalarType.Double).get
}
```

这个 case class 包含了一个 labels 成员，存储字符串映射到浮点值的映射关系。它非常类似于 `StringIndexerModel`，但是它的字符串是随意且无序的。  

## MLeap Transformer

MLeap Transformer 包含将核心模型逻辑应用到 Leap Frame 的代码段。所有的 MLeap Transformer 继承自基类  `ml.combust.mleap.runtime.transformer.Transformer`。我们在  `StringMap` 例子中使用了一个工具基类 `ml.combust.mleap.runtime.transformer.SimpleTransformer` 来实现简单的输入输出数据转换。这个基类可以作为任何仅包含单条输入和单条输出的 Transformer 的样板类。  

以下是 MLeap Transformer 的 Scala 源码。

[StringMap.scala](https://github.com/combust/mleap/blob/master/mleap-runtime/src/main/scala/ml/combust/mleap/runtime/transformer/feature/StringMap.scala)
```scala
import ml.combust.mleap.core.feature.StringMapModel
import ml.combust.mleap.core.types.NodeShape
import ml.combust.mleap.runtime.function.UserDefinedFunction
import ml.combust.mleap.runtime.transformer.{SimpleTransformer, Transformer}

case class StringMap(override val uid: String = Transformer.uniqueName("string_map"),
                     override val shape: NodeShape,
                     override val model: StringMapModel) extends SimpleTransformer {
  override val exec: UserDefinedFunction = (label: String) => model(label)
```

需要注意 exec 这个 `UserDefinedFunction`  实例。这是一个从 Scala 方法反射创建得到的 MLeap 用户定义方法。用户自定义方法是 MLeap 允许转换 Leap Frame 的主要手段。`NodeShape` 类的实例 shape 定义了 Transformer 的输入字段和输出字段。  

## Spark Transformer

Spark Transformer 知道如何将核心模型逻辑应用到 Spark DataFrame 中。所有的 Spark Transformer 都继承自 `org.apache.spark.ml.Transformer`。如果你之前曾实现过自定义 Spark Transformer，其过程非常相似。  

以下是 Spark Transformer 的 Scala 源码：  

[StringMap.scala](https://github.com/combust/mleap/tree/master/mleap-spark-extension/src/main/scala/org/apache/spark/ml/mleap/feature/StringMap.scala)
```scala
import ml.combust.mleap.core.feature.StringMapModel
import org.apache.spark.annotation.DeveloperApi
import org.apache.spark.ml.Transformer
import org.apache.spark.ml.param.ParamMap
import org.apache.spark.ml.param.shared.{HasInputCol, HasOutputCol}
import org.apache.spark.ml.util.Identifiable
import org.apache.spark.sql.functions._
import org.apache.spark.sql.{DataFrame, Dataset}
import org.apache.spark.sql.types._

class StringMap(override val uid: String,
                val model: StringMapModel) extends Transformer
  with HasInputCol
  with HasOutputCol {
  def this(model: StringMapModel) = this(uid = Identifiable.randomUID("string_map"), model = model)

  def setInputCol(value: String): this.type = set(inputCol, value)
  def setOutputCol(value: String): this.type = set(outputCol, value)

  @org.apache.spark.annotation.Since("2.0.0")
  override def transform(dataset: Dataset[_]): DataFrame = {
    val stringMapUdf = udf {
      (label: String) => model(label)
    }

    dataset.withColumn($(outputCol), stringMapUdf(dataset($(inputCol))))
  }

  override def copy(extra: ParamMap): Transformer = copyValues(new StringMap(uid, model), extra)

  @DeveloperApi
  override def transformSchema(schema: StructType): StructType = {
    require(schema($(inputCol)).dataType.isInstanceOf[StringType],
      s"Input column must be of type StringType but got ${schema($(inputCol)).dataType}")
    val inputFields = schema.fields
    require(!inputFields.exists(_.name == $(outputCol)),
      s"Output column ${$(outputCol)} already exists.")

    StructType(schema.fields :+ StructField($(outputCol), DoubleType))
  }
}
```

## MLeap 序列化  

我们需要定义如何序列化我们的模型到 MLeap Bundle，以及如何从 MLeap Bundle 反序列化得到我们的模型。为了达到这个目的，我们需要为 MLeap Transformer 和核心模型实现 `ml.combust.mleap.bundle.ops.MleapOp` and `ml.combust.bundle.op.OpModel` 两个抽象类。这两个类是我们定义 bundle 序列化所需要的所有类。  

以下是我们实现 MLeap Transformer 序列化过程的 Scala 源码。

注意：下面的代码看上去很长，其实大多是都是 IDE 自动生成的。  

[StringMapOp.scala](https://github.com/combust/mleap/blob/master/mleap-runtime/src/main/scala/ml/combust/mleap/bundle/ops/feature/StringMapOp.scala)
```scala
import ml.combust.bundle.BundleContext
import ml.combust.bundle.dsl._
import ml.combust.bundle.op.OpModel
import ml.combust.mleap.bundle.ops.MleapOp
import ml.combust.mleap.core.feature.StringMapModel
import ml.combust.mleap.runtime.MleapContext
import ml.combust.mleap.runtime.transformer.feature.StringMap

class StringMapOp extends MleapOp[StringMap, StringMapModel] {
  override val Model: OpModel[MleapContext, StringMapModel] = new OpModel[MleapContext, StringMapModel] {
    // the class of the model is needed for when we go to serialize JVM objects
    override val klazz: Class[StringMapModel] = classOf[StringMapModel]

    // a unique name for our op: "string_map"
    override def opName: String = Bundle.BuiltinOps.feature.string_map

    override def store(model: Model, obj: StringMapModel)
                      (implicit context: BundleContext[MleapContext]): Model = {
      // unzip our label map so we can store the label and the value
      // as two parallel arrays, we do this because MLeap Bundles do
      // not support storing data as a map
      val (labels, values) = obj.labels.toSeq.unzip

      // add the labels and values to the Bundle model that
      // will be serialized to our MLeap bundle
      model.withValue("labels", Value.stringList(labels)).
        withValue("values", Value.doubleList(values))
    }

    override def load(model: Model)
                     (implicit context: BundleContext[MleapContext]): StringMapModel = {
      // retrieve our list of labels
      val labels = model.value("labels").getStringList

      // retrieve our list of values
      val values = model.value("values").getDoubleList

      // reconstruct the model using the parallel labels and values
      StringMapModel(labels.zip(values).toMap)
    }
  }

  // the core model that is used by the transformer
  override def model(node: StringMap): StringMapModel = node.model
}
```

我们还需要注册 `StringMapOp` 到 MLeap Bundle 注册表中，以让 MLeap 在运行时知道它的存在。我们会在本文稍后来讨论注册表。  

## Spark 序列化

我们同样需要定义如何序列化 / 反序列化我们的 Spark 模型。其与我们处理 MLeap Transformer 的过程非常相似。我们需要再次实现 `ml.combust.bundle.op.OpNode` 和 `ml.combust.bundle.op.OpModel` 两个类。  

我们的序列化 Scala 代码如下。

[StringMapOp.scala](https://github.com/combust/mleap/tree/master/mleap-spark-extension/src/main/scala/org/apache/spark/ml/bundle/extension/ops/feature/StringMapOp.scala)
```scala
import ml.combust.bundle.BundleContext
import ml.combust.bundle.dsl._
import ml.combust.bundle.op.{OpModel, OpNode}
import ml.combust.mleap.core.feature.StringMapModel
import ml.combust.mleap.runtime.MleapContext
import org.apache.spark.ml.bundle.SparkBundleContext
import org.apache.spark.ml.mleap.feature.StringMap

class StringMapOp extends OpNode[SparkBundleContext, StringMap, StringMapModel] {
  override val Model: OpModel[SparkBundleContext, StringMapModel] = new OpModel[SparkBundleContext, StringMapModel] {
    // the class of the model is needed for when we go to serialize JVM objects
    override val klazz: Class[StringMapModel] = classOf[StringMapModel]

    // a unique name for our op: "string_map"
    // this should be the same as for the MLeap transformer serialization
    override def opName: String = Bundle.BuiltinOps.feature.string_map

    override def store(model: Model, obj: StringMapModel)
                      (implicit context: BundleContext[SparkBundleContext]): Model = {
      // unzip our label map so we can store the label and the value
      // as two parallel arrays, we do this because MLeap Bundles do
      // not support storing data as a map
      val (labels, values) = obj.labels.toSeq.unzip

      // add the labels and values to the Bundle model that
      // will be serialized to our MLeap bundle
      model.withValue("labels", Value.stringList(labels)).
        withValue("values", Value.doubleList(values))
    }

    override def load(model: Model)
                     (implicit context: BundleContext[SparkBundleContext]): StringMapModel = {
      // retrieve our list of labels
      val labels = model.value("labels").getStringList

      // retrieve our list of values
      val values = model.value("values").getDoubleList

      // reconstruct the model using the parallel labels and values
      StringMapModel(labels.zip(values).toMap)
    }
  }
  override val klazz: Class[StringMap] = classOf[StringMap]

  override def name(node: StringMap): String = node.uid

  override def model(node: StringMap): StringMapModel = node.model

  override def load(node: Node, model: StringMapModel)
                   (implicit context: BundleContext[SparkBundleContext]): StringMap = {
    new StringMap(uid = node.name, model = model).
      setInputCol(node.shape.standardInput.name).
      setOutputCol(node.shape.standardOutput.name)
  }

  override def shape(node: StringMap)(implicit context: BundleContext[SparkBundleContext]): NodeShape =
    NodeShape().withStandardIO(node.getInputCol, node.getOutputCol)
}
```

我们同样需要注册这个类到 MLeap 注册表中，从而让 MLeap 知道如何序列化 Spark Transformer。  

## MLeap Bundle 注册

一个注册表包含了所有的自定义 Transformers，以及给定的执行引擎的类型。在我们的例子中，我们提供了 `StringMap` 的 MLeap / Spark 执行引擎，因为我们必须要分别配置 Spark 和 MLeap 的注册表，以让 MLeap 知道如何序列化两者各自的 Transformers。  

MLeap 使用  [Typesafe Config](https://github.com/typesafehub/config) 来配置这些注册表。默认情况下，MLeap 自带了 Spark Runtime 和 MLeap Runtime 的注册表配置。你可以通过如下链接来查阅对应的配置文件：  

1. [MLeap 注册表](https://github.com/combust/mleap/blob/master/mleap-runtime/src/main/resources/reference.conf)
2. [Spark 注册表](https://github.com/combust/mleap/blob/master/mleap-spark/src/main/resources/reference.conf)

默认情况下，MLeap Runtime 使用 `ml.combust.mleap.registry.default` 中的配置，Spark 使用  `ml.combust.mleap.spark.registry.default` 中的配置。  

### MLeap 注册表

为了添加自定义 Transformer 到默认的 MLeap 注册表中，我们需要添加一个 `reference.conf` 到我们的文件中，它看起来像这样：  

```conf
// make a list of all your custom transformers
// the list contains the fully-qualified class names of the
// OpNode implementations for your transformers
my.domain.mleap.ops = ["my.domain.mleap.ops.StringMapOp"]

// include the custom transformers we have defined to the default MLeap registry
ml.combust.mleap.registry.default.ops += "my.domain.mleap.ops"
```

### Spark 注册表

为了添加自定义 Transformer 到默认的 Spark 注册表中，我们需要添加一个 `reference.conf` 到我们的文件中，它看起来像这样：  

```conf
// make a list of all your custom transformers
// the list contains the fully-qualified class names of the
// OpNode implementations for your transformers
my.domain.mleap.spark.ops = ["my.domain.spark.ops.StringMapOp"]

// include the custom transformers ops we have defined to the default Spark registries
ml.combust.mleap.spark.registry.v20.ops += my.domain.mleap.spark.ops
ml.combust.mleap.spark.registry.v21.ops += my.domain.mleap.spark.ops
ml.combust.mleap.spark.registry.v22.ops += my.domain.mleap.spark.ops
```
