# MLeap Serving | MLeap 服务

MLeap serving provides a lightweight docker image for setting up RESTful API services with MLeap models. It is meant to be very simple and used as a tool to get prototypes up and running quickly.

MLeap 服务提供了一个轻量级的 Docker 映像，用于配置一套能够处理 MLeap Model 的 RESTful API 服务。这就意味着我们能够轻松使用这套服务来快速构造与运行原型。

## Installation | 安装

MLeap serving is a Docker image hosted on [Docker Hub](https://hub.docker.com/r/combustml/mleap-serving/).

MLeap 服务的 Docker 映像被托管在 [Docker Hub](https://hub.docker.com/r/combustml/mleap-serving/)。

To get started, pull the image to your local machine:

开始之前，我们需要把映像拉取到本地机器。

```
docker pull combustml/mleap-serving:0.9.0
```

## Usage | 用法

In order to start using your models as a REST API, we will need to:

为了能够将 model 以 RESTful API 的方式被使用，我们将要：

1. Start the server in Docker | 在 Docker 中启动服务
2. Load our model into memory | 加载模型到内存中
3. Transform a leap frame | 转换一帧 Leap Frame

### Start Server | 启动服务

First let's start the Docker image so we can start transforming data. Make sure to mount a directory containing your models on the host machine into the container. In this example, we will be storing our models in `/tmp/models` and mounting it in the container at `/models`.

转换数据之前我们需要启动 Docker 映像。确保已经把包含你模型的目录从本地机器挂载到容器中。在这个例子里面，我们将会把模型存储在 `tmp/models` 目录下面，并挂载到容器的 `/models` 目录。

```
mkdir /tmp/models
docker run -p 65327:65327 -v /tmp/models:/models combustml/mleap-serving:0.9.0
```

This will expose the model server locally on port `65327`.

这会把我们的模型服务开放在本地的 `65327` 端口。

### Load Model | 加载模型

Use curl to load the model into memory. If you don't have your own model, download one of our example models. Make sure to place it in the models directory you mounted when starting the server.

使用 cURL 命令来加载模型到内存中。如果你没有自己的模型，也可以从我们的样例模型中下载。启动服务的时候，请确保模型已经被放置在了正确的容器挂载目录中。

1. [AirBnB 线性回归](https://github.com/combust/mleap/raw/master/mleap-benchmark/src/main/resources/models/airbnb.model.lr.zip)
2. [AirBnB 随机森林](https://github.com/combust/mleap/raw/master/mleap-benchmark/src/main/resources/models/airbnb.model.rf.zip)

```
curl -XPUT -H "content-type: application/json" \
  -d '{"path":"/models/<my model>.zip"}' \
  http://localhost:65327/model
```

### Transform | 转换操作

Next we will use our model to transform a JSON-encoded leap frame. If you are using our AirBnB example models, you can download the leap frame here:

接下来我们会使用模型来转换一帧 JSON 编码的 Leap Frame。如果你使用我们的 AirBnb 样例模型的话，可以从这里下载对应的 Leap Frame。

1. [AirBnB Leap Frame](https://s3-us-west-2.amazonaws.com/mleap-demo/frame.airbnb.json)

Save the frame to `/tmp/frame.airbnb.json` and then let's transform it using our server.

保存 Leap Frame 到 `/tmp/frame.airbnb.json` 路径，然后使用我们的服务来转换数据。

```
curl -XPOST -H "accept: application/json" \
  -H "content-type: application/json" \
  -d @/tmp/frame.airbnb.json \
  http://localhost:65327/transform
```

You should get back a result leap frame, as JSON, that you can then extract the result from. If you used one of our example AirBnB models, the last field in the leap frame will be the prediction.

你会得到一帧 JSON 格式的结果 Leap Frame，你可以从中提取结果数据。如果你使用的是我们样例 AirBnB 模型的其中一个的话，那么 Leap Frame 的最后一个字段将会是预测结果。

### Unload Model | 卸载模型

If for some reason you don't want any model to be loaded into memory, but keep the server running, just DELETE the `model` resource:

如果因为某些原因，你希望在服务能够正常运作的前提下，将模型从内存中删除，那么只需要 DELETE `model` 资源即可。

```
curl -XDELETE http://localhost:65327/model
```

