# 关于 Spark

主要特点：

- 运行速度快：使用 DAG（有向无环图）执行引擎
- 容易使用：Spark 支持使用 Scala、Java、Python 和 R 语言进行编程
- 通用型：Spark 提供了完整而强大的技术栈，包括 SQL 查询、流式计算、机器学习和图算法组件，这些组件可以无缝整合在同一个应用中，足以应对复杂的计算；
- 运行模式多样：Spark 可运行于独立的集群模式中，或者运行于 Hadoop 中，也可运行于 Amazon EC2 等云环境中，并且可以访问 HDFS、Cassandra、HBase、Hive 等多种数据源。

# RDD

一个 RDD 就是一个分布式对象集合，本质是一个只读的分区记录集合

Action：执行计算并指定输出的形式，接受 RDD 返回非 RDD

Transformaion：执行计算，接受 RDD 并返回 RDD

### 持久化

```python
rdd.cache()
```

rdd.flatMap

