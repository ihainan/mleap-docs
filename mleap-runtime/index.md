# 基础用法

MLeap Runtime 是一个轻量级但被高度优化过的机器学习 Pipeline 执行引擎。MLeap runtime 的目标是提供一个能够达到生产级别，适用于常用机器学习框架的的服务 / 评分架构，它的使用无需再依赖于原框架的核心函数库，这就意味着：

* 执行 Spakr ML Pipeline，而无需依赖于 Spark Context，分布式 Data Frame，以及耗时的执行方案（Execution Plan）
* 执行 Scikit-Learn Pipeline，而无需依赖于 numpy、pandas、scipy 或者其他用于训练的函数库 。  

MLeap 致力于尽可能简单易用，基于这个目标，我们有如下的设计原则：  

1. 尽可能遵循单子编程（Monadic Programming）设计模式
2. 通过自动化资源管理来保持简洁性
3. 汇报用户在用法上的错误
4. 基于 Leap Frame 和 Transformer 的操作要尽可能自然和简单

我们使用了大量的技巧和方法使得 API 能够对用户友好，你无需担心内部实现细节，除非希望去了解它。  

让我们开始了解一些 MLeap 的基础用法，比如创建一帧 Leap Frame，修改它，以及最后使用 Transformer 和 Pipeline 来完成整个 ML pipeline 的转换过程。  