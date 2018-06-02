# Demo Notebooks

我们已经整理了一些 Notebook 来展示 MLeap 集成 Spark、Scikit-Learn（很快就会支持 TensorFlow）的使用。这些 Demo Notebook 可以从 [mleap-demo](https://github.com/combust/mleap-demo) GitHub 仓库中获取。本章其他的页面也介绍了在不同的 NoteBook 环境中如何导入 MLeap 依赖。

我们的目标是能在 Jupyter / Zeppelin / DataBricks 环境以及 Spark / Scikit / TensorFlow 框架中保持一致性。

我们现在已有的 Demo Notebook 如下：

### AirBnb 价格预测

使用 AirBnb 历史数据集的一个快照来构建得到一个 ML Pipeline，对房间价格与房间特征（ `房间数量`、`浴室数量`、`面积` 等）的关系进行建模。这个 Notebook 展示了如何分别训练和序列化一个线性回归模型和一个随机森林模型，以及反序列化一个 Bundle 和在 MLeap Runtime 中不依赖于 Spark Context 来对 Leap Frame 进行评分。

* [Spark 代码](https://github.com/combust/mleap-demo/blob/master/notebooks/airbnb-price-regression.ipynb) 
* [PySpark 代码](https://github.com/combust/mleap-demo/blob/master/notebooks/PySpark%20-%20AirBnb.ipynb)
* [Scikit-Learn 代码](https://github.com/combust/mleap-demo/blob/master/notebooks/airbnb-price-regression-scikit.ipynb)


### Lending Club 贷款批准

使用公开的 Lending Club 数据集来决定是否批准贷款申请。我们构建了一个 ML Pipeline，分别训练一个逻辑回归模型和一个随机森林模型，并展示了一个如何部署两个模型到 API 服务的例子。对于反序列化和 MLeap Runtime 的例子，请参见上面的 AirBnb Demo（我们后面也会添加到本例子中）。

* [Spark 代码](https://github.com/combust/mleap-demo/blob/master/notebooks/lending-club-classifier-demo.ipynb) 


### MNIST 手写数字识别

这个 Demo 使用了现在比较流行的 MNIST 数据集。Demo 使用更为复杂的 Transformer（比如 PCA） 来训练得到一个随机森林模型，并将其序列化成一个 Bundle。这个 Demo 是为了说明即使是复杂的 Transformer，MLeap 也能够节省开发者用于编写和维护代码以支持诸如 PCA 和随机森林模型等特性所花费的时间。

* [Spark 代码](https://github.com/combust/mleap-demo/blob/master/zeppelin/mnist-mleap-demo-code.json) - 暂时只支持 Zeppelin