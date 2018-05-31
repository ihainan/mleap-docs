# CREATING A LEAP FRAME | 创建 Leap Frame

Let's create a leap frame to hold our data.

让我们创建一帧 Leap Frame 来存储数据。

```scala
import ml.combust.mleap.runtime._
import ml.combust.mleap.core.types._
import ml.combust.mleap.runtime.frame.{DefaultLeapFrame, Row}

// Create a schema. Returned as a Try monad to ensure that there
// Are no duplicate field names
val schema: StructType = StructType(StructField("a_string", ScalarType.String),
  StructField("a_double", ScalarType.Double),
  StructField("a_float", ScalarType.Float),
  StructField("an_int", ScalarType.Int),
  StructField("a_long", ScalarType.Long)).get

// Create a dataset to contain all of our values
// This dataset has two rows
val dataset = Seq(Row("Hello, MLeap!", 56.7d, 13.0f, 42, 67l),
  Row("Another row", 23.4d, 11.0f, 43, 88l))

// Create a LeapFrame from the schema and dataset
val leapFrame = DefaultLeapFrame(schema, dataset)

// Make some assertions about the data in our leap frame
assert(leapFrame.dataset(0).getString(0) == "Hello, MLeap!")
assert(leapFrame.dataset(0).getDouble(1) == 56.7d)
assert(leapFrame.dataset(1).getDouble(1) == 23.4d)
```

Programatically creating leap frames like this can be very useful for scoring data from, say, a web server or some other user input. It is also useful to be able to load data from files and store data for later use. See our [section on serializing leap frames](Serializing+Leap+Frames.html) for more information.

对于预测来自例如 Web 服务器或者其他用户输入等数据源的数据来说，类似这种通过代码来创建 Leap Frames 的方法是非常有用的。此外，它还可以从文件中加载，或者把数据存储到文件中供后期使用。可以参见我们的  [序列化 Leap Frame](Serializing+Leap+Frames.html)  章节以了解更多的细节。