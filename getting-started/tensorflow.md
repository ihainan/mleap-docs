# TensorFlow 集成入门

MLeap Tensorflow 集成允许用户将 TensorFlow Graph 当做 Transformer 集成到 ML Pipeline 中。在未来我们会提升对 TensorFlow 的兼容性。目前来说，TensorFlow 与 MLeap 的集成还是一个实验性功能，我们仍在进一步稳定这个特性。

## 编译 MLeap-TensorFlow 模块

MLeap TensorFlow 模块未被托管在 Maven Central 上，用户必须借助 TensorFlow 提供的 JNI（Java Native Interface）支持，编译源码获得。参考相关 [教程](building.md#build-tensorflow-mleap-module) 从源码编译 TensorFlow 模块。

## 使用 MLeap-TensorFlow

编译工作就绪之后，你就能轻松将 TensorFlow 集成到 MLeap Pipeline 中。

首先，添加 MLeap-TensorFlow 作为项目依赖。

```sbt
libraryDependencies += "ml.combust.mleap" %% "mleap-tensorflow" % "0.9.0"
```

接下来就能在代码中使用 Tensor Graph。让我们构建一个包含两个 Tensor 的简单 Graph。

```scala
import ml.combust.bundle.dsl.Shape
import ml.combust.mleap.runtime.frame.{DefaultLeapFrame, Row}
import ml.combust.mleap.runtime.types.{FloatType, StructField, StructType}
import org.tensorflow

// Initialize our Tensorflow demo graph
val graph = new tensorflow.Graph

// Build placeholders for our input values
val inputA = graph.opBuilder("Placeholder", "InputA").
  setAttr("dtype", tensorflow.DataType.FLOAT).
  build()
val inputB = graph.opBuilder("Placeholder", "InputB").
  setAttr("dtype", tensorflow.DataType.FLOAT).
  build()

// Multiply the two placeholders and put the result in
// The "MyResult" tensor
graph.opBuilder("Mul", "MyResult").
  setAttr("T", tensorflow.DataType.FLOAT).
  addInput(inputA.output(0)).
  addInput(inputB.output(0)).
  build()

// Build the MLeap model wrapper around the Tensorflow graph
val model = TensorflowModel(graph,
  // Must specify inputs and input types for converting to TF tensors
  inputs = Seq(("InputA", FloatType(false)), ("InputB", FloatType(false))),
  // Likewise, specify the output values so we can convert back to MLeap
  // Types properly
  outputs = Seq(("MyResult", FloatType(false))))

// Connect our Leap Frame values to the Tensorflow graph
// Inputs and outputs
val shape = Shape().
  // Column "input_a" gets sent to the TF graph as the input "InputA"
  withInput("input_a", "InputA").
  // Column "input_b" gets sent to the TF graph as the input "InputB"
  withInput("input_b", "InputB").
  // TF graph output "MyResult" gets placed in the leap frame as col
  // "my_result"
  withOutput("my_result", "MyResult")

// Create the MLeap transformer that executes the TF model against
// A leap frame
val transformer = TensorflowTransformer(inputs = shape.inputs,
  outputs = shape.outputs ,
  rawOutputCol = Some("raw_result"),
  model = model)

// Create a sample leap frame to transform with the Tensorflow graph
val schema = StructType(StructField("input_a", FloatType()), StructField("input_b", FloatType())).get
val dataset = Seq(Row(5.6f, 7.9f),
  Row(3.4f, 6.7f),
  Row(1.2f, 9.7f))
val frame = DefaultLeapFrame(schema, dataset)

// Transform the leap frame and make sure it behaves as expected
val data = transformer.transform(frame).get.dataset
assert(data(0)(3) == 5.6f * 7.9f)
assert(data(1)(3) == 3.4f * 6.7f)
assert(data(2)(3) == 1.2f * 9.7f)

// Cleanup the transformer
// This closes the TF session and graph resources
transformer.close()
```

更多关于 TensorFlow 集成如何运作的细节：

1. 数据集成与转换的相关细节参见 [本章节](../tensorflow/mleap-integration.md)。
2. 序列化 TensorFlow Graph 为 MLeap Bundle 的相关细节参见 [本章节](../tensorflow/bundle-serialization.md)。

