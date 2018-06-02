# 支持的 Transformer

以下是 MLeap 支持的所有 Transformer。

注意：TensorFlow 的相关支持情况并没有被列在这里，但你可以[在一个 MLeap Transformer Pipline 中插入任意 TensorFlow Graph](../../integration/tensorflow/usage.html)。

## 特征相关

| Transformer| Spark | MLeap | Scikit-Learn | TensorFlow |
|---|:---:|:---:|:---:|:---:|
| Binarizer | x | x | x | |
| BucketedRandomProjectionLSH | x | x | | |
| Bucketizer       | x | x |  | |
| ChiSqSelector | x | x | | |
| CountVectorizer       | x | x | | |
| DCT | x | x | | |
| ElementwiseProduct | x | x | x | |
| HashingTermFrequency | x | x | x | |
| IDF | x | x | | |
| Imputer   | x | x | x | |
| Interaction | x | x | x | |
| MaxAbsScaler | x | x | | |
| MinHashLSH | x | x | | |
| MinMaxScaler | x | x | x |  |
| Ngram | x | x | | |
| Normalizer | x | x | | |
| OneHotEncoder | x | x | x |
| PCA | x | x | x | |
| QuantileDiscretizer | x | x | | |
| PolynomialExpansion | x | x | x | |
| ReverseStringIndexer | x | x | x | |
| StandardScaler | x | x | x | |
| StopWordsRemover | x | x | | |
| StringIndexer | x | x | x | |
| Tokenizer | x | x | x | |
| VectorAssembler | x | x | x | |
| VectorIndexer | x | x | | |
| VectorSlicer | x | x | | |
| WordToVector | x | x | | | |

## 分类相关

| Transformer | Spark| MLeap | Scikit-Learn  | TensorFlow |
|---|:---:|:---:|:---:|:---:|
| DecisionTreeClassifier | x | x | x | |
| GradientBoostedTreeClassifier | x | x | | |
| LogisticRegression | x | x | x | |
| LogisticRegressionCv | x | x | x | |
| NaiveBayesClassifier | x | x | | |
| OneVsRest | x | x | | |
| RandomForestClassifier | x | x | x | |
| SupportVectorMachines | x | x | x | |
| MultiLayerPerceptron | x | x | | | |

## 回归相关

| Transformer | Spark | MLeap | Scikit-Learn | TensorFlow |
|---|:---:|:---:|:---:|:---:|
| AFTSurvivalRegression | x | x | | |
| DecisionTreeRegression | x | x | x | |
| GeneralizedLinearRegression | x | x | | |
| GradientBoostedTreeRegression | x | x | | |
| IsotonicRegression | x | x | | |
| LinearRegression | x | x | x | |
| RandomForestRegression | x | x | x | | |


## 聚类相关

| Transformer | Spark | MLeap | Scikit-Learn | TensorFlow |
|---|:---:|:---:|:---:|:---:|
| BisectingKMeans | x | x | | |
| GaussianMixtureModel | x | x | | |
| KMeans | x | x | | |
| LDA | x | | | | |

## 扩展
| Transformer | Spark | MLeap | Scikit-Learn | TensorFlow | 描述 |
|---|:---:|:---:|:---:|:---:|:---|
| MathUnary | x | x | x | | Simple set of unary mathematical operations \| 一元数学计算集合 |
| MathBinary | x | x | x | | Simple set of binary mathematical operations \| 二元数学计算集合 |
| StringMap | x | x | x | | Maps a string to a double \| 映射字符串为 double 值。 |

## 推荐算法
| Transformer | Spark | MLeap | Scikit-Learn | TensorFlow |
|---|:---:|:---:|:---:|:---:|
| ALS | x | | | | |

