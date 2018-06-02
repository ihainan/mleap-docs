## Jupyter 配置

我们推荐使用 Toree Kernel 来实现在 Jupyter 中运行 Spark。我们假设你已经安装了如下依赖：

1. Python 2.x
2. PIP
3. Docker（用于安装 Toree）

### 安装 Jupyter

```bash
virtualenv venv

source ./venv/bin/activate

pip install jupyter
```

### 构建和安装 Toree

Clone Toree 的 [github 仓库](https://github.com/apache/incubator-toree/blob/master/README.md)到你的本地目录。

在执行下一步操作之前，确保 Docker 正在运行。

```bash
cd incubator-toree
make release
cd dist/toree-pip
pip install .

SPARK_HOME=<path to spark> jupyter toree install
```

### 启动 Spark MLeap 集成 Notebook

最大限度减少错误的途径是通过直接修改内核来添加 MLeap 支持到你的项目中（或者创建一个新的内核来使用 Toree 和 Spark 2.0）

Kernel 配置文件的路径一般为 /usr/local/share/jupyter/kernels/apache_toree_scala/kernel.json。

编辑该文件，添加或者修改  `__TOREE_SPARK_OPTS__` 变量：

```bash
"__TOREE_SPARK_OPTS__": "--packages com.databricks:spark-avro_2.11:3.0.1,ml.combust.mleap:mleap-spark_2.11:0.9.0,"
```

另一个方法是使用 AddDeps 来添加依赖，但是可能会引起依赖冲突，因此需要自行承担相应的后果。

`%AddDeps ml.combust.mleap mleap-spark_2.11 0.9.0 --transitive`

### 启动 PySpark MLeap 集成 Notebook

首先需要完整走一遍上述所说的启动 Spark MLeap 集成 Notebook 的流程，然后添加如下内容到 `PYTHONPATH` 变量中。

```bash
    "PYTHONPATH": "/usr/local/spark-2.0.0-bin-hadoop2.7/python:/usr/local/spark-2.0.0-bin-hadoop2.7/python/lib/py4j-0.10.1-src.zip:/<git directory>/combust/combust-mleap/python",
```

无需直接修改  `kernel.json` 文件，只需要如[这里](../scikit-learn/index.md)所述实例化依赖库。