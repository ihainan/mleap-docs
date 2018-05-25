# Data Frames | Data Frame

Data Frames are used to store data during execution of an ML pipeline. They are similar to a SQL table in that they have a schema for storing the data types of every column and they have rows for storing the actual values.

Data Freame 用于 ML Pipline 执行期间的数据存储。类似于 SQL Table，它有 Schema，用于存储数据类型，有 Column，用于实际存储每一行的数据。

Spark, Scikit-learn, and MLeap all have their own version of a data frame. Tensorflow uses a graph of inputs and outputs to execute transformations, which is very easy to inteface with a data frame structure.

Spark、Scikit-Learn 和 MLeap 实现了各自版本的 Data Frame，TensorFlow 则使用了包含输入和输出的 Graph 来执行 Transformer，**因此也能够轻松获取到类似 Data Frame 的数据结构**。



## Spark Data Frames | Spark 的 Data Frame

Spark's data frames are optimized for distributed computation, making them excellent for processing large datasets. They are very heavy-weight, as they need to handle network failure scenarios, compilation of execution plans, redundancy, and many other requirements in a distributed context. Spark data frames offer a lot of functionality outside of ML pipelines, such as joining large datasets, mapping, reducing, SQL queries, etc.

Spark 的 Data Frame 针对分布式计算做了相应优化，以便能够处理大规模的数据集，**为此它们相对会比较重（Heavy-Weight）：它们需要处理网络失败问题，分布式上下文（Distributed Context）也需要有执行 Plan、保证数据冗余性等要求**。此外 Spark 的 Data Frame 提供了一系列 ML Pipline 之外的功能，比如结合两个大的数据集，做 map、reduce 和 SQL 查询等操作。

## Scikit-learn Data Frames | Scikit-Learn 的 Data Frame

Scikit-learn data frames are provided by [Pandas](http://pandas.pydata.org/) and [NumPy](http://www.numpy.org/). These are lightweight data structures, and offer quite a bit of the same functionality as Spark data frames, minus the distributed nature of Spark's data frames.

Scikit-Learn 的 Data Frame 由 [Pandas](http://pandas.pydata.org/) 和 [NumPy](http://www.numpy.org/) 提供。它们是相对轻量级的数据结构，提供了一系列类似于 Spark Data Frame 的操作，相比后者少了分布式计算和存储的负担。

## MLeap Data Frames: Leap Frames | MLeap 的 Data Frame：Leap Frame

Leap frames are very lightweight data structures and are meant to support very basic operations and ML transformations. Because of their simplicity, they are highly-optimized for use as a realtime prediction engine or small-batch predictions. Leap frames can be abstracted over Spark data frames, and so they do not lose their ability to act as an efficient batch-mode data store as well.

Leap Frame 是非常轻量级的数据结构，其提供了最基础的机器学习操作和 Transformer。因为它的简单性，Leap Frame 针对实时预测引擎和小规模数据预测做了高度优化。**Leap Frame 既能作为 Spark Data Frame 的数据抽象，又不会丧失其能够高效存储批处理模式数据的能力**。

### Example Leap Frame | Leap Frame 示例

Here is an example leap frame in JSON, it comes from our [AirBnB demo](https://github.com/combust/mleap-demo/blob/master/notebooks/airbnb-price-regression.ipynb):

以下是以 JSON 格式存储的 Leap Frame 的一个例子，来自我们的 [AirBnB demo](https://github.com/combust/mleap-demo/blob/master/notebooks/airbnb-price-regression.ipynb):

```json
{
  "schema": {
    "fields": [{
      "name": "state",
      "type": "string"
    }, {
      "name": "bathrooms",
      "type": "double"
    }, {
      "name": "square_feet",
      "type": "double"
    }, {
      "name": "bedrooms",
      "type": "double"
    }, {
      "name": "review_scores_rating",
      "type": "double"
    }, {
      "name": "room_type",
      "type": "string"
    }, {
      "name": "cancellation_policy",
      "type": "string"
    }]
  },
  "rows": [["NY", 2.0, 1250.0, 3.0, 50.0, "Entire home/apt", "strict"]]
}
```

## Tensorflow | TensorFlow

[Tensorflow](https://www.tensorflow.org/) does not have data frames like Spark, Scikit-learn and MLeap. Instead, Tensorflow relies on input nodes and output nodes, connected by a graph of transfomation operations. This paradigm is actually neatly compatible with data frames, as certain columns can be used to provide data for the input nodes, while output nodes can be placed in new columns of a data frame. Leap frames are specifically designed to be compatible with Tensorflow graphs, Spark data frames, and to a certain extent, Scikit-learn data frames.

[Tensorflow](https://www.tensorflow.org/) 本身并没有提供类似于 Spark、Scikit-Learn 和 MLeap 的 Data Frame 数据结构，但是考虑到 TensorFlow 依赖于输入节点和输出节点，节点由 Graph 中的 Transformer 操作相连接，**而 Data Frame 也是输入节点提供输入数据，输出数据被放在输出节点的新列中**，因此 TensorFlow 这个模型实际上能够很好地与我们的 Data Frame 概念相兼容。LEAP Frame 被设计成能够兼容 TensorFlow Graph、Spark Data Frame，以及在一定程度上兼容 Scikit-Learn 的 Data Frame。