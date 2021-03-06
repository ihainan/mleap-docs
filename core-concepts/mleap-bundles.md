# MLeap Bundle

MLeap Bundle 是一个基于图、跨平台的文件格式，它可以用于序列化和反序列化：

1. 机器学习 Data Pipeline - 所有基于 Transformer 的 Data Pipeline。
2. 算法（回归，基于树的模型、贝叶斯模型、神经网络、聚类）。

Bundle 的存在使得你能够轻松分享训练得到的 Pipeline，只需要生成一个 Bundle 文件然后将它通过邮件发给你的同事，或者自己去浏览 Pipeline 和算法的元数据信息。

Bundle 的部署也非常简单，只需要导出你的 Bundle，把它加载到你的 Spark、Scikit-Learn，又或者是你基于 MLeap 开发的应用程序中。

## MLeap Bundle 的功能特征

1. 序列化到一个目录或者是一个 Zip 包中。
2. Bundle 所有文件都是 JSON 或者 Prortobuf 格式。
3. 可以序列化成纯粹的 JSON / Protobuf 格式，或者是混合两种文件格式。
4. 高可扩展性，易于集成新的 Transformer。

## Spark、Scikit-Learn 和 TrnsorFlow 的统一格式

MLeap Bundle 为 Spark、Scikit-Learn 和 TensorFlow 提供了一种统一的序列化格式。例如，Standard Scaler trasnformer (TensorFlow 中的 `tf.random_normal_initializer`) 在所有三个平台的表现都是一样的，因此理论上它可以在这三个平台中任意地进行转换和互换。

<img src="../assets/images/common-serialization.jpg" alt="Common Serialization"/>

## Bundle 文件结构

Bundle 的根目录下面会有一个 `bundle.json` 文件，它包含了 Bundle 序列化相关的基础元数据。Bundle 里面会有一个 `root/` 目录，包含了 ML Pipeline 的根 Transformer，根 Transformer 可以是 MLeap 支持的任意类型的 Transformer，但一般来说会是一个 `Pipeline` transformer。

来看一个 MLeap Bundle 的真实例子。这个 Pipeline 包含了一个字符串索引器，用于将字符串索引成分类特征，紧接着对数据进行 One-Hot 编码，并把结果合并成一个特征向量，最后对向量执行线性回归算法。这个 Bundle 的目录结构是这样的：

```
├── bundle.json
└── root
    ├── linReg_7a946be681a8.node
    │   ├── model.json
    │   └── node.json
    ├── model.json
    ├── node.json
    ├── oneHot_4b815730d602.node
    │   ├── model.json
    │   └── node.json
    ├── strIdx_ac9c3f9c6d3a.node
    │   ├── model.json
    │   └── node.json
    └── vecAssembler_9eb71026cd11.node
        ├── model.json
        └── node.json
```

### bundle.json | bundle.json

```
{
  "uid": "7b4eaab4-7d84-4f52-9351-5de98f9d5d04",
  "name": "pipeline_43ec54dff5b2",
  "timestamp": "2017-09-03T17:41:25.206",
  "format": "json",
  "version": "0.14.0"
}
```

1. `uid` 是一个自动生成的 UUID 字符串，用做 Bundle 的唯一标示。
2. `name` 是根 Transformer 的名字。
3. `foramt` 是序列化这个 bundle 所使用的序列化格式。
4. `version` 是序列化这个 bundle 所使用的 MLeap 的版本。
5. `timestamp` 是 bundle 创建的时间。

### model.json

对于 Pipeline：

```
{
  "op": "pipeline",
  "attributes": {
  "nodes": {
    "type": "list",
    "string": ["strIdx_ac9c3f9c6d3a", "oneHot_4b815730d602", "vecAssembler_9eb71026cd11", "linReg_7a946be681a8"]
  }
}

```

对于线性回归：

```
{
  "op": "linear_regression",
  "attributes": {
    "coefficients": {
      "double": [7274.194347379634, 4326.995162668048, 9341.604695180558, 1691.794448740186, 2162.2199731255423, 2342.150297286721, 0.18287261938061752],
      "shape": {
        "dimensions": [{
          "size": 7,
          "name": ""
        }]
      },
      "type": "tensor"
    },
    "intercept": {
      "double": 8085.6026142683095
    }
}
```

1. `op` 指明即将被执行的操作，对于每个 MLeap 所支持的 Transformer 都会有一个 op 名字。
2. `attributes` 包含操作被执行所需要的参数值。

### node.json

对于 One-Hot 编码器：

```
{
  "name": "oneHot_4b815730d602",
  "shape": {
    "inputs": [{
      "name": "fico_index",
      "port": "input"
    }],
    "outputs": [{
      "name": "fico",
      "port": "output"
    }]
  }
}
```

1. `name` 指明了执行图（Execution Graph）中节点的名字。
2. `shape` 指明节点的输入和输出，以及数据如何在内部被 Transformer 使用。

这个例子中，`fico_index` 字段被用作 One-Hot 编码器的输入字段，而 `fico` 会作为结果字段。

## MLeap Bundle 实例

这里提供了一些序列化 Bundle 文件的例子。它们可能不都是有意义的 Pipeline，但是能用于解释这些 Bundle 文件都是怎么样的。Pipeline 是在运行 Spark 对等校验（Parity Tests）时候生成的，以保证 MLeap 的 Transformer 和 Spark 的 Transformer 能够准确生成相同的结果。

[MLeap/Spark Parity Bundle Examples](../assets/bundles/spark-parity.zip)

注意：由于 GitBook 不允许用户直接点击链接下载，请右键另存为。
