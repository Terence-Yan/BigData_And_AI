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







