# MLeap 问题诊断

## Must provide a sample dataset for the X transformer

你想要序列化的 Transformer 依赖于 Spark DataFrame 中的元数据，从而引发了这个错误。MLeap 需要访问这个元数据来保证 Transformer 能够被正确序列化，因此我们可以在 MLeap Bundle 中存储所有可能出现的值。这个方案需要提供一帧已经被 Spark ML Pipeline 成功转换的样例 DataFrame。

### 解决代码

```scala
// Use your Spark ML Pipeline to transform the Spark DataFrame
val transformedDataset = sparkTransformer.transform(sparkDataset)

// Create a custom SparkBundleContext and provide the transformed DataFrame
implicit val sbc = SparkBundleContext().withDataset(transformedDataset)

// Serialize the pipeline as you would normally
(for(bf <- managed(BundleFile(file))) yield {
  sparkTransformer.writeBundle.save(bf).get
}).tried.get
```
