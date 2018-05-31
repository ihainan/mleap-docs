# BOOSTING TRANSFORM SPEED | 提高转换速度

MLeap's performance with transforming leap frames is good out of the box, but sometimes there are situations where even more speed is needed. Luckily, we have a row-based transformer that eliminates a lot of overhead in transforming leap frames. The overhead that is being eliminated is transforming the schema of the leap frame on every call to transform. How is this done?

默认情况下 MLeap 转换 Leap Frame 的性能已经很好了，但有时候某些场景要求更快的速度。幸运的是，我们有一个基于数据行的转换器，用于减少每次转换 Leap Frame 时 Schema 转换的时间开销。那么这个转换器是怎么做到的呢？

1. Calculate the input/output schema of a leap frame before any calls to
   transform | 在每次调用转换接口前，计算好 Leap 数据帧的输入 / 输出  Schema
2. When we transform a row, preallocate enough space to store all
   required results | 当转换一条数据记录时，预分配足够的内存空间用于存储所有的结果数据
3. Fill the preallocated space with results from our transformers and
   return the transformed row | 将 Transformer 的计算结果填进之前预分配的空间，并返回转换后的数据记录

A lot of time is spent in transforming the schema relative to actually processing your data, so this can be a huge speed increase in certain cases.

相对比实际的数据处理过程，大量的时间都被花费在了转换 Schema 的过程中，因此在特定情况下可以实现很大的速度提升。