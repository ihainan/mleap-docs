# Data Types | 数据类型

There are many data types supported by Spark, MLeap, Scikit-learn and Tensorflow. Fortunately, because all of these technologies are based on well-known mathematical data structures, they are all cross-compatible with each other to a large extent.

Spark、MLeap、Scikit-Learn 和 TensorFlow 自身都支持了相当多的数据类型。庆幸的是，由于这些平台框架都是基于已被广泛认可的数学数据结构来设计，因为他们能够在一定程度上相互兼容。

Data frames store the data types of their columns in a schema object. This schema can be consulted to determine which operations are available for which columns and how transformations should be handled.

Data Frame 在一个 Schema 对象中存储它的数据列的数据类型。这个 Schema 对象可悲用来决定什么操作能够被应用在某一列上，以及转换操作应该如何去执行。

## Supported Data Types | 支持的数据类型

| Data Type | Notes |
|---|---|
| Byte | 8-bit integer values supported by all platforms, MLeap and Spark only support signed versions \| 8 位整型值，所有平台都支持，但 MLeap 和 Spark 只支持带符号整型。 |
| Short | 16-bit integer values supported by all platforms, MLeap and Spark only support signed versions \| 16 位整型值，所有平台都支持，但 MLeap 和 Spark 只支持带符号整型。 |
| Integer | 32-bit integer values supported by all platforms, MLeap and Spark only support signed versions \| 32 位整型值，所有平台都支持，但 MLeap 和 Spark 只支持带符号整型。 |
| Long | 64-bit integer values are supported by all platforms, MLeap and Spark only support signed versions \| 64 位整型值，所有平台都支持，但 MLeap 和 Spark 只支持带符号整型。 |
| Float | 32-bit floating point values are supported by all platforms \| 32 位浮点值，所有平台都支持。 |
| Double | 64-bit floating point values are supported by all platforms \| 32 位浮点值，所有平台都支持。 |
| Boolean | 8-bit value representing true or false, can be packed into 1-bit if needed \| 8 位值，用于表示 true 和 false，在需要的情况下能够被表示成 1 位值。 |
| String | A series of characters, either null-terminated or length prefixed depending on platform \| 字符集合，根据平台的不同，可能是 null 结尾的变长值，或者是长度放在数据前面的固长值。 |
| Array | Sequence of elements of any of the above basic types \| 以上基础类型的数据的集合。 |
| Tensor | Supported by MLeap and Tensorflow, provides n-dimensional storage for one of the above basic data types \| 被 MLeap 和 TensorFlow 支持，提供以上基础类型数据集合的多维存储支持。 |

