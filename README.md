<img src="assets/images/logo.png" alt="MLeap Logo" width="176" height="70" />

#### 什么是 MLeap?

MLeap 既是适用于机器学习 Pipeline 存储的通用序列化格式，又是 Pipeline 的通用执行引擎。MLeap 支持 Spark、Scikit-Learn、TensorFlow 等机器学习框架的 Pipeline 的训练，也能够将 Pipeline 导出成一个 MLeap Bundle。序列化得到的 Bundle 可以被反序列回原始的 Pipeline，也可被用于评分引擎（API 服务）中。 

## 为什么选择 MLeap？

不少公司在使用 Spark 和 Scikit-Learn 来部署他们的机器学习 / 数据 Pipeline 模型到生产环境 API 服务时，会遇到许多麻烦。如果公司不愿意使用 Python 作为他们的 API 栈，也不愿意使用 Google ML Cloud，即使是使用 Tensorflow，也会变得相当棘手。MLeap 提供了简单的接口去执行包括特征 Transformer 到分类器、回归算法、聚类算法和神经网络在内的整个 ML Pipeline。 

### 跨平台模型 

模型属于你自己。你可以把 MLeap Bundle 带到任何地方。诸如 Microsoft Azure 和 Google ML Cloud 在内的平台会把你限制在他们提供的服务包里面，而 MLeap 允许模型跟着你走。 

### Spark、Scikit-Learn 和  TensorFlow 的统一 Runtime 

混合多种机器学习技术对于 MLeap 来说易如反掌。无需再去要求整个开发团队都去研究 Pipeline 相关产品，只需简单导出为 MLeap Bundle，就可以在任何需要的地方执行你的 Pipeline。 

统一的 Runtime 还有这些好处：

* 分别使用 Spark、Scikit-Learn 或者 TensorFlow 来训练 Pipeline 的其中一小部分，然后将它们导出成单一的的 MLeap Bundle 文件，并部署到任何地方。
* 如果你正在使用 Scikit-Learn 做研发，但 Spark 提供了更好的算法实现，你可以把 Scikit-Learn 的 ML Pipeline 转换成 Spark Pipeline，在 Spark 中训练得到新的模型，并使用 MLeap Runtime 将模型部署到生产环境中

<img src="assets/images/single-runtime.jpg" alt="Unified Runtime"/>

### 通用序列化格式 

除了提供了一个非常实用的执行引擎之外，对于包含大量特征提取器和算法在内的 Pipeline，MLeap Bundle 提供了一套通用的序列化格式，允许用户在 Spark、Scikit-Learn、TensorFlow 以及 MLeap 之间相互进行格式转换。这意味着当你需要在特定环境执行 Pipeline 时，只需简单地把 Pipeline 转换为对应平台的格式即可。 

### 无缝集成 

多半情况下，我们无需修改任何内部代码，或者要求使用 Spark 或者 Scikit-Learn 来实现 Transformer。对于 TensorFlow，如果 MLeap 中不存在对应的 Op，我们也会尽可能使用已经内置的 Op 来做相应实现。这意味着如果想要使用和运行 MLeap，对原有 Pipeline 代码的改动是非常小的。而对于大多数使用场景，无需做任何代码修改，只需要把 Pipeline 导出成 MLeap Bundle，或者将其部署到一个 Combust API 服务上，就能立刻使用你的 Pipeline。 

### 开源

MLeap 完全开放源码，我们的源码可以从 [https://github.com/combust/mleap](https://github.com/combust/mleap) 获取。此外我们的自动化测试和部署放置在  [travis ci](https://travis-ci.org/combust/mleap) 上。 