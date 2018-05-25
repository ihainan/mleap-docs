# Pipelines | Pipeline

Machine learning pipelines are series of transformers that execute on a data frame. They allow us to combine our feature transformations together with our actual predictive algorithms. Pipelines can be as simple as a single transformer or quite complex, involving hundreds of feature transformers and multiple predictive algorithms.

机器学习 Pipeline 由一系列执行于 Data Frame 的 Transformer 构成。Pipeline 允许我们结合多个特征 Transformer 和实际的预测算法在一起。Pipeline 既能简单到仅有单个 Transformer，也能复杂到包含成百个 Transformer 和多个预测算法。


# Simple Pipeline Example |  Pipeline 的一个简单例子

The diagram below shows a very simple pipeline that can be serialized to a bundle and then scored using MLeap Runtime. The ideas is that MLeap enables serialization and execution of transformers that operate on continuous and categorical features. A more complicated version of this pipeline may include dimension reduction transformers like PCA and feature selection tranformers like the Chi-Squared selector. 

下图演示了一个非常简单的 Pipeline 的例子，它能够被序列化成 Bundle 然后使用 MLeap Runtime 来进行评分。一个更复杂的 Pipeline 可能会包含降维 Transformer，比如 PCA 算法，或者是特征选择 Transformer，比如卡方选择器等。

<img src="../assets/images/simple-pipeline.jpg" alt="Very Simple Pipeline"/>


# Advanced Pipelines | 进阶 Pipeline

To see more advanced pipelines, please take a look at our [MLeap demo notebooks](https://github.com/combust/mleap-demo).

可以参见我们的 [MLeap demo notebooks](https://github.com/combust/mleap-demo) ，获得更多复杂 Pipeline 的例子。