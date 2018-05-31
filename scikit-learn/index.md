# MLeap Scikit-Learn Integration | MLeap Scikit-Learn 集成

MLeap provides serialization functionality to Scikit Pipelines, Feature Unions and Transformers to Bundle.Ml in such a way that we maintain parity between Scikit and Spark transformers' functionality. There are two main use-cases for MLeap-Scikit:

MLeap 为 Scikit-Learn 的 Pipeline、Transformer 以及特征联合添加了序列化为 Bundle 的能力，以保证 Scikit-Learn Transformer 和 Spark Transformer 功能上的一致。MLeap-Scikit 集成有两个主要的应用场景 ：

1. Serialize Scikit Pipelines and execute using MLeap Runtime | 序列化 Scikit-Learn Pipeline 并在 MLeap Runtime 中执行。
2. Serialize Scikit Pipelines and deserialize with Spark | 序列化 Scikit-Learn Pipeline，以及反序列化回 Spark Pipeline。

As mentioned earlier, MLeap Runtime is a scala-only library today and we plan to add Python bindings in the future. However, it is enough to be able to execute pipelines and models without the dependency on Scikit, and Numpy.

如我们之前所说，MLeap Runtime 目前仅提供了 Scala 的函数库，我们会在未来添加 Python 绑定支持，但目前已经完全能够不依赖于 Scikit-Learn 和 Numpy 去执行 Pipeline 了。

## Extending Scikit with MLeap | 使用 MLeap 扩展 Scikit-Learn

There are a couple of important differences in how scikit transformers work and how Spark transformers work:

Scikit-Learn Transformer 和 Spark Transformer 的运作存在以下几点重要区别：

1. Spark transformers all come with `name`, `op`, `inputCol`, and `outputCol` attributes, scikit does not | 所有的 Spark Transformer 都带有 `name`、`op`、`inputCol` 以及 `outputCol` 属性，而 Scikit-Learn 并没有（**ihainan: 不会全有，还是全部没有？**）。
2. Spark transformers can opperate on a vector, where as scikit operates on a n-dimensional arrays and matrices | Spark Transformer 可以作用于一个向量，而 Scikit-Learn Transformer 作用于一个 n 维数组和矩阵。
3. Spark, because it is written in Scala, makes it easy to add implicit functions and attributes, with scikit it is a bit trickier and requires use of setattr() | Spark 是由 Scala 语言编写而成，因此可以非常方便地添加隐式方法和属性，Scikit-Learn 则使用了一点小技巧，要求用户去调用 `setattr()` 方法。

Because of these additional complexities, there are a few paradigms we have to follow when extending scikit transformers with MLeap. 

由于这三个额外的差异，在使用 MLeap 来扩展 Scikit-Learn 的功能时，需要遵循一些规范。

First is we have to initialize each transformer to include:

首先，我们必须初始化每一个 Transformer，使其包含如下属性：

* Op: Unique `op` name - this is used as a link to Spark-based transformers (i.e. a Standard Scaler in scikit is the same as in Spark, so we have an op called `standard_scaler` to represent it) | Op: 唯一的 `op` 名字，用于链接基于 Spark 的 Transformer（例如，Scikit-Learn 和 Spark 中的 Standard Scaler 是等价的，因为我们需要用 `op` 值 `standard_scaler` 来表示这个 Transformer）。
* Name: A unique name for each transformer. For example, if you have multiple Standard Scaler objects, each needs to be assigned a unque name | Name: 为每一个 Transformer 提供唯一的 name。例如，如果你有多个 Standard Scaler 实例，那么每一个实例都需要提供独一无二的 name。
* Input Column: Strictly for serialization, we set what the input column is | Input Column：严格用在序列化过程，指明输入字段是什么。
* Output Column: Strictly for serialization, we set what the output column is | Output Column：严格用在序列化过程，指明输出字段是什么。

### Scikit Transformer and Pipeline with MLeap | MLeap 中使用 Scikit-Learn Transformer 和 Pipeline

Let's first initialize all of the required libraries

首先导入所有必要的依赖库：

```python
# Initialize MLeap libraries before Scikit/Pandas
import mleap.sklearn.preprocessing.data
import mleap.sklearn.pipeline
from mleap.sklearn.preprocessing.data import FeatureExtractor

# Import Scikit Transformer(s)
import pandas as pd
import numpy as np
from sklearn.preprocessing import StandardScaler
from sklearn.pipeline import Pipeline
```

