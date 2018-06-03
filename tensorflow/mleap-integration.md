# TensorFlow 集成

MLeap 支持集成已经训练好的 TensorFlow Graph。MLeap 使用 TensorFlow 提供的 Swig 绑定来将 MLeap 的数据类型转换为要求的 C++ Tensor 类型，并向下调用 C++ 的 TensorFlow 依赖库。

## TensorFlow 的 Transformer

MLeap TensorFlow 的 Transformer 被编译在一个 TensorFlow Graph 文件当中，这个文件通过调用 `freeze_graph` 方法生成。当第一个请求要求转换 Leap Frame 的时候，这个 Transformer 会被惰性初始化。会话一旦已经开始，就会维持打开状态，直到 Transformer 的 `close` 方法被调用。

TensorFlow Transformer 处理 Leap Frame 之前，需要保证 Leap Frame 中的数据的格式正确。参见[类型转换](#类型转换)小节了解如何将数据转换为 TensorFlow 类型。

Transformer 随后输出一列 Tensor 作为原始的 TensorFlow 输出字段，以及多个输出 Tensor 所指定的独立字段。由于现在 MLeap 的工作机制限制，中间的输出字段列必须得保留，但在未来我们会避免这个问题的出现。参考[实例](#实例)小节了解 TensorFlow Transformer 如何作用于 Leap Frame。由于 TensorFlow 的输出类型需要提前获知，因此我们能够知道如何去构建转换之后新的 Leap Frame。

### MLeap TensorFlow 模型

MLeap TensorFlow 模型负责维护 TensorFlow 会话，它是一个 Java `Closeable` 实现，需要在 MLeap Transformer 和其他控制对象不再需要资源之后关闭。模型也会在 `finalized` 阶段关闭会话，以防用户忘记调用该方法。模型的输出是 MLeap `Tensor` 对象的 `Seq` 序列，`Tensor` 对象包含按照对应 shape 指定顺序排序的输出数据。

为了让 TensorFlow 支持兼容 MLeap 数据类型的 Transformer，我们需要以下信息：

| 属性 | 说明 |
|---|---|
| graph | 使用 **freeze_graph** 保存得到的 TensorFlow GraphDef |
| shape | 必须指定类型的输出 / 输出定义 |
| nodes | 将会被执行（不考虑保存输出）的节点的字符串列表 |

## 类型转换

所有的数据类型已被隐式转换为级别 0 的 TensorFlow Tensor。MLeap 中的`Tensor` 数据类型与 TensorFlow Tensor 完美对应，`SparseTensor` 值在传给 TensorFlow 之前先会被转换为稠密矩阵。

下表为我们目前所支持的 TensorFlow 数据类型。

| Tensorflow 数据类型 | MLeap 数据类型 |
|---|---|
| DT_BOOLEAN | BooleanType |
| DT_STRING | StringType |
| DT_INT32 | IntegerType |
| DT_INT64 | LongType |
| DT_FLOAT | FloatType |
| DT_DOUBLE | DoubleType |
| _Tensor_ | TensorType |

因为 Swig 封装器目前还未支持所有的数据类型，我们建议为你的 MLeap TensorFlow Graph 添加一个强制转换操作。

### Notes | 注意

1. Swig 封装器尚未支持无符号整形，8 位和 16 位整型。
2. Swig 封装器尚未支持复杂类型。

# 实例

假设我们有包含如下的数据的 Leap Frame。数据包括一个 Scala 的浮点数值和一个一维的浮点 Tensor（或被称作浮点值向量）

## 输入 Leap Frame

### 数据


| double1 | tensor1 |
|---|---|
| 3.0 | [2.0, 1.0, 5.0] |

### 数据类型

| Field | Data Type |
|---|---|
| double1 | DoubleType() |
| tensor1 | TensorType(DoubleType(false)) |

如果我们使用一个 TensorFlow Transformer 来处理以上的 Leap Frame，这个 Transformer 使用 `double1` 来归一化 `tensor1` 向量，并指定了我们的结果 Tensor，那么输出的 Leap Frame 会类似于：

## 输出 Leap Frame

### 数据

| double1 | tensor1 | raw_tf_tensors | output1 |
|---|---|---|---|
| 3.0 | [2.0, 1.0, 5.0] | Seq([6.0, 3.0, 15.0]) | [6.0, 3.0, 15.0] |

### 数据类型

| Field | Data Type | Notes |
|---|---|---|
| double1 | DoubleType() | |
| tensor1 | TensorType(DoubleType(false)) | |
| raw_tf_tensors | ListType(AnyType(false))  | 返回类型未知，不允许为 null 值 |
| output1 | TensorType(DoubleType(false)) | 底层数据类型此时已知，并反射为 LeapFrame 中的数据类型。 |

## 注意

1. `raw_tf_tensors` 包含 `AnyType` 数据，这意味着该字段不可被序列化。如果使用 Combust API 服务，那么这个字段会从返回结果中筛除。
