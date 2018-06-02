# 入门指南

无论是集成 PySpark、Scikit-Learn、Spark、TensorFlow 还是直接通过 MLeap Runtime 来使用 MLeap，上手都是非常轻松的。大多数情况下，所有你需要的依赖都已经被托管在 [Maven Central](https://search.maven.org/) 或者 [PyPi](https://pypi.python.org/pypi) 上了，除了 TensorFlow Transformer 这个例外，其构建和运行可能还需要你自己编译一些 Jar 包。

你可以通过交互式 Scala 命令行、Python 命令行或者创建一个 Notebook 来轻松体验 MLeap。

## 典型 MLeap 工作流

一个典型的 MLeap 工作流包括三部分：
1.  训练：维持你现有的方式来构建 ML Pipeline。
2.  序列化：序列化你所有的数据处理流程（即 ML Pipeline）和算法为 Bundle.ML。
3.  执行：使用 MLeap Runtime 去执行你序列化后的 Pipeline，执行过程不再依赖 Spark 或者 Scikit-Learn，但对于 TensorFlow 你仍然需要一些二进制包。

### 训练

MLeap 被设计成尽可能不要影响你现有的 ML Pipeline 构建流程。我们希望你能够保持现在编写 Scala 或者 Python 代码的方式，以尽可能少的额外代码来获得 MLeap 相应的功能。

如你将会在基础用法章节所见，大多数情况下所需要做的是导入一些 MLeap 的依赖包（Scikit-Learn 除外）。

### 序列化

当你训练好自己的 Pipeline 之后，MLeap 能够把整个 ML/Data Pipeline 和训练算法（线性模型、树模型、神经网络等）转换成 Bundle.ML。序列化生成 `bundle`，bundle 是你想要部署的 Pipeline 和算法的物理存储，你可以将其部署、分享，或者是直接去浏览 Pipeline 中的每一环节。

### 执行

MLeap 最初的目标是实现 Spark ML Pipeline 的评分（Scoring），而无需依赖于 Spark 本身。MLeap Runtime 实现了这个功能，其加载序列化后的 Bundle，并转换输入 Data Frame（在 MLeap 里面被称为 Leap Frames）。

我们有提及 MLeap Runtime 的速度非常快吗？我们的基准测试中，LeapFrames 的直接执行时间达到了微秒级别，在集成至 RESTful API 服务时，也能实现 5ms 以内的响应时间。

注：目前为止，MLeap Runtime 只提供了 Java / Scala 依赖库，我们计划在未来提供 Python 绑定。

## 组件
### MLeap Spark

### MLeap Scikit

### MLeap Bundle

### MLeap Runtime