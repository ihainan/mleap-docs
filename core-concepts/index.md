# Core Concepts | 核心概念

MLeap uses several core building blocks to deploy your pipelines in an easy-to-use manner.

MLeap 通过多个核心构件（Building Block）来实现 Pipeline 的轻松部署。

| Concept \| 概念 | Description \| 描述 |
|---|---|
| Data Frames | Used to store data that is to be transformed, similar to a SQL table \| 用于存储将被转换的数据，类似于 SQL 表。 |
| Transformers | Take data from a data frame, apply some operation to it, and output new fields into the data frame \| 从 Data Frame 中提取数据，对数据应用某些操作，并输出新的字段到 Data Frame 中。 |
| Pipelines | Use pipelines to execute a series of transformers against a data frame \| 使用 Pipeline 来对 Data Frame 执行一系列 Transformer 的操作。 |
| Feature Unions (Scikit Only) \| 特征联合（Feature Unions，仅适用于 Scikit Learn） | Use feature unions to execute Pipelines of transformers in parallel and join results at the end \| 使用特征联合来并行执行包含 Transformer 的多个 Pipeline，并在结束后结合（Join）产出的结果。 |
| MLeap Bundles | Used to store ML pipelines in a common JSON/Protobuf serialization format \| 以通用的 JSON 和 Protobuf 等序列化格式来存储 ML Pipeline。 |
| MLeap Runtime | Used to execute an ML pipeline in the JVM using lightweight data structures \| 在 JVM 中以轻量级的数据结构来执行 ML Pipeline。 |

This section is meant as an introduction to people who are unfamiliar with the basics of machine learning pipelines and working with data frames. Although, the sections on MLeap Bundles and the MLeap Runtime should be useful to everyone.

虽然本章的目的是为不熟悉 Pipeline 和 Data Frame 等机器学习基础的人提供的一份入门指导，但是关于 MLeap Bundle 和 MLeap Runtme 的章节也适用于所有人。