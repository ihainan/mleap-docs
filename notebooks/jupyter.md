## Jupyter Setup | Jupyter 配置

To run Spark within Jupyter we recommend using the Toree kernel. We are going to assume you already have the following installed:

我们推荐使用 Toree Kernel 来实现在 Jupyter 中运行 Spark。我们假设你已经安装了如下依赖：

1. Python 2.x | Python 2.x
2. PIP | PIP
3. Docker (required to install Toree) | Docker（用于安装 Toree）

### Install Jupyter | 安装 Jupyter

```bash
virtualenv venv

source ./venv/bin/activate

pip install jupyter
```

### Build and install Toree | 构建和安装 Toree

Clone master into your working directory from Toree's [github repo](https://github.com/apache/incubator-toree/blob/master/README.md).

Clone Toree 的 [github 仓库](https://github.com/apache/incubator-toree/blob/master/README.md) 到你的本地目录。

For this next step, you'll need to make sure that docker is running.

在执行下一步操作之前，确保 Docker 正在运行。

```bash
cd incubator-toree
make release
cd dist/toree-pip
pip install .

SPARK_HOME=<path to spark> jupyter toree install
```

### Launch Notebook with MLeap for Spark | 启动 Spark MLeap 集成 Notebook

The most error-proof way to add mleap to your project is to modify the kernel directly (or create a new one for Toree and Spark 2.0).

最大限度减少错误的途径是通过直接修改内核来添加 MLeap 支持到你的项目中（或者创建一个新的内核来使用 Toree 和 Spark 2.0）

Kernel config files are typically located in `/usr/local/share/jupyter/kernels/apache_toree_scala/kernel.json`

Kernel 配置文件的路径一般为 /usr/local/share/jupyter/kernels/apache_toree_scala/kernel.json。

Go ahead and add or modify `__TOREE_SPARK_OPTS__` like so:

编辑该文件，添加或者修改  `__TOREE_SPARK_OPTS__` 变量：

```bash
"__TOREE_SPARK_OPTS__": "--packages com.databricks:spark-avro_2.11:3.0.1,ml.combust.mleap:mleap-spark_2.11:0.9.0,"
```

An alternative way is to use AddDeps Magics, but we've run into dependency collisions, so do so at your own risk:

另一个方法是使用 AddDeps 来添加依赖，但是可能会引起依赖冲突，因此需要自行承担相应的后果。

`%AddDeps ml.combust.mleap mleap-spark_2.11 0.9.0 --transitive`

### Launch Notebook with MLeap for PySpark | 启动 PySpark MLeap 集成 Notebook

First go through the steps above for launching a notebook with MLeap for Spark, then add the following to `PYTHONPATH`

首先需要完整走一遍上述所说的启动 Spark MLeap 集成 Notebook 的流程，然后添加如下内容到 `PYTHONPATH` 变量中。

```bash
    "PYTHONPATH": "/usr/local/spark-2.0.0-bin-hadoop2.7/python:/usr/local/spark-2.0.0-bin-hadoop2.7/python/lib/py4j-0.10.1-src.zip:/<git directory>/combust/combust-mleap/python",
```

### Launch Notebook with MLeap for Scikit-Learn | 启动 Scikit-Learn MLeap 集成 Notebook

No need to modify the `kernel.json` directly, just instantiate the libraries like described [here](../scikit-learn/index.md).

无需直接修改  `kernel.json` 文件，只需要如 [这里](../scikit-learn/index.md) 所述实例化依赖库。