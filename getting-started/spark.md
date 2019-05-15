# Spark 集成入门
MLeap Spark 的集成允许用户将 Spark 训练得到的 ML Pipeline 序列化为 [MLeap Bundle](../mleap-bundle/)（**译者注：文档已被原作者删除**），此外，MLeap 还进一步扩展了 Spark 的原生功能，增强了包括 One Hot Encoding 模型、One vs Rest 模型以及一元 / 二元数学运算转换在内的模型和算法。

## 添加 MLeap Spark 依赖到你的项目中
MLeap 依赖包及其快照已经被托管在 Maven Central 之上了，所以 Maven 构建文件或者 SBT 都能轻松获取得到这些包。MLeap 目前分别基于 Scala 2.10 和 2.11 做了交叉编译，我们尝试去维护与 Spark 相兼容的 Scala 版本。

### 使用 SBT
```sbt
libraryDependencies += "ml.combust.mleap" %% "mleap-spark" % "0.14.0"
```

想在 Spark 中使用 MLeap Extension 的话：

```sbt
libraryDependencies += "ml.combust.mleap" %% "mleap-spark-extension" % "0.14.0"
```

### 使用 Maven

```pom
<dependency>
  <groupId>ml.combust.mleap</groupId>
  <artifactId>mleap-spark_2.11</artifactId>
  <version>0.14.0</version>
</dependency>
```

想在 Spark 中使用 MLeap Extension 的话：

```pom
<dependency>
  <groupId>ml.combust.mleap</groupId>
  <artifactId>mleap-spark-extension_2.11</artifactId>
  <version>0.14.0</version>
</dependency>
```

1. 参见[编译指南](./building.html)章节，从源码编译 MLeap
2. 参见[核心概念](../core-concepts/)章节，从整体上了解 ML Pipeline。
3. 参见 [Spark 文档](http://spark.apache.org/docs/latest/ml-guide.html)，学习如何使用 Spark 来训练 ML Pipeline。
4. 参见 [Demo notebooks](https://github.com/combust/mleap-demo/tree/master/notebooks) 章节，了解如何集成 PySpark 和 MLeap 来实现序列化 Pipeline 为 Bundle.ML，以及使用 MLeap 来进行评分。

