# MLeap 服务

MLeap 服务提供了一个轻量级的 Docker 映像，用于配置一套能够处理 MLeap Model 的 RESTful API 服务。这就意味着我们能够轻松使用这套服务来快速构造与运行原型。

## 安装

MLeap 服务的 Docker 映像被托管在 [Docker Hub](https://hub.docker.com/r/combustml/mleap-serving/)。

开始之前，我们需要把映像拉取到本地机器。

```
docker pull combustml/mleap-serving:0.9.0
```

## 用法

为了能够将 model 以 RESTful API 的方式被使用，我们将要：

1. 在 Docker 中启动服务
2. 加载模型到内存中
3. 转换一帧 Leap Frame

### 启动服务

转换数据之前我们需要启动 Docker 映像。确保已经把包含你模型的目录从本地机器挂载到容器中。在这个例子里面，我们将会把模型存储在 `tmp/models` 目录下面，并挂载到容器的 `/models` 目录。

```
mkdir /tmp/models
docker run -p 65327:65327 -v /tmp/models:/models combustml/mleap-serving:0.9.0
```

这会把我们的模型服务开放在本地的 `65327` 端口。

### 加载模型

使用 cURL 命令来加载模型到内存中。如果你没有自己的模型，也可以从我们的样例模型中下载。启动服务的时候，请确保模型已经被放置在了正确的容器挂载目录中。

1. [AirBnB 线性回归](https://github.com/combust/mleap/raw/master/mleap-benchmark/src/main/resources/models/airbnb.model.lr.zip)
2. [AirBnB 随机森林](https://github.com/combust/mleap/raw/master/mleap-benchmark/src/main/resources/models/airbnb.model.rf.zip)

```
curl -XPUT -H "content-type: application/json" \
  -d '{"path":"/models/<my model>.zip"}' \
  http://localhost:65327/model
```

### 转换操作

接下来我们会使用模型来转换一帧 JSON 编码的 Leap Frame。如果你使用我们的 AirBnb 样例模型的话，可以从这里下载对应的 Leap Frame。

1. [AirBnB Leap Frame](https://s3-us-west-2.amazonaws.com/mleap-demo/frame.airbnb.json)

保存 Leap Frame 到 `/tmp/frame.airbnb.json` 路径，然后使用我们的服务来转换数据。

```
curl -XPOST -H "accept: application/json" \
  -H "content-type: application/json" \
  -d @/tmp/frame.airbnb.json \
  http://localhost:65327/transform
```

你会得到一帧 JSON 格式的结果 Leap Frame，你可以从中提取结果数据。如果你使用的是我们样例 AirBnB 模型的其中一个的话，那么 Leap Frame 的最后一个字段将会是预测结果。

### 卸载模型

如果因为某些原因，你希望在服务能够正常运作的前提下，将模型从内存中删除，那么只需要 DELETE `model` 资源即可。

```
curl -XDELETE http://localhost:65327/model
```

