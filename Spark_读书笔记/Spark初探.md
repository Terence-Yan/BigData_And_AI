#### 1.Spark的创建者与创建时间
早在**2009**年，***Matei Zaharia*** 在加州大学伯克利分校的AMPLab进行博士研究时创立了Spark大数据处理与计算框架。

#### 2.Spark的优势与特点
不同于传统的数据处理框架，Spark基于内存的基本类型(primitive)为一些应用程序带来了100倍的性能提升。Spark允许用户程序将数据加载到集群内存中用于反复查询，非常适用于大数据和机器学习，日益成为最广泛采用的大数据模块之一。
Spark作为下一代大数据处理引擎，在非常短的时间里崭露头角，并且以燎原之势席卷业界。Spark对曾经引爆大数据产业革命的Hadoop MapReduce的改进主要体现在以下几个方面：
```
首先，Spark速度更快；
其次，Spark丰富的API带来了更强大的易用性；
最后，Spark不单单支持传统批处理应用，更支持交互式查询、流式计算、机器学习、图计算等各种应用，满足各种不同应用场景下的需求。
```

#### 3.Spark是什么
Spark是一个用来实现快速而通用的集群计算平台。
```
官方介绍：Apache Spark is a fast and general-purpose cluster computing system. It provides high-level APIs in Java, Scala, Python 
and R, and an optimized engine that supports general execution graphs. It also supports a rich set of higher-level tools including 
Spark SQL for SQL and structured data processing, MLlib for machine learning, GraphX for graph processing, and Spark Streaming.
```

#### 4.Spark的核心是什么
Spark的核心是一个对由很多计算任务组成的、运行在多个工作机器或者是一个计算集群上的应用进行调度、分发以及监控的计算引擎。

#### 5.Spark的软件栈
Spark的组件有：Spark Core、Spark SQL、Spark Streaming、MLlib、GraphX、集群管理器

#### 6.Spark的集群管理器
就底层而言，Spark设计为可以高效地在一个计算节点到数千个计算节点之间伸缩计算。为了实现这样的要求，同时获得最大的灵活性，Spark支持在各种集群管理器(cluster manager)上运行，包括 Hadoop YARN、Apache Mesos,以及Spark自带的一个简易调度器，叫做独立调度器。如果要在没有预装任何集群管理器的机器上安装Spark,那么Spark自带的独立调度器可以让你轻松入门；而如果已经有一个装有 YARN 或 Mesos 的集群，通过Spark对这些集群管理器的支持，你的应用也同样能运行在这些集群之上。

#### 7.Spark的存储层次
Spark不仅可以将任何Hadoop分布式文件系统(HDFS)上的文件读取为分布式数据集，也可以支持其他支持Hadoop 接口的文件系统，比如本地文件、亚马逊S3、Cassandra、Hive、HBase等。我们需要弄清楚的是，Hadoop并非 Spark 的必要条件，Spark支持任何实现了Hadoop 接口的存储系统。Spark 支持的Hadoop 输入格式包括文本文件、SequenceFile、Avro、Parquet等。





