Then let's create a test DataFrame in Pandas

然后使用 Pandas 创建一帧测试用的 DataFrame

```python
# Create a pandas DataFrame
df = pd.DataFrame(np.random.randn(10, 5), columns=['a', 'b', 'c', 'd', 'e'])
```

Let's define two transformers, a feature extractor that will extract only the features we want to scale and Standard Scaler, which will perform the standard normal scaling operation.

让我们定义两个 Transformer，一个为特征提取器（FeatureExtractor），用于提取我们想要进行归一化的字段，以及一个 Standard Scaler，用于执行标准归一化操作。

```python
# Initialize a FeatureExtractor, which subselects only the features we want
# to run the Standard Scaler against
input_features = ['a', 'c', 'd']
output_vector_name = 'unscaled_continuous_features' # Used only for serialization purposes
output_features = ["{}_scaled".format(x) for x in input_features]

feature_extractor_tf = FeatureExtractor(input_scalars=input_features,
                                        output_vector=output_vector_name,
                                        output_vector_items=output_features)


# Define the Standard Scaler as we normally would
standard_scaler_tf = StandardScaler(with_mean=True,
                                    with_std=True)

# Execute ML-Init to add the require attributes to the transformer object
# Op and Name will be initialized automatically
standard_scaler_tf.mlinit(prior_tf=feature_extractor_tf,
                          output_features='scaled_continuous_features')
```

Now that we have our transformers defined, we assemble them into a pipeline and execute it on our data frame

现在我们已经定义好了 Transformer，接下来需要把它们组装为 Pipeline，并对 Data Frame 执行转换操作。

```python
# Now let's create a small pipeline using the Feature Extractor and the Standard Scaler
standard_scaler_pipeline = Pipeline([(feature_extractor_tf.name, feature_extractor_tf),
                                     (standard_scaler_tf.name, standard_scaler_tf)])
standard_scaler_pipeline.mlinit()

# Now let's run it on our test DataFrame
standard_scaler_pipeline.fit_transform(df)

# Printed output
array([[ 0.2070446 ,  0.30612846, -0.91620529],
       [ 0.81463009, -0.26668287,  1.95663995],
       [-0.94079041, -0.18882131, -0.0462197 ],
       [-0.43931405,  0.13214763, -0.10700743],
       [ 0.43992551, -0.2985418 , -0.89093752],
       [-0.15391539, -2.20828471,  0.5361159 ],
       [-1.07689244,  1.61019861,  1.42868885],
       [ 0.87874789,  1.43146482, -0.44362038],
       [-1.60105094, -0.40130005, -0.10754886],
       [ 1.87161513, -0.11630878, -1.40990552]])
```

## Combining Transformers | 合并多个 Transformer

We just demonstrated how to apply a transformer to a set of features, but the output of that opperation is just a n-dimensional array that we would have to join back to our original data if we wanted to use it in say a regression model. Let's show how we can combine data from multiple transformers using Feature Unions.

我们演示了如何将 Transformer 作用于一系列特征之上，但是这个操作的输出仅仅只是一个 n 维的数组，如果我们需要把它提供如回归模型这样的模型使用的话，还需要将原始的数据与这个输出联合在一起。让我们看下如何使用特征联合来将多个 Transformer 的结果联合在一起。

First, go ahead and create another transformers, a MinMaxScaler on the remaining two features of the data frame:

首先，我们继续创建一个新的 Transformer：一个 MinMaxScaler，作用于 Data Frame 中剩下的两个特征之上。

