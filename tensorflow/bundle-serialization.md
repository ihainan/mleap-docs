# TensorFlow Bundle 序列化

当序列化 TensorFlow Transformer 为 MLeap Bundle 的时候，我们会存储 TensorFlow Graph 为一个 Protobuf 文件，因此你需要先使用 `free_graph` 来冻结你的 TensorFlow Graph，以保证所有需要用于执行你的 Graph 的依赖都会被存在定义文件中。

## 示例 MLeap TensorFlow Bundle

从这里下载 [MLeap Bundle](../assets/bundles/tensorflow-model.zip) 的示例，这个例子使用 TensorFlow 来实现两个浮点数求和。

注意：由于 GitBook 不允许用户直接点击链接下载，请右键另存为。