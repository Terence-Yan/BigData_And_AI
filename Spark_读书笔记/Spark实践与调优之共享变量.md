#### 1.Spark中的共享变量
Spark中有两种类型的共享变量：**广播变量**(broadcast variable) 与**累加器**(accumulator)。它们用于两种常见的情形：前者用于在集群中**分发**任务共享的
大数据，而后者用于从worker中将信息**聚合**回driver。

#### 2.Spark中的闭包(closure)
闭包是指一个**有外部引用**的函数：它依赖于在**外部声明**而**作用域却在其内部**的变量。在下面的例子中，定义了一个计算幂的闭包，其中power是定义在声明的
函数之外的变量：
```
   val power = 2
   val raiseToPower = (number : Double) => pow(number, power)
```
用分布式方式在集群中执行闭包时会发生什么？首先Spark会计算闭包，将它**序列化**并**拷贝到每个任务里**。例如：
```
   import scala.math._
   val myRdd = sc.parallelize(Array(1, 2, 3, 4, 5, 6))
   val power = 2
   myRdd.foreach(pow(_, power))
```
在本例中，声明在函数之外的power变量被传给foreach方法。这个值将随着任务被拷贝到序列化闭包里。

#### 3.广播变量
![图1](https://github.com/Terence-Yan/note_pictures/blob/master/spark_pictures/broadVariable2.png "")
![图2](https://github.com/Terence-Yan/note_pictures/blob/master/spark_pictures/broadVariable3.png "")
![图3](https://github.com/Terence-Yan/note_pictures/blob/master/spark_pictures/broadVariable.png "")
* 为了实现一致性与容错性，广播变量是只读的。如果它们不是只读的，那么当一个节点上的变量改变时，该变量在其他机器上的所有副本也必须改变。
为了避免这些问题，Spark仅支持只读变量。
* 当请求广播一个变量时，Spark driver将数据写在**本地文件夹**内，并且会把它写到由块ID标识的**块管理器**(block manager)内。当提交任务时，
使用了这个广播变量的转换函数将与该广播变量的原信息一起被序列化，这个序列化信息将会**扩散到集群上**。
* 当这个任务在executor上开始时，它将被反序列化，还会尝试根据元信息读取该对象。首先，它会尝试从本地块管理器中读取此变量，看看缓存中是否已经有该对象
的副本。如果在本地executor上未发现副本，则从driver中拉取数据。

#### 4.广播变量的广播方式
* 数据的拉取方式很重要，因为它会影响性能。针对数据的广播方式，Spark提供了两种实现：**HTTP广播**与**Torrent广播**。
* 使用HTTP广播时，数据从driver端运行的HTTP服务器中拉取。当driver把数据发送到集群的所有节点上时，可能会存在**网络瓶颈**。Torrent广播**避免**了这种
瓶颈。其背后的主要思想是，把数据分割成较小的数据块，一旦有executor拉取了一部分数据块，它就会成为一个种子器(seeder)。

#### 5.广播变量广播方式的设置
你可以通过**spark.broadcast.factory**属性来设置使用哪种类型的广播。设置**spark.broadcast.blockSize**，可以把Torrent方式使用的块调整为想要
的大小。选择这个值的时候需要小心，因为如果值太大，将减少并行度；如果值太小，会给块管理器带来压力。
```
   val configuration = new SparkConf()
   configuration.set("spark.broadcast.factory","org.apache.spark.broadcast.TorrentBroadcastFactory")
   configuration.set("spark.broadcast.blockSize","4m")
```
总结一下，当有多个任务需要访问同一变量时，应当考虑广播变量。这将提升应用的性能。




