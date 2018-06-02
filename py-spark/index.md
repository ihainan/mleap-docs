# MLeap PySpark 集成

MLeap 与 PySpark 的集成带来了如下特性：

* 将 Transformer 和 Pipeline 序列化为 Bundle.ML，或者将 Bundle.ML 反序列化回 Transformer 和 Pipeline。
* 额外的特征 Transformer 和模型（例如 SVM、OneVsRest、MapTransform 等）。
* 支持自定义 Transformer。

使用 MLeap 无需去修改你现在构建 Pipeline 的方式，因此本文后面重点会描述如何在 Pipeline 和 Bundle.ml 之间序列化和反序列化。你可以参见 [MLeap Runtime](../mleap-runtime/index.md) 章节了解如何脱离 Spark 执行你的 Pipeline。

# Pyspark 序列化

Spark 的序列化和反序列化操作基本与 MLeap 一致，唯一的区别是：在序列化和反序列化 Spark Pipeline 的时候，我们需要导入不同的隐式支持类（Implicit Support Classes）。

## 创建一个简单的 Spark Pipeline

```python
# Imports MLeap serialization functionality for PySpark
import mleap.pyspark
from mleap.pyspark.spark_support import SimpleSparkSerializer

# Import standard PySpark Transformers and packages
from pyspark.ml.feature import VectorAssembler, StandardScaler, OneHotEncoder, StringIndexer
from pyspark.ml import Pipeline, PipelineModel
from pyspark.sql import Row

# Create a test data frame
l = [('Alice', 1), ('Bob', 2)]
rdd = sc.parallelize(l)
Person = Row('name', 'age')
person = rdd.map(lambda r: Person(*r))
df2 = spark.createDataFrame(person)
df2.collect()

# Build a very simple pipeline using two transformers
string_indexer = StringIndexer(inputCol='name', outputCol='name_string_index')

feature_assembler = VectorAssembler(inputCols=[string_indexer.getOutputCol()], outputCol="features")

feature_pipeline = [string_indexer, feature_assembler]

featurePipeline = Pipeline(stages=feature_pipeline)

fittedPipeline = featurePipeline.fit(df2)
```


## 序列化为 Zip 文件

In order to serialize to a zip file, make sure the URI begins with `jar:file` and ends with a `.zip`.

为了序列化为 Zip 文件，需要确保 URL 以 `jar:file` 开头，以 `.zip` 结尾。

For example `jar:file:/tmp/mleap-bundle.zip`.

例如： `jar:file:/tmp/mleap-bundle.zip`.

### JSON 格式

```python
fittedPipeline.serializeToBundle("jar:file:/tmp/pyspark.example.zip", fittedPipeline.transform(df2))
```

### Protobuf 格式

即将支持。

## 反序列化

反序列化和序列化一样简单，你无需事先知道 MLeap Bundle 的序列化格式，唯一需要了解的，是这个包的路径。

### 反序列化 Zip Bundle

```python
deserializedPipeline = PipelineModel.deserializeFromBundle("jar:file:/tmp/pyspark.example.zip")
```