# Zeppelin MLeap 配置

Zeppelin 默认已经能跑 Spark，我们不会在本节讲解如何配置一个 Zeppelin Notebook。一旦已经配置好 Zeppelin，那么添加 MLeap 依赖就会十分简单。

MLap 已经被托管在 Maven Central 上，所以你需要做的只是添加：

* `ml.combust.mleap:mleap-spark_2.11:0.14.0` 依赖以提供 Spark 序列化支持
* `ml.combust.mleap:mleap-runtime_2.11:0.14.0` 和 `ml.combust.mleap:mleap-core_2.11:0.14.0` 依赖以提供 MLeap Runtime 支持

完成之后，只需要添加如下 MLeap import 语句：

```scala
// MLeap/Bundle.ML Serialization Libraries
import ml.combust.mleap.spark.SparkSupport._
import resource._
import ml.combust.bundle.BundleFile
import org.apache.spark.ml.bundle.SparkBundleContext
```
