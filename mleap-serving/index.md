# MLeap 服务（基于 Spring Bot）

MLeap 的 Spring Boot 项目提供了一个轻量级的 Docker 映像，用于配置一套能够处理 MLeap Model 的 RESTful API 服务。

## 安装

MLeap Springt Boot 的 Docker 映像被托管在 [Docker Hub](https://hub.docker.com/r/combustml/mleap-spring-boot/)。

开始之前，我们需要把映像拉取到本地机器，将 {VERSION}` 替换成你需要的版本。

```bash
docker pull combustml/mleap-spring-boot:{VERSION}
```

### 启动服务

转换数据之前我们需要启动 Docker 映像。确保已经把包含你模型的目录从本地机器挂载到容器中。在这个例子里面，我们将会把模型存储在 `tmp/models` 目录下，并挂载到容器的 `/models` 目录。

```shell
mkdir /tmp/models
docker run -p 8080:8080 -v /tmp/models:/models combustml/mleap-spring-boot:{VERSION}
```

我们的模型服务将会开放在本地的 `8080` 端口。

### 开放接口

1. POST /models : 加载一个模型，替换`{BUNDLE_ZIP}` 成你的 Bundle Zip 的名字，以及替换 `{YOUR_MODEL_NAME}` 为你的模型名。

```shell
body='{"modelName":"{YOUR_MODEL_NAME}","uri":"file:/models/{BUNDLE_ZIP}","config":{"memoryTimeout":900000,"diskTimeout":900000},"force":false}'

curl --header "Content-Type: application/json" \
  --request POST \
  --data "$body" http://localhost:8080/models
```

2. DELETE /models/{YOUR_MODEL_NAME} : 卸载一个模型，替换 `{YOUR_MODEL_NAME}` 为你的模型名。

   ``` shell
   curl --header "Content-Type: application/json" \
   --request DELETE \
   http://localhost:8080/models/{YOUR_MODEL_NAME}
   ```

3. GET /models/{YOUR_MODEL_NAME} : 拉取一个已加载模型，替换 `{YOUR_MODEL_NAME}` 为你的模型名。

   ``` shell
   curl --header "Content-Type: application/json" \
       --request GET \
       http://localhost:8080/models/{YOUR_MODEL_NAME}
   ```

4. GET /models/{YOUR_MODEL_NAME}/meta : 获取一个已加载模型的元数据信息，替换 `{YOUR_MODEL_NAME}` 为你的模型名。

   ``` shell
   curl --header "Content-Type: application/json" \
   	--request GET \
   	http://localhost:8080/models/{YOUR_MODEL_NAME}/meta
   ```

   

5. POST /models/{YOUR_MODEL_NAME}/transform: 对请求数据进行转换或者评分，替换 `{YOUR_MODEL_NAME}` 为你的模型名，填写 `{YOUR_FORMAT}` 字段，并对你的 Leap Frame 数据进行编码，填入 `{ENCODED_LEAP_FRAME}` 字段中。

   ``` shell
   curl --header "Content-Type: application/json" \
     --request POST \
     --data "$body" http://localhost:8080/models/transform
   ```

`Format` 可以是 `ml.combust.mleap.binary` 或 `ml.combust.mleap.json`， Leap Frame 也应该使用 `format` 指定的编码格式进行编码。

注意：上述接口可以使用：

- JSON（`Content-Type` header 设置为 `application/json`）。
- Protobuf （`Content-Type` header 设置为 `application/x-protobuf`）。

前面的评分接口需要你对 Leap Frame 进行编码，可以是 JSON 或者 protobuf。如果你不希望对 Leap Frame 进行编码，可以使用下面的接口，替换成你选择的模型和你的 Leap Frame 数据即可。

```shell
body='{YOUR_LEAP_FRAME_JSON}'

curl --header "Content-Type: application/json" \
  --header "timeout: 1000" \
  --request POST \
  --data "$body" http://localhost:8080/models/{YOUR_MODEL_NAME}/transform	
```

注意：该接口可以使用：

- 若是 JSON Leap Frame，则使用 JSON（`Content-Type` header 设置为 `application/json`）。 
- 若是 Protobuf Leap Frame，则使用 Protobuf （`Content-Type` header 设置为 `application/x-protobuf`）。

可以查看我们的 Swagger API 文档 [mleap_serving_1.0.0_swagger.yaml](https://github.com/combust/mleap/blob/master/mleap-spring-boot/src/main/resources/mleap_serving_1.0.0_swagger.yaml) 获得更多的信息，或者用它来体验这些 API 接口。

样例模型:

1. [AirBnB 线性回归](https://github.com/combust/mleap/raw/master/mleap-benchmark/src/main/resources/models/airbnb.model.lr.zip)
2. [AirBnB 随机森林](https://github.com/combust/mleap/raw/master/mleap-benchmark/src/main/resources/models/airbnb.model.rf.zip)

样例 Leap Frames:

1. [AirBnB Leap Frame](https://s3-us-west-2.amazonaws.com/mleap-demo/frame.airbnb.json)

参见 [mleap-executor](https://github.com/combust/mleap/tree/master/mleap-executor) 项目的 README.md 了解如何使用 gRPC 而非调用 HTTP。