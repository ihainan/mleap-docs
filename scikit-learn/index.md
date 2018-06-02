# MLeap Scikit-Learn 集成

MLeap 为 Scikit-Learn 的 Pipeline、Transformer 以及特征联合添加了序列化为 Bundle 的能力，以保证 Scikit-Learn Transformer 和 Spark Transformer 功能上的一致。MLeap-Scikit 集成有两个主要的应用场景 ：

1. 序列化 Scikit-Learn Pipeline 并在 MLeap Runtime 中执行。
2. 序列化 Scikit-Learn Pipeline，以及反序列化回 Spark Pipeline。

如我们之前所说，MLeap Runtime 目前仅提供了 Scala 的函数库，我们会在未来添加 Python 绑定支持，但目前已经完全能够不依赖于 Scikit-Learn 和 Numpy 去执行 Pipeline 了。

## 使用 MLeap 扩展 Scikit-Learn

Scikit-Learn Transformer 和 Spark Transformer 的运作存在以下几点重要区别：

1. 所有的 Spark Transformer 都带有 `name`、`op`、`inputCol` 以及 `outputCol` 属性，而 Scikit-Learn 并没有（**ihainan: 不会全有，还是全部没有？**）。
2. Spark Transformer 可以作用于一个向量，而 Scikit-Learn Transformer 作用于一个 n 维数组和矩阵。
3. Spark 是由 Scala 语言编写而成，因此可以非常方便地添加隐式方法和属性，Scikit-Learn 则使用了一点小技巧，要求用户去调用 `setattr()` 方法。

由于这三个额外的差异，在使用 MLeap 来扩展 Scikit-Learn 的功能时，需要遵循一些规范。

首先，我们必须初始化每一个 Transformer，使其包含如下属性：

* Op: 唯一的 `op` 名字，用于链接基于 Spark 的 Transformer（例如，Scikit-Learn 和 Spark 中的 Standard Scaler 是等价的，因为我们需要用 `op` 值 `standard_scaler` 来表示这个 Transformer）。
* Name: 为每一个 Transformer 提供唯一的 name。例如，如果你有多个 Standard Scaler 实例，那么每一个实例都需要提供独一无二的 name。
* Input Column：严格用在序列化过程，指明输入字段是什么。
* Output Column：严格用在序列化过程，指明输出字段是什么。

### MLeap 中使用 Scikit-Learn Transformer 和 Pipeline

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

然后使用 Pandas 创建一帧测试用的 DataFrame

```python
# Create a pandas DataFrame
df = pd.DataFrame(np.random.randn(10, 5), columns=['a', 'b', 'c', 'd', 'e'])
```

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

## 合并多个 Transformer

我们演示了如何将 Transformer 作用于一系列特征之上，但是这个操作的输出仅仅只是一个 n 维的数组，如果我们需要把它提供如回归模型这样的模型使用的话，还需要将原始的数据与这个输出联合在一起。让我们看下如何使用特征联合来将多个 Transformer 的结果联合在一起。

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

## 序列化为 ZIP 文件

为了序列化为 Zip 文件，需要确保 URL 以 `jar:file` 开头，以 `.zip` 结尾。

For example `jar:file:/tmp/mleap-bundle.zip`.

例如： `jar:file:/tmp/mleap-bundle.zip`.

### JSON 格式

设置 `init=Ture`告知序列化器我们创建一个 Bundle，而非仅仅只是序列化 Transformer。

```python
feature_union_pipeline.serialize_to_bundle('/tmp', 'mleap-bundle', init=True)
```

### Protobuf 格式

即将支持。

### 反序列化

即将支持。

## Demos

完整的 Demo 可从 Github 获取，Demo 展示了 Transformer、Pipeline、特征联合以及序列化的完整用法。