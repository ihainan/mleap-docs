# Transformers | Transformer

Transformers offer a basic building block to executing machine learning pipelines. A transformer takes data from a data frame, transforms it using some operation and outputs one or more new fields back to the data frame. This is a very simple task, but is at the core of the Spark, Scikit-learn, MLeap and Tensorflow execution engines.

Transformer 是机器学习 Pipeline 执行的基本单元。一个 Transformer 以一帧 Data Frame 作为输入，执行某些转换操作，输出包含一个或者多个新字段的数据到原来的 Data Frame 中。虽然要做的事情很简单，但它是 Spark、Scikit-Learn、MLeap 和 TensorFlow 执行引擎的核心部分。

Transformers are used for many different tasks, but the most common in machine learning are:

Transformer 被用于许多用途，但在机器学习中，最常见的用途是：

1. Feature extraction | 特征提取
2. Model scoring | 模型评分

## Feature Extraction | 特征提取

Feature extraction is the process of taking one or more features from an input dataset and deriving new features from them. In the case of data frames, the features come from the input data frame and are written to the output data frame.

特征提取用于从一个输入数据数据集中提取一个或者多个特征，延生成一个新的特征。在我们 Data Frame 的例子中，特征来源自输入 Data Frame，并被写入到输出 Data Frame 中。

Some examples of feature extraction are:

一些特征提取的例子：

1. String indexing (label encoding) - Taking a string and converting it to an integer value | 字符串索引（标签编码） - 将一个字符串转换为整型值。
2. One hot encoding - Converting an integer value to a vector of 1s and 0s | One Hot Encoding - 将整型值转换为 0 / 1 的二值向量。
3. Feature selection - Running analysis to determine which features are most effective for training a predictive ML algorithm (i.e. CHI2) | 特征选择 - 分析哪一个特征能够更有效地训练出一个用于预测的机器学习算法（比如 CHI2）。
4. Math - Basic mathematical functions, such as dividing two features by each other or taking the log of a feature | 数学运算 - 基础的数学函数，比如将两个特征的值相互相除，或者求一个特征值的 log 值。

There are too many examples of feature extraction to enumerate here, but take a look at our complete list of [supported feature transformers](support.html#features) to get an idea of what is possible.

有太多的特征提取操作可以去列举，你可以参见 [支持的特征 Transformer](support.html#features) 章节来获得完整的支持列表。

## Regression | 回归

Regression is used to predict a continuous numeric value, such as the price of a car or a home. Regression models, for the most part, operate on vectors of doubles called a "feature vector". The feature vector contains all of the known information about what is being predicted. In the case of predicting a price of a house, the feature vector will have things like the encoded region where the house is, the square footage, how many bathrooms there are, how old it is, etc.

回归（Regression）用于预测一个连续值，比如一辆车或者一套房子的价格。在大多数情况下回归模型会操作一个被称为 “特征向量” 的浮点数值向量，这个特征向量包含了所有需要被用于预测的信息。以预测房价为例，特征向量会包含编码过的房子所在地区信息、面积信息、房间数量信息、房龄信息，等等。

See a list of [supported regression models](support.html#regression).

参见  [支持的回归模型](support.html#regression) 页面获得完整列表。

## Classification | 分类

Classification is used to predict categorical information. An example is making a binary prediction of whether or not to give a consumer a loan. Another example is predicting what type of sound is contained in a .wav file, or whether or not there is a person in and image.

分类（Classification）用于预测离散值。比如预测是否应该给用户贷款，或者预测一个 .wav 文件内包含了什么类型的声音，又或者是图片中是否有一个人类。

[Supported classification models](support.html#classification).

[支持的分类模型](support.html#classification).

## Clustering | 聚类

Clustering is used to assign a label to similar data (thus categorizing/clustering it). It is similar to classification in that the predictions are discrete values from a set. Unlike classification models though, clustering models are part of the unsupervised family of models and do not operate on labeled data. This is useful for feature engineering, anomaly detection, as well as many other tasks.

聚类（Clustering）用于给相似的数据打上标签（也就是做分类或者聚合）。类似于分类，得到的结果是一个集合里面的离散值，但与分类不同的是，聚类模型属于非监督模型，他的训练数据并没有已经贴好标签。聚类对于特征功能、异常检测等领域非常有用。

[Supported clustering models](support.html#clustering).

[支持的聚类模型](support.html#clustering).

## Other Types of Transformers | 其他类型的 Transformer

Transformers can do ANYTHING! This is just a sample of the most common uses of them. However, you can build transformers to resize images, resample sound data, import data from different data sources or anything else you can think of. The sky is the limit.

Transformer 无所不能！以上仅仅只是一些典型的例子，你也可以构建 Transformer 来调整图片尺寸、重采样声音数据，从不同的数据源获取得到数据，无所拘束地去做任何你想做的事情。