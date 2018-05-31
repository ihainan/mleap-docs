# Scikit Transformers Examples | Scikit-Learn Transformer 示例

Here we outline some more complicated transformers and transformers that require additional processing to work nicely with Pipelines and Feature Unions.

接下来我们列出了一些更为复杂的 Transformer，以及一些需要额外处理来更好兼容 Pipeline 和特征联合的 Transformer。

### Label Encoder

The `LabelEncoder` is synonymous with `StringIndexer` in Spark, however there are a couple of unique features of the scikit transformer that we need to account for:

在 Spark 中，`LabelEncoder`  和 `StringIndexer` 是等价的，但是在 Scikit-Learn 中，我们需要去考虑一些独有的特性：

1. `LabelEncoder` only opperates on a single feature at a time | `LabelEncoder` 一次只能作用于单个特征
2. The output of the `LabelEncoder` is a numpy array of shape (1,n) instead of (n,1), which is required for further processing like One-Hot-Encoding | `LabelEncoder` 的输出是一个 (1, n)，而非 (n, 1) 的 numpy 数组，因此还需要进行例如 One-Hot-Encoding 之类的进一步处理。

Here is what an example Pipeline looks like for a `LabelEncoder`

下面是一个 `LabelEncoder` 的示例 Pipeline。

```python
# Create a dataframe with some a categorical and a continuous feature
df = pd.DataFrame(np.array([ ['Alice', 32], ['Jack', 18], ['Bob',34]]), columns=['name', 'age'])

# Define our feature extractor
feature_extractor_tf = FeatureExtractor(input_scalars=['name'], 
                                        output_vector='name_continuous_feature', 
                                        output_vector_items=['name_label_encoded'])

# Label Encoder for x1 Label 
label_encoder_tf = LabelEncoder()
label_encoder_tf.mlinit(input_features = feature_extractor_tf.output_vector_items, output_features='name_label_le')

# Reshape the output of the LabelEncoder to N-by-1 array
reshape_le_tf = ReshapeArrayToN1()

# Create our pipeline object and initialize MLeap Serialization
le_pipeline = Pipeline([(feature_extractor_tf.name, feature_extractor_tf),
                        (label_encoder_tf.name, label_encoder_tf),
                        (reshape_le_tf.name, reshape_le_tf)
                        ])
le_pipeline.mlinit()

# Transform our DataFrame
le_pipeline.fit_transform(df)

# output
array([[0],
       [2],
       [1]])
```

Next step is to combine the label indexer with a `OneHotEncoder`

接下来我们来结合 `LabelIndexer` 和 `OneHotEncoder`。

### Scikit OneHotEncoder | Scikit-Learn 中的 OneHotEncoder

We'll continue the example above to demonstrate how the out-of-the-box Scikit OneHotEncoder works.

我们继续来看 Scikit-Learn 自带的 OneHotEncoder 是如何运作的：

```python
## Vector Assembler for x1 One Hot Encoder
one_hot_encoder_tf = OneHotEncoder(sparse=False) # Make sure to set sparse=False
one_hot_encoder_tf.mlinit(prior_tf=label_encoder_tf, output_features = '{}_one_hot_encoded'.format(label_encoder_tf.output_features))
#

# Construct our pipeline
one_hot_encoder_pipeline_x0 = Pipeline([
                                         (feature_extractor_tf.name, feature_extractor_tf),
                                         (label_encoder_tf.name, label_encoder_tf),
                                         (reshape_le_tf.name, reshape_le_tf),
                                         (one_hot_encoder_tf.name, one_hot_encoder_tf)
                                        ])

one_hot_encoder_pipeline_x0.mlinit()

# Execute our LabelEncoder + OneHotEncoder pipeline on our dataframe
one_hot_encoder_pipeline_x0.fit_transform(df)
matrix([[ 1.,  0.,  0.],
        [ 0.,  0.,  1.],
        [ 0.,  1.,  0.]])
```

One of the short-comings of Scikit's OneHotEncoder is that it's missing a `drop_last` functionality that's required in ML pipelines. 

Scikit-Learn 中的 OneHotEncoder 的一个缺点是其缺失了 ML Pipeline 所要求的 `drop_last` 功能。

MLeap comes with it's own OneHotEncoder that enables that function

MLeap 带来的 OneHotEncoder 则提供了这个功能。

### MLeap OneHotEncoder | MLeap 的 OneHotEncoder

Very similar to the Scikit OneHotEncoder, except we set an additional `drop_last` attribute.

类似于 Scikit-Learn 的 OneHotEncoder，但是我们设置了一个额外的 `drop_last` 属性。

```python
from mleap.sklearn.extensions.data import OneHotEncoder

## Vector Assembler for x1 One Hot Encoder
one_hot_encoder_tf = OneHotEncoder(sparse=False, drop_last=True) # Make sure to set sparse=False
one_hot_encoder_tf.mlinit(prior_tf=label_encoder_tf, output_features = '{}_one_hot_encoded'.format(label_encoder_tf.output_features))
#

# Construct our pipeline
one_hot_encoder_pipeline_x0 = Pipeline([
                                         (feature_extractor_tf.name, feature_extractor_tf),
                                         (label_encoder_tf.name, label_encoder_tf),
                                         (reshape_le_tf.name, reshape_le_tf),
                                         (one_hot_encoder_tf.name, one_hot_encoder_tf)
                                        ])

one_hot_encoder_pipeline_x0.mlinit()

# Execute our LabelEncoder + OneHotEncoder pipeline on our dataframe
one_hot_encoder_pipeline_x0.fit_transform(df)
matrix([[ 1.,  0.],
        [ 0.,  0.],
        [ 0.,  1.]])
```
