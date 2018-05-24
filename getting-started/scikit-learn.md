1. # Getting Started with Scikit-Learn | Scikit-Learn 集成入门
   MLeap Scikit-Learn integration provides serialization of Scikit-trained ML pipelines to [MLeap Bundles](../mleap-bundle/). MLeap also provides several extensions to Scikit, including [MLeap extensions transformers.](../core concepts/transformers/support.md#extensions)

   MLeap Scikit-Learn 的集成允许用户将 Scikit-Learn 训练得到的 ML Pipeline 序列化为 MLeap Bundle，此外，MLeap 还提供了包括 [MLeap Extensions Transformers](../core concepts/transformers/support.md#extensions) 在内的额外扩展功能。

   

   MLeap Scikit integration works by adding Bundle. ML serialization to Transformers, Pipelines and Feature Unions.  It is important to note that because the core execution engine is in scala and is modeled after Spark transformers,  that only supported transformers are those that are available in Spark and any libraries that extend Spark. For a full list of supported scikit-transformers see the [supp.orted transformers page](../core-concepts/transformers/support.md) or if you'd like support for custom transformers see the [custom transformers]() section.

   MLeap Scikit 的集成基于添加 Bundle 来实现。ML 被序列化为 Transformer、Pipeline 以及特征单元。必须要注意的是，因为核心执行引擎（Core Execution Engine）基于 Scala 语言来编写、基于已有 Spark Transformer 来做的实现，因此 MLeap 支持的 Transformer 为那些原先就存在于 Spark 或者扩展自 Spark 的 Transformer。完整的 Scikit-Learn Transformer 支持列表参见 [支持的 Transformer](../core-concepts/transformers/support.md) 章节，或者你希望自己实现 Transformer 的话，可以参考 [自定义 Transformer](../mleap-runtime/custom-transformer.md) 章节（译者注：原文这里少了链接。）。

   ## Adding MLeap Scikit to Your Project | 集成 MLeap Scikit 到你的项目中

   To add MLeap to your Scikit project, just pip install MLeap

   想要集成 MLeap 到 Scikit Learn 项目中，只需要通过 PIP 安装 MLeap。

   ```bash
   pip install mleap
   ```

   Then in your python environment import MLeap extensions to any Scikit transformers you plan to serialize:

   **然后在你的 Python 环境中，针对你计划要序列化的 Scikit Transformer，导入对应的 MLeap Extensions。**

   ```python
   # Extends Bundle.ML Serialization for Pipelines
   import mleap.sklearn.pipeline
   
   # Extends Bundle.ML Serialization for Feature Unions
   import mleap.sklearn.feature_union
   
   # Extends Bundle.ML Serialization for Base Transformers (i.e. LabelEncoder, Standard Scaler)
   import mleap.sklearn.preprocessing.data
   
   # Extends Bundle.ML Serialization for Linear Regression Models
   import mleap.sklearn.base
   
   # Extends Bundle.ML Serialization for Logistic Regression
   import mleap.sklearn.logistic
   
   # Extends Bundle.ML Serialization for Random Forest Regressor
   from mleap.sklearn.ensemble import forest
   ```


   For more information on how to use MLeap extensions to Scikit:

   更多关于如何集成 MLeap Extensions 到 Scikit Learn 中的相关资料：

   1. See [core concepts](../core-concepts/) for an overview of ML pipelines. |  参见  [核心概念](../core-concepts/) 章节，从整体上了解 ML Pipeline。
   2. Detailed guide to [MLeap and Scikit-Learn](../scikit-learn/index.md) | 参见 [MLeap 和 Scikit-Learn](../scikit-learn/index.md) 章节，了解更多具体细节。
   3. See [Scikit-learn documentation](http://scikit-learn.org/stable/modules/generated/sklearn.pipeline.Pipeline.html) to learn how to train ML pipelines in Python. | 参见 [Scikit-learn 文档](http://scikit-learn.org/stable/modules/generated/sklearn.pipeline.Pipeline.html)，学习如何在 Python 环境中训练 ML Pipeline。
   4. See Scikit-learn documentation on how to use [Feature Unions](http://scikit-learn.org/stable/modules/generated/sklearn.pipeline.FeatureUnion.html) with pipelines | 参见 Scikit-Learn 文档，了解如何在 Pipeline 中使用 [特征联合](http://scikit-learn.org/stable/modules/generated/sklearn.pipeline.FeatureUnion.html)。
   5. See [Demo notebook](https://github.com/combust/mleap-demo/) on how to use Scikit and MLeap to serialize your pipeline to Bundle.ml. | 参见 [Demo notebooks](https://github.com/combust/mleap-demo/tree/master/notebooks) 章节，了解如何集成 PySpark 和 MLeap 来实现序列化 Pipeline 为 Bundle.ML。
   6. [Learn](../basic/transofrm-leap-frame.md) how to transform a [DataFrame](../core-concepts/data-frames/index.md) using MLeap. | [学习](../basic/transofrm-leap-frame.md) 如何使用 MLeap 转换 [DataFrame](../core-concepts/data-frames/index.md)。

