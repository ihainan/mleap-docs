# Building MLeap From Source / 从源码编译 MLeap

MLeap is hosted on [Github](https://github.com/combust/mleap) as a public project. Building MLeap is very straightforward and has very few dependencies. Here are instructions for building from source.

MLeap 作为一个公共项目，托管在 [Github](https://github.com/combust/mleap) 上。MLeap 的编译流程非常直接简单，无需太多的第三方依赖。本章节介绍如果从源码编译 MLeap。

## Install SBT | 安装 SBT

[Install Simple Build Tool](http://www.scala-sbt.org/0.13/docs/Setup.html), which is used to build many Scala projects.

[安装 SBT](http://www.scala-sbt.org/0.13/docs/Setup.html)，SBT 被广泛用于编译 Scala 项目。

## Compiling Core MLeap | 编译 MLeap 核心模块

The core of MLeap includes every submodule except for Tensorflow integration. We do not include Tensorflow in the core build because its dependencies can be difficult to install.

MLeap 的核心模块包含了除 TensorFlow 集成之外的所有子模块。TensorFlow 的依赖比较难安装，所以我们没有将其包含在核心模块中。

### Clone the Github Repository | 克隆 GitHub 代码仓库

```bash
git clone https://github.com/combust/mleap.git
cd mleap
```

### Initialize Git Submodules | 初始化 Git Submodule

MLeap depends on a git submodule for all of the protobuf definitions, so we need to initialize and update them.

MLeap 依赖于 Git Submodule 来管理所有的 ProtoBuf 数据定义，所以我们需要初始化和更新这些 Submodule。

```bash
git submodule init
git submodule update
```

### Compile MLeap | 编译 MLeap

There are many submodules that make up the MLeap project. All of them are aggregated to the root SBT project. This means running a command from SBT will cause the command to be run on all subprojects.

MLeap 项目由很多子项目组成。这个项目由根 SBT 项目管理，这意味着我们只要执行一条 SBT 命令，就能编译所有的子项目。

```bash
sbt compile
```

### Run the Tests | 运行测试

MLeap has extensive testing, including full parity tests between MLeap and Spark transformers.

MLeap 项目有着大量的测试，**包括许多对 MLeap 和 Spark 的 Transformer 执行结果的比较校验测试**。

```bash
sbt test
```

## Compiling Tensorflow Support | 编译 TensorFlow 支持

Compiling the `mleap-tensorflow` submodule does not happen automatically. Instead, we first need to compile Tensorflow and install the Tensorflow Java jar to our local maven2 repository.

 `mleap-tensorflo` 子模块的编译并不是全自动的，我们需要先编译 TensorFlow 并把 TensorFlow 的 Jar 包安装到本地的 Maven2 Repository 中。

### Compile/Install Tensorflow | 编译 / 安装 TensorFlow

Tensorflow has a great set of instructions for compiling and installing.

有大量的教程介绍如何编译和安装 TensorFlow。

1. [Tensorflow](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/g3doc/get_started/os_setup.md)
2. [Tensorflow Java Bindings](https://github.com/tensorflow/tensorflow/tree/master/tensorflow/java).

### Build Tensorflow MLeap Module | 编译 TensorFlow MLeap 模块

```bash
sbt mleap-tensorflow/compile
```

### Run Tensorflow Integration Tests | 运行 TensorFlow 集成测试

```bash
TENSORFLOW_JNI=/path/to/tensorflow/library/folder/java sbt mleap-tensorflow/test
```
