#### 1.Spark执行模式
* 当运行一个Spark应用时，driver进程会随着集群worker节点上的一系列executor进程一起启动。driver负责运行用户的应用程序，当有action被触发时driver负责
管理所需执行的所有工作。另一方面，executor进程以任务(task)的形式执行实际的工作以及保存结果。但是，这些任务是如何分配给executor的呢？
* 对于Spark应用内部触发的**每个action**，DAG调度器都会创建**一个执行计划**来完成它。执行计划就是将尽可能多的窄依赖(narrow dependency)转换
(transformation)装配到各步骤(stage)中。RDD间的**窄依赖**是指父RDD的每一个分区最多能被一个子RDD的分区使用。当有一些**宽依赖需要做shuffle操作**时，
stage就受限制了。当多个子RDD的分区使用同一个父RDD的分区时，RDD间就会产生**宽依赖**。

#### 2.分区
分区其实就是RDD中的数据被切分后形成的片段。当DAG调度器将job转换为stage时，每个分区将被处理成一个task，每个task需要一个CPU核来执行。这意味着Spark
应用的**并行度取决于RDD的分区数**。因此，不难理解对Spark应用性能进行**调优**时，**RDD的分区数**可能是需要考虑的**最重要**的事情。

#### 3.RDD：一个只读且分区的记录集。



