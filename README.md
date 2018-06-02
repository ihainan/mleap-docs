<img src="assets/images/logo.png" alt="MLeap Logo" width="176" height="70" />

#### What is MLeap? | 什么是 MLeap?

MLeap is a common serialization format and execution engine for machine learning pipelines. It supports Spark, Scikit-learn and Tensorflow for training pipelines and exporting them to an MLeap Bundle. Serialized pipelines (bundles) can be deserialized back into Spark, Scikit-learn, TensorFlow graphs, or an MLeap pipeline for use in a scoring engine (API Servers).

MLeap 既是适用于机器学习 Pipeline 存储的通用序列化格式，又是 Pipeline 的执行引擎。MLeap 支持 Spark、Scikit-Learn、TensorFlow 等机器学习框架的 Pipeline 的训练，也能够将 Pipeline 导出成一个 MLeap Bundle。序列化得到的 Bundle 可以被反序列回原始的 Pipeline，也可被用于评分引擎（API 服务）中。 

## Why MLeap? | 为什么选择 MLeap？

Many companies that use Spark and Scikit-learn have a difficult time deploying their research ML/data pipelines models to production API services. Even using Tensorflow can be difficult to set these services up if a company does not wish to use Python in their API stack or does not use Google ML Cloud. MLeap provides simple interfaces to execute entire ML pipelines, from feature transformers to classifiers, regressions, clustering algorithms, and neural networks.

不少公司在使用 Spark 和 Scikit-Learn 来部署他们的机器学习 / 数据 Pipeline 模型到生产环境 API 服务时，会遇到许多麻烦。即使是使用 Tensorflow，如果公司不愿意使用 Python 作为 API 栈，也不愿意使用 Google ML Cloud，也会变得相当棘手。MLeap 提供了简单的接口去执行包括特征 Transformer 到分类器、回归算法、聚类算法和神经网络在内的整个 ML Pipeline。 

### Portable Models | 跨平台模型 

Your models are your models. Take them with you wherever you go using MLeap Bundles. Platforms like Microsoft Azure and Google ML can lock you into their services package. MLeap allows you to take your models with you wherever you go.

模型属于你自己。你可以把 MLeap Bundle 带到任何地方。诸如 Microsoft Azure 和 Google ML Cloud 在内的平台会把你限制在他们提供的服务包里面，而 MLeap 允许你把模型带到任何地方去。 

### Spark, Scikit-learn and Tensorflow: One Runtime | Spark、Scikit-Learn 和 TensorFlow 的统一 Runtime 

Mixing and matching ML technologies becomes a simple task. Instead of requiring an entire team of developers to make research pipelines production ready, simply export to an MLeap Bundle and run your pipeline wherever it is needed.

混合多种机器学习技术在 MLeap 中变得如此简单。无需再去要求整个开发团队都去研究 Pipeline 相关产品，只需简单导出为 MLeap Bundle，就可以在任何需要的地方执行你的 Pipeline。 

Other benefits of a unified runtime:

通用 Runtime 的其他好处：

* Train different pieces of your pipeline using Spark, Scikit-learn or Tensorflow, then export them to one MLeap Bundle file and deploy it anywhere | 分别使用 Spark、Scikit-Learn 或者 TensorFlow 来训练 Pipeline 的其中一部分，然后将其导出成单一的的 MLeap Bundle 文件，并部署到任何地方
* If you're using Scikit for R&D, but Spark comes out with a better algorithm, you can export your Scikit ML pipeline to Spark, train the new model in Spark and then deploy to production using the MLeap runtime | 如果你正在使用 Scikit-Learn 做研发，但 Spark 提供了更好的算法，你可以把 Scikit-Learn ML Pipeline 转换成 Spark Pipeline，在 Spark 中训练得到新的模型，并使用 MLeap Runtime 将模型部署到生产环境中

<img src="assets/images/single-runtime.jpg" alt="Unified Runtime"/>

### Common Serialization | 通用序列化格式 

In addition to providing a useful execution engine, MLeap Bundles provide a common serialization format for a large set of ML feature extractors and algorithms that are able to be exported and imported across Spark, Scikit-learn, Tensorflow and MLeap. This means you can easily convert pipelines between these technologies depending on where you need to execute a pipeline.

除了提供一个实用的执行引擎之外，对于包含大量特征提取器和算法在内的 Pipeline，MLeap Bundle 提供了一套通用的序列化格式，允许用户在 Spark、Scikit-Learn、TensorFlow 以及 MLeap 之间相互进行格式转换。这意味着当你需要在特定环境执行 Pipeline 时，只需简单地把 Pipeline 转换为对应平台的格式即可。 

### Seamless Integrations | 无缝集成 

For the most part, we do not modify any internal code or require custom implementations of transformers in any Spark or Scikit-learn. For Tensorflow, we use as many built-in ops as we can and implement custom ops for MLeap when they do not exist. This means that code changes to your existing pipelines are minimal to get up and running with MLeap. For many use cases, no changes will be required and you can simply export to an MLeap Bundle or deploy to a Combust API server to start getting immediate use of your pipeline.

多半情况下，我们无需修改任何内部代码，或者要求你使用 Spark 或者 Scikit-Learn 来实现 Transformer。对于 TensorFlow，如果 MLeap 中不存在对应的 Op，我们也会尽可能使用已经内置的 Op 来做相应实现。这意味着如果想要使用和运行 MLeap，对原有 Pipeline 代码的改动是非常小的。而对于大多数使用场景，无需做任何代码修改，只需要把 Pipeline 导出成 MLeap Bundle，或者将其部署到一个 Combust API 服务上，就能立刻使用你的 Pipeline。 

### Open Source | 开源

MLeap is entirely open source. Our source code is available at [https://github.com/combust/mleap](https://github.com/combust/mleap). We also automate our tests and deploys with [travis ci](https://travis-ci.org/combust/mleap).

MLeap 完全开放源码，我们的源码可以从 [https://github.com/combust/mleap](https://github.com/combust/mleap) 获取。此外我们的自动化测试和部署放置在  [travis ci](https://travis-ci.org/combust/mleap) 上。 