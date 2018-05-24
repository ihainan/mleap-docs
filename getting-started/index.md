# Getting Started | 入门


Whether using MLeap with PySpark, Scikit-learn, Spark, Tensorflow, or MLeap Runtime, getting started is easy. For the most part, everything you will need is already in [Maven Central](https://search.maven.org/) or [PyPi](https://pypi.python.org/pypi). If you are trying to get up and running with Tensorflow transformers, you will have to build some of your own jars.

无论是集成 PySpark、Scikit-Learn、Spark、TensorFlow 还是直接通过 MLeap Runtime 来使用 MLeap，上手都是非常简单的。大多数情况下，所有你需要的依赖都已经放在 [Maven Central](https://search.maven.org/) 或者 [PyPi](https://pypi.python.org/pypi) 上了，除了 TensorFlow Transformer 这个例外，其构建和运行可能还需要你自己编译一些 Jar 包。

Experimenting with MLeap is easy to do either through an interactive Scala console, Python console or a notebook.

你可以通过交互式 Scala 命令行、Python 命令行或者创建一个 Notebook 来轻松体验 MLeap。

## TYPICAL MLEAP WORKFLOW | 典型 MLeap 工作流

A typical MLeap workflow consists of 3 parts:
1. Training: Write your ML Pipelines the same way you do today
2. Serialization: Serialize all of the data processing (ml pipeline) and the algorithms to Bundle.ML
3. Execution: Use MLeap runtime to execute your serialized pipeline without dependencies on Spark or Scikit (you'll still need TensorFlow binaries)

一个典型的 MLeap 工作流包括三部分：
1.  训练：维持你现有的方式来构建 ML Pipeline。
2.  序列化：序列化你所有的数据处理流程（即 ML Pipeline）和算法为 Bundle.ML。
3.  执行：使用 MLeap Runtime 去执行你序列化后的 Pipeline，执行过程不再依赖 Spark 或者 Scikit-Learn，但对于 TensorFlow 你仍然需要一些二进制包。

### Training | 训练

MLeap is designed to have minimal impact on how you build your ML pipelines today. We want you to write your scala or python code in the same way you do today, with minimial additional needed to support MLeap functionality.

MLeap 被设计成尽可能不要影响你现有的 ML Pipeline 构建流程。我们希望你能够保持现在编写 Scala 或者 Python 代码的方式，以尽可能少的额外代码来支持 MLeap 相应的功能。

As you will see from the basic usage section, most often all you have to do is import some MLeap libraries and that is it (except for scikit-learn).

如你将会在 ___基础用法___ 章节所见，大多数情况下你需要做的，是导入一些 MLeap 的依赖包（除了 Scikit-Learn）。

### Serialization | 序列化

Once you have your pipeline trained, MLeap provides functionality to serialize the entire ML/Data Pipeline and your trained algorithm (linear models, tree-based models, neural networks) to Bundle.ML. Serialization generates something called a `bundle` which is a physical representation of your pipeline and algorithm that you can deploy, share, view all of the pieces of the pipeline.

当你训练好自己的 Pipeline 之后，MLeap 能够把整个 ML/Data Pipeline 和训练算法（线性模型、树模型、神经网络等）转换成 Bundle.ML。序列化过程会生成 `bundle`，其物理存储了 Pipeline，你可以将其部署、分享，或者是查看 Pipeline 中的每一环节。

### Execution | 执行

The goal of MLeap was initially to enable scoring of Spark's ML pipelines without the dependency on Spark. That functionality is powered by MLeap Runtime, which loads your serialized bundle and executes it on incoming dataframes (LeapFrames).

MLeap 最初的目标是实现 Spark ML Pipeline 的评分（Scoring），而无需依赖于 Spark 本身。MLeap Runtime 实现了这个功能，其加载序列化后的 Bundle，并转换输入的数据帧（在 MLeap 里面被称为 LeapFrames）。

Did we mention that MLeap Runtime is extremely fast? We have recorded benchmarks of micro-second execution on LeapFrames and sub-5ms response times when part of a RESTful API service.

我们有提及 MLeap Runtime 的速度非常快吗？我们的基准测试中，LeapFrames 的直接执行时间达到了毫秒级别，在集成至 RESTful API 服务时，也能实现 5ms 以内的响应时间。

Note: As of right now, MLeap runtime is only provided as a Java/Scala library, but we do plan to add python bindings in the future.

注：目前为止，MLeap Runtime 只提供了 Java / Scala 依赖库，我们计划在未来提供相应的 Python 绑定。

## Components | 组件
### MLeap Spark

### MLeap Scikit

### MLeap Bundle

### MLeap Runtime