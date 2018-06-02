# MLeap 常见问题

## MLeap 是否支持自定义 Transformer

妥妥的 - 我们的目标是让自定义 Transformer 变得简单。编写一个自定义 Transformer 与为 Spark 和 MLeap 编写 Transformer 的过程基本一致，唯一的区别在于我们已经预先打包好了整合 `mleap-runtime` 和 `mleap-spark` 的所有原生 Spark Transformer。

请参见  [自定义 Transformer](mleap-runtime/custom-transformer.md) 章节，了解更多有关自定义 Transformer 的细节。

## MLeap Runtime 的推断性能如何？

MLeap 经过优化，已经能够提供微秒级别（即千分之一毫秒，有人希望我们能够明确这点）的 ML Pipeline 执行性能。

实际的执行速度取决于 Pipeline 中的节点数量，我们的测试是以 AirBnb Pipeline 作为标准，并与使用 `SparkContext` 处理 `LocalRelation` DataFrame 的结果来做对比。

下面两组基准测试使用带有相同特征处理 Transformer 的 Pipeline，它们都包含 Vector Assembler、Standard Scaler、String Indexer 和 One-Hot-Encoder，但 Pipeline 最后执行的分别是：

* 线性回归：6.2 微秒（.0062 毫秒） 对比 Spark LocalRelation 的 106 毫秒
* 随机森林：6.8 微秒（.0068 毫秒） 对比 Spark LocalRelation 的 101 毫秒

### MLeap 随机森林转换速度：

随机森林：大约 6.8 微秒（68 / 10000）

| Transformer 数量 | 总时间（毫秒） | 转换时间（微秒） |
|:---:|:---:|:---:|
| 1000 | 6.956204 | 7 |
| 2000 | 13.717578 | 7 |
| 3000 | 20.424697 | 7 |
| 4000 | 27.160537 | 7 |
| 5000 | 34.025757 | 7 |
| 6000 | 41.017156 | 7 |
| 7000 | 48.140102 | 7 |
| 8000 | 54.724859 | 7 |
| 9000 | 61.769202 | 7 |
| 10000 | 68.646654 | 7 |

### 运行我们的基准测试

若想要运行我们的基准测试，或者想了解如何创建自己的测试，请参见我们的 [MLeap 基准测试](https://github.com/combust/mleap/tree/master/mleap-benchmark)项目。

更多的基准测试请从 [MLeap 基准测试 README 文档](https://github.com/combust/mleap/blob/master/mleap-benchmark/README.md)获取。

## 为什么选择 MLeap Bundle，而不是 PMML 或者其他序列化框架

MLeap 的序列化考虑到了如下的目标和要求：

1. 应该能够方便开发者使用 Scala 和 Java （未来会添加 Python 和 C 语言支持）来添加 `自定义 Transformer`。
2. 序列化格式应该足够灵活，并满足最大限度的性能要求。MLeap 可被序列化成 Protobuf 3，从而使大规模 Pipeline（包含上千个特征）和模型（随机森林、神经网络等）的弹性部署与执行变为可能。
3. 序列化操作应该为 ML Transformer 和 Pipeline 做好相关优化。
4. 序列化后的文件应该能够被包括低级语言（比如 C、C++ 和 Rust 等）在内的所有的环境和平台访问。
5. 为 Spark、Scikit-Learn 和 TensorFlow 的 Transformer 提供一套通用的序列化框架（例如一个 Standard Scaler 能够在所有平台以相同的方式去执行）。

## MLeap 是否已经可以用于生产工作？

是的，MLeap 已经在一些公司和工厂内被用于实际的生产工作，其范围覆盖了广告技术、自动驾驶、深度学习（集成 Spark ML Pipeline 和 TF Inception 模型）和市场调研等。

MLeap 0.9.0 为 ML Pipeline 提供了稳定的序列化格式和 Runtime API。1.0.0 版本会保证能够向后兼容这些格式和 API，但是我们无法预知后面会不会有一些比较大的结构变动。

## 为什么不使用 SparkContext 和 LocalRelation DataFrame 来实现转换操作  

依赖于 Spark Context 的接口能够被优化在大概 100ms 的请求处理时间，但是对于企业需求来说这个时间实在是太慢了，比如营销平台就要求低于 5ms 的请求响应时间。MLeap 提供了亚毫秒级的复杂 Pipeline 执行时间。这样的性能归功于 Scala Breeze 线性代数库等技术的支持。

## 是否支持 Spark MLlib？

Spark ML Pipeline 已经提供了大量与 MLlib 相同的 Transformer 和模型。此外，我们也在 `mleap-spark-extension` 子模块中提供了 MLlib SupportVectorMachine 的包装器。

如果你发现有任何 MLlib 包含但是 Spark ML 遗漏的功能和特征，请告诉我们，或者直接向 MLeap 提供你自己实现的包装器。

## TensorFlow 集成是如何运作的？

目前 TensorFlow 的集成是基于 TensorFlow 官方提供的 SWIG 包装器来实现的。我们最后可能会将 SWIG 替换成 JavaCPP 绑定，或者使用一种类似于 Erlang 的方式，为每一个 TensorFlow Graph 的执行提供一个单独的 TensorFlow Process。但无论如何，现有接口都不会发生变动，你永远都可以使用 `TensorflowTransformer` 来处理你的 Leap Frame。

## 怎么为项目做贡献？

* 移植 Spark 目前已有的 Estimator 和 Transformer，或者提交你自己实现的自定义 Transformer。
* 完善文档
* 为有趣的机器学习应用场景编写教程和指南
* 在你的公司使用 MLeap，并向我们反馈你的想法
* 与我们在  [Gitter](https://gitter.im/combust/mleap) 上交流

你也可以直接发邮件到我们的邮箱地址： `hollin@combust.ml` 和`mikhail@combust.ml `。

