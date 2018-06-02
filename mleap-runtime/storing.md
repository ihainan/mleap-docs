# 存储 Leap Frame

我们能够使用不同的序列化策略来存储和加载 Leap Frame。目前提供的格式包括：

1. JSON
2. Avro
3. Binary

如果以上格式不能满足你的使用需求，你可以定制自己的序列化格式。

## Leap Frame 示例

后续所有的序列化示例都会使用如下 Leap Frame。

```scala
val schema = StructType(StructField("features", TensorType(BasicType.Double)),
  StructField("name", ScalarType.String),
  StructField("list_data", ListType(BasicType.String)),
  StructField("nullable_double", ScalarType(BasicType.Double, true)),
  StructField("float", ScalarType.Float),
  StructField("byte_tensor", TensorType(BasicType.Byte)),
  StructField("short_list", ListType(BasicType.Short)),
  StructField("nullable_string", ScalarType(BasicType.String, true))).get
val dataset = Seq(Row(Tensor.denseVector(Array(20.0, 10.0, 5.0)),
  "hello", Seq("hello", "there"),
  Option(56.7d), 32.4f,
  Tensor.denseVector(Array[Byte](1, 2, 3, 4)),
  Seq[Short](99, 12, 45),
  None))
val frame = DefaultLeapFrame(schema, dataset)
```

## JSON

```scala
// Store Leap Frame
for(bytes <- frame.writer("ml.combust.mleap.json").toBytes();
    frame2 <- FrameReader("ml.combust.mleap.json").fromBytes(bytes)) {
  println(new String(bytes)) // print the JSON bytes
  assert(frame == frame2)
}
```

## AVRO

```scala
// Store Leap Frame
for(bytes <- frame.writer("ml.combust.mleap.avro").toBytes();
    frame2 <- FrameReader("ml.combust.mleap.avro").fromBytes(bytes)) {
  println(new String(bytes)) // print the Avro bytes
  assert(frame == frame2)
}
```

## 二进制数据

最有效的存储格式，使用数据的输入 / 输出流来序列化 Leap Frame 数据。

```scala
// Store Leap Frame
for(bytes <- frame.writer("ml.combust.mleap.binary").toBytes();
    frame2 <- FrameReader("ml.combust.mleap.binary").fromBytes(bytes)) {
  println(new String(bytes)) // print the binary bytes
  assert(frame == frame2)
}
```

## 自定义格式

MLeap 允许用户自己实现序列化器。