# MLeap 入门

MLeap Runtime 本身已经包含了所有用于执行和序列化 Pipeline 的依赖，但并没有集成用于训练 ML Pipeline 的包。因此开始使用 MLeap 之前，你需要手动添加 MLeap 相关依赖到你的项目当中。

## 添加 MLeap 依赖到你的项目中

MLeap 依赖包及其快照已经被托管在 Maven Central 之上了，所以 Maven 构建文件或者 SBT 都能轻松获取得到这些包。MLeap 目前分别基于 Scala 2.10 和 2.11 做了交叉编译，我们尝试去维护与 Spark 相兼容的 Scala 版本。

### 使用 SBT

```sbt
libraryDependencies += "ml.combust.mleap" %% "mleap-runtime" % "0.9.0"
```

### 使用 Maven

```pom
<dependency>
  <groupId>ml.combust.mleap</groupId>
  <artifactId>mleap-runtime_2.11</artifactId>
  <version>0.9.0</version>
</dependency>
```

如果想把依赖包打包成独立的 Jar 包的话，你需要使用 Maven Shade 插件，并在插件配置中添加如下的 transformer ，以确保 `reference.conf` 能够被正确合并，而非被其他文件覆盖。

```pom
<transformer implementation="org.apache.maven.plugins.shade.resource.AppendingTransformer">
	<resource>reference.conf</resource>
</transformer>
```

1. 参见[编译指南](./building.html)章节，从源码编译 MLeap。
2. 参见[核心概念](../core-concepts/)章节，从整体上了解 ML Pipeline。
3. 参见[基础用法](../basic/)章节（**译者注：文档已被原作者删除**），来实现 Leap Frame 的转换操作。

