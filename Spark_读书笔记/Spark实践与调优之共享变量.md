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

#### 6.累加器
累加器是另一种类型的共享变量，可以用它从worker节点**聚合数值**，然后**返回给driver**。假设我们有一个数据集，我们想要计算达到一定条件的数目一共
有多少：
```
   val myRdd = sc.parallelize(Array(1, 2, 3, 4, 5, 6, 7))
   val evenNumbersCount = 0
   val unevenNumbersCount = 0
   myRdd.foreach(element => {
       if (element % 2 == 0) evenNumbersCount += 1
       else unevenNumbersCount += 1
   })
```
* 在上面的例子中，evenNumbersCount及unevenNumbersCount将在闭包内被序列化，发送给executor的各个任务中。这意味着每个任务会计算它们管理的分区
所对应的奇数与偶数计数器，但是对所有任务的总计数并不会累加到一起。
* 通过累加器来解决这类问题，为我们提供了一种机制，能安全地更新所有executor之间共享的变量。它们的值在每个任务内并行计算，然后在driver端相加。这是
为什么在executor上的操作必须具备关联性(associative)的原因。
* 为了利用累加器，需要将前面的示例改成下面的样子：
```
   val myRdd = sc.parallelize(Array(1, 2, 3, 4, 5, 6, 7))
   val evenNumbersCount = sc.accumulator(0, "Even numbers")
   val unevenNumbersCount = sc.accumulator(0, "Uneven numbers")
   myRdd.foreach(element => {
       if (element % 2 == 0) evenNumbersCount += 1
       else unevenNumbersCount += 1
   })
   println(s" Even numbers ${ evenNumbersCount.value }")
   println(s" Uneven numbers ${ unevenNumbersCount.value }")
```
当我们创建累加器时，会提供初始值，还可以给它起一个名字。运行在集群上的任务能更新这个值，但是不能读取。只有driver能读取累加器的值(evenNumbersCount.value)。

#### 7.实现自定义的累加器
* 如果你想自定义累加器的行为，可以自己实现一个累加器。只需要扩展**AccumulatorParam类**A并实现两个方法：zero方法，为累加的类型提供“零值”；
addInPlace方法，把两个值相加。
* 你也能实现一个输入是某种类型但结果是其他类型的累加器。对于此类累加器，你需要实现更通用的**Accumulable接口**A来定制累加器行为。
* 累加器在排查问题时非常有用。比如，统计成功与失败的操作的次数，或者统计某个操作被执行过多少次，与某个业务场景相关的问题出现过多少次。

#### 8.使用累加器的注意事项
* 关于累加器需要知道的一点是，应当**仅在action**而**不是transformation**操作中计算它们。这是因为Spark容错机制的缘故。Spark会因任务失败或太慢
而自动重新执行任务。如果执行一个转换时一个节点宕机，此任务将在另一台机器上启动。当任务在节点上运行太长时间时，也会发生同样的事情。如果一些分区数据
从缓存中被挤出来，但是做计算时又要用到它们，就会基于RDD**谱系**(lineage)重新计算出这些分区。因此，这里需要强调的是特定的函数可能会在数据集的同一
分区上被执行多次，这取决于集群上是否发生了最终导致不可靠累加器值的一些事件。
* 一旦理解这种行为，就知道如果希望不论集群上发生什么都能得到可靠的计数值，需要在action操作而不是transformation操作中更新累加器。对于action而言，
Spark确保在累加器中仅发生一次任务更新。当进行转换时则不能有此保证，因为累加器在转换中可能更新多次。








