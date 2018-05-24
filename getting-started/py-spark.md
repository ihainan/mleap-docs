1. # Getting Started with PySpark | PySpark 集成入门
   MLeap PySpark integration provides serialization of PySpark-trained ML pipelines to [MLeap Bundles](../mleap-bundle/). MLeap also provides several extensions to Spark, including enhanced one hot encoding and one vs rest models. Unlike Mleap<>Spark integration, MLeap doesn't yet provide PySpark integration with [Spark Extensions transformers.](../core-concepts/transformers/support.md#extensions)

   MLeap Spark 的集成允许用户将 Spark 训练得到的 ML Pipeline 序列化为 [MLeap Bundle](../mleap-bundle/)，此外，MLeap 还进一步扩展了 Spark 的原生功能，增强了包括 One Hot Encoding、One vs Rest 在内的模型。但与 MLeap Spark 的集成不同，MLeap 目前尚未提供 PySpark 与 [Spark Extensions Transformer](../core-concepts/transformers/support.md#extensions) 的集成。

   ## Adding MLeap Spark to Your Project | 添加 MLeap Spark 依赖到你的项目中

   Before adding MLeap Pyspark to your project, you first have to compile and add [MLeap Spark](./spark.md).

   在添加 MLeap PySpark 依赖之前，你首先应该添加 [MLeap Spark](./spark.md) 依赖到项目中。

   

   MLeap PySpark is available in the [combust/mleap](https://github.com/combust/mleap) github repository in the python package.

   MLeap PySpark 依赖包可以从 [combust/mleap](https://github.com/combust/mleap) 仓库的 Python 包中获取。

   

   To add MLeap to your PySpark project, just clone the git repo, add the `mleap/pyhton` path, and import `mleap.pyspark`

   只需要克隆这个 Git Repo，添加 `mleap/pyhton` 目录到 Python 的搜索路径中，并在代码里导入 `mleap.pyspark` 包，即可集成 MLeap 到你的 PySpark 项目中。


   ```bash
   git clone git@github.com:combust/mleap.git
   ```

   Then in your python environment do:

   随后在你的 Python 环境中：

   ```python
   import sys
   sys.path.append('<git directory>/mleap/python')
   
   import mleap.pyspark 
   ```

   Note: the import of `mleap.pyspark` needs to happen before any other PySpark libraries are imported.

   注意：`mleap.pyspark` 包的导入操作需要在导入其他 PySpark 库之前执行。

   Note: If you are working from a notebook environment, be sure to take a look at instructions of how to set up MLeap PySpark with:
   * [Jupyter](../notebooks/jupyter.md)
   * [Zeppelin](../notebooks/zeppelin.md)
   * [Databricks](../notebooks/databricks.md)

   注意：如果你使用的是 Notebook 环境，请确保在配置 MLeap PySpark 之前阅读过相应的指南教程：
   * [Jupyter](../notebooks/jupyter.md)
   * [Zeppelin](../notebooks/zeppelin.md)
   * [Databricks](../notebooks/databricks.md)

   ## Using PIP | 使用 PIP
   Alternatively, there is PIP support for PySpark available under: https://pypi.python.org/pypi/mleap.

   此外，依赖包也可通过 PIP 从 https://pypi.python.org/pypi/mleap 获取。

   

   To use MLeap extensions to PySpark:

   要使用 PySpark 对应的 MLeap Extension：

   1. See [build instructions](./building.html) to build MLeap from source. | 参见 [编译指南](./building.html) 章节，从源码编译 MLeap。
   2. See [core concepts](../core-concepts/) for an overview of ML pipelines. | 参见  [核心概念](../core-concepts/) 章节，从整体上了解 ML Pipeline。
   3. See [Spark documentation](http://spark.apache.org/docs/latest/ml-guide.html) to learn how to train ML pipelines in Spark. | 参见 [Spark 文档](http://spark.apache.org/docs/latest/ml-guide.html)，学习如何在 Spark 环境中训练 ML Pipeline。
   4. See [Demo notebook](https://github.com/combust/mleap-demo/blob/master/notebooks/PySpark%20-%20AirBnb.ipynb) on how to use PySpark and MLeap to serialize your pipeline to Bundle.ml. | 参见 [Demo notebooks](https://github.com/combust/mleap-demo/tree/master/notebooks) 章节，了解如何集成 PySpark 和 MLeap 来实现序列化 Pipeline 为 Bundle.ML。

