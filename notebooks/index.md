# Demo Notebooks | Demo Notebooks

We have put together a number of notebooks to demonstrate MLeap usage with Spark, Scikit and soon TensorFlow. The demo notebooks can be found on our [mleap-demo](https://github.com/combust/mleap-demo) github repository. Child pages below also include instructions for how to include MLeap within your notebook environment of choice.

我们已经整理了一些 Notebook 来展示 MLeap 集成 Spark、Scikit-Learn（很快就会支持 TensorFlow）的使用。这些 Demo Notebook 可以从 [mleap-demo](https://github.com/combust/mleap-demo) GitHub 仓库中获取。本章其他的页面也介绍了在不同的 NoteBook 环境中如何导入 MLeap 依赖。

Our goal is to maintain parity between notebooks for Jupyter/Zeppelin/DataBricks and Spark/Scikit/TensorFlow. 

我们的目标是能在 Jupyter / Zeppelin / DataBricks 环境以及 Spark / Scikit / TensorFlow 框架中保持一致性。

Here is a list of demo notebooks we have available today:

我们现在已有的 Demo Notebook 如下：

### AirBnb Price Prediction | AirBnb 价格预测

Using a snapshot of historical AirBnb listings data, build a ML Pipeline to model the price of an appartment based on features like `number of rooms`, `number of bathrooms`, `square feet` and others. The notebook demonstrates how to train and serialize both a linear regression and a random forest model. This notebook also shows deserialization of a bundle as well as scoring of new LeapFrames without the Spark Context with MLeap Runtime.

使用 AirBnb 历史数据集的一个快照来构建得到一个 ML Pipeline，对房间价格与房间特征（ `房间数量`、`浴室数量`、`面积` 等）的关系进行建模。这个 Notebook 展示了如何分别训练和序列化一个线性回归模型和一个随机森林模型，以及反序列化一个 Bundle 和在 MLeap Runtime 中不依赖于 Spark Context 来对 Leap Frame 进行评分。

* [Code for Spark](https://github.com/combust/mleap-demo/blob/master/notebooks/airbnb-price-regression.ipynb) | [Spark 代码](https://github.com/combust/mleap-demo/blob/master/notebooks/airbnb-price-regression.ipynb) 
* [Code for PySpark](https://github.com/combust/mleap-demo/blob/master/notebooks/PySpark%20-%20AirBnb.ipynb) | [PySpark 代码](https://github.com/combust/mleap-demo/blob/master/notebooks/PySpark%20-%20AirBnb.ipynb)
* [Code for Scikit-Learn](https://github.com/combust/mleap-demo/blob/master/notebooks/airbnb-price-regression-scikit.ipynb) | [Scikit-Learn 代码](https://github.com/combust/mleap-demo/blob/master/notebooks/airbnb-price-regression-scikit.ipynb)


### Lending Club Loan Approval | Lending Club 贷款批准

Using the public Lending Club dataset to determine whether the applicant is approved for a loan or not, we build an ML Pipeline and train a logistic regression and a random forest classifier. We also show an example of how to deploy the two models to an API server. For an example of deserialization and MLeap runtime, see the AirBnb demo above (we'll add it here too).

使用公开的 Lending Club 数据集来决定是否批准贷款申请。我们构建了一个 ML Pipeline，分别训练一个逻辑回归模型和一个随机森林模型，并展示了一个如何部署两个模型到 API 服务的例子。对于反序列化和 MLeap Runtime 的例子，请参见上面的 AirBnb Demo（我们后面也会添加到本例子中）。

* [Code for Spark](https://github.com/combust/mleap-demo/blob/master/notebooks/lending-club-classifier-demo.ipynb) | [Spark 代码](https://github.com/combust/mleap-demo/blob/master/notebooks/lending-club-classifier-demo.ipynb) 


### MNIST Digits Recognition | MNIST 手写数字识别

This demo uses the popular MNIST dataset and demonstrates usage and serialization of more complicated transformers like `PCA` to train a random forest classifier and serialize it to a bundle. The goal of this demo is to demonstrate that even for more complex transformers, MLeap saves developers potentially days of writing and maintaining code to support features like PCA and random forest models.

这个 Demo 使用了现在比较流行的 MNIST 数据集。Demo 使用更为复杂的 Transformer（比如 PCA） 来训练得到一个随机森林模型，并将其序列化成一个 Bundle。这个 Demo 是为了说明即使是复杂的 Transformer，MLeap 也能够节省开发者用于编写和维护代码以支持诸如 PCA 和随机森林模型等特性所花费的时间。

* [Code for Spark](https://github.com/combust/mleap-demo/blob/master/zeppelin/mnist-mleap-demo-code.json) - Zeppelin only for now | [Spark 代码](https://github.com/combust/mleap-demo/blob/master/zeppelin/mnist-mleap-demo-code.json) - 暂时只支持 Zeppelin