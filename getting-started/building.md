# 从源码编译 MLeap

MLeap 作为一个开放项目被托管在 [Github](https://github.com/combust/mleap) 上。MLeap 的编译过程非常直接和简单，无需太多的第三方依赖。本章节介绍如果从源码编译 MLeap。

## 安装 SBT

[安装 SBT](http://www.scala-sbt.org/0.13/docs/Setup.html)，SBT 被广泛用于编译 Scala 项目。

## 编译 MLeap 核心模块

MLeap 的核心模块包含了除 TensorFlow 集成之外的所有子模块。考虑到 TensorFlow 的依赖比较难安装，我们没有将其包含在核心模块中。

### Clone GitHub 代码仓库

```bash
git clone https://github.com/combust/mleap.git
cd mleap
```

### 初始化 Git Submodule

MLeap 依赖于 Git Submodule 来管理所有的 ProtoBuf 数据定义，所以我们需要初始化和更新这些 Submodule。

```bash
git submodule init
git submodule update
```

### 编译 MLeap

MLeap 项目由很多子项目组成。这个项目由根 SBT 项目管理，这意味着我们只要执行一条 SBT 命令，就能编译所有的子项目。

```bash
sbt compile
```

### 运行测试

MLeap has extensive testing, including full parity tests between MLeap and Spark transformers.

MLeap 项目含有大量的测试，包括许多对 MLeap 和 Spark 的 Transformer 执行结果的比较校验测试。

```bash
sbt test
```

## 编译 TensorFlow 支持

 `mleap-tensorflo` 子模块的编译并不是全自动的，我们需要先编译 TensorFlow 并把 TensorFlow 的 Jar 包安装到本地的 Maven2 Repository 中。

### 编译 / 安装 TensorFlow

有非常多的教程介绍如何编译和安装 TensorFlow。

1. [Tensorflow](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/g3doc/get_started/os_setup.md)
2. [Tensorflow Java Bindings](https://github.com/tensorflow/tensorflow/tree/master/tensorflow/java).

### 编译 TensorFlow MLeap 模块

```bash
sbt mleap-tensorflow/compile
```

### 运行 TensorFlow 集成测试

```bash
TENSORFLOW_JNI=/path/to/tensorflow/library/folder/java sbt mleap-tensorflow/test
```
