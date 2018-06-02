# MLeap Runtime

MLeap Runtime 是一个轻量级的 Pipeline 执行引擎，它有如下特征：

1. **Leap Frame 支持所有常见的数据类型和自定义数据类型。**
2. 目前支持 Spark 所有的 Transformer，以及 MLeap 添加的多个扩展 Transformer。
3. 能够基于 Transformer 轻松构建得到 Pipeline。
4. **与 MLeap Bundle 的完整集成，MLeap Runtime 为 MLeap Bundle 以及想要自己实现序列化方法的用户都提供了相应的参考实现**。
5. **Leap Frame 的序列化格式能够轻松通过网络传输**。
6. [BLAS](https://github.com/scalanlp/breeze) 提供了一个非常快的线性代数系统。

参见我们的 [MLeap Runtime 用法章节](../mleap-runtime/index.md)来进一步了解如何在应用程序中使用 MLeap Runtime。