```python
from sklearn.preprocessing import MinMaxScaler

input_features_min_max = ['b', 'e']
output_vector_name_min_max = 'unscaled_continuous_features_min_max' # Used only for serialization purposes
output_features_min_max = ["{}_min_maxscaled".format(x) for x in input_features_min_max]

feature_extractor_min_max_tf = FeatureExtractor(input_scalars=input_features_min_max,
                                                output_vector=output_vector_name_min_max,
                                                output_vector_items=output_features_min_max)


# Define the MinMaxScaler as we normally would
min_maxscaler_tf = MinMaxScaler()

# Execute ML-Init to add the require attributes to the transformer object
# Op and Name will be initialized automatically
min_maxscaler_tf.mlinit(prior_tf=feature_extractor_min_max_tf,
                          output_features='min_max_scaled_continuous_features')

# Assemble our MinMaxScaler Pipeline
min_max_scaler_pipeline = Pipeline([(feature_extractor_min_max_tf.name, feature_extractor_min_max_tf),
                                    (min_maxscaler_tf.name, min_maxscaler_tf)])
min_max_scaler_pipeline.mlinit()

# Now let's run it on our test DataFrame
min_max_scaler_pipeline.fit_transform(df)

array([[ 0.58433367,  0.72234095],
       [ 0.21145259,  0.72993807],
       [ 0.52661493,  0.59771784],
       [ 0.29403088,  0.19431993],
       [ 0.48838789,  1.        ],
       [ 1.        ,  0.46456522],
       [ 0.36402459,  0.43669119],
       [ 0.        ,  0.74182958],
       [ 0.60312285,  0.        ],
       [ 0.33707035,  0.39792128]])
```

Finaly, let's combine the two pipelines using a Feature Union. Note that you do not have to run the `fit` or `fit_transform` method on the pipeline before assembling the Feature Union.

最后，让我们使用特征联合来合并两个 Pipeline。需要注意的是在合并之前，你无需去执行 Pipeline 的 `fit` 或者 `fit_transform` 操作。

```python
# Import MLeap extension to Feature Unions
import mleap.sklearn.feature_union

# Import Feature Union
from sklearn.pipeline import FeatureUnion

feature_union = FeatureUnion([
        (standard_scaler_pipeline.name, standard_scaler_pipeline),
        (min_max_scaler_pipeline.name, min_max_scaler_pipeline)
        ])
feature_union.mlinit()

# Create pipeline out of the Feature Union
feature_union_pipeline = Pipeline([(feature_union.name, feature_union)])
feature_union_pipeline.mlinit()

# Execute it on our data frame
feature_union_pipeline.fit_transform(df)

array([[ 0.2070446 ,  0.30612846, -0.91620529,  0.58433367,  0.72234095],
       [ 0.81463009, -0.26668287,  1.95663995,  0.21145259,  0.72993807],
       [-0.94079041, -0.18882131, -0.0462197 ,  0.52661493,  0.59771784],
       [-0.43931405,  0.13214763, -0.10700743,  0.29403088,  0.19431993],
       [ 0.43992551, -0.2985418 , -0.89093752,  0.48838789,  1.        ],
       [-0.15391539, -2.20828471,  0.5361159 ,  1.        ,  0.46456522],
       [-1.07689244,  1.61019861,  1.42868885,  0.36402459,  0.43669119],
       [ 0.87874789,  1.43146482, -0.44362038,  0.        ,  0.74182958],
       [-1.60105094, -0.40130005, -0.10754886,  0.60312285,  0.        ],
       [ 1.87161513, -0.11630878, -1.40990552,  0.33707035,  0.39792128]])
```

## Serialize to Zip File | 序列化为 ZIP 文件

In order to serialize to a zip file, make sure the URI begins with `jar:file` and ends with a `.zip`.

为了序列化为 Zip 文件，需要确保 URL 以 `jar:file` 开头，以 `.zip` 结尾。

For example `jar:file:/tmp/mleap-bundle.zip`.

Note that you do have to fit your pipeline before serializing.

例如： `jar:file:/tmp/mleap-bundle.zip`.

### JSON Format | JSON 格式

Setting `init=True` tells the serializer that we are creating a bundle instead of just serializing the transformer.

设置 `init=Ture`告知序列化器我们创建一个 Bundle，而非仅仅只是序列化 Transformer。

```python
feature_union_pipeline.serialize_to_bundle('/tmp', 'mleap-bundle', init=True)
```

### Protobuf Format | Protobuf 格式

Coming Soon

即将支持

### Deserializing | 反序列化

Coming Soon

即将支持

## Demos | Demos

Complete demos available on github that demonstrates full usage of Transformers, Pipelines, Feature Unions and serialization.

完整的 Demo 可从 Github 获取，Demo 展示了 Transformer、Pipeline、特征联合以及序列化的完整用法。