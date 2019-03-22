#### 1.在YARN上运行Spark的优点
* 使用YARN集群管理器，使得维护运行在**单个集群上**的**不同类型应用程序**更方便。它既能支持多种多样的场景，也能确保应用程序间公平地共享资源。
* 在YARN上运行Spark的另一个好处是，它不需要额外地配置或部署Spark二进制文件。当提交一个YARN应用时，只需提交一个编译过的二进制文件，YARN就会
**处理该文件在集群内的分发**。这意味着，对于更大的集群，系统管理员或dev-ops(开发运维)工程师仅需要配置一次YARN集群，而无须对每一台机器重新配
置就能运行各种应用程序。由于不再需要配置单个worker节点，部署方式也变得更加简单。

#### 2.YARN架构简介
* 在YARN里，有两个主要实体：**ResourceManager**(RM)和**ApplicationMaster**(AM)。还有一个组件，**NodeManager**(NM)，它是ResourceManager的
从节点(slave)。一般来说，ResourceManager控制一个集群里可用的全局资源，这个资源池有某些配置和约束。
* 集群中的每一个应用程序都有一个与框架对应的ApplicationMaster，例如MapReduce及Spark就有自己的ApplicationMaster版本。ApplicationMaster从
ResourceManager中请求资源，ResourceManager会根据资源的可用性进行分配。ApplicationMaster与NodeManager协调来执行任务。

#### 3.YARN的资源管理——容器
YARN的资源分配方式定义了一个重要的概念“容器”(container)。容器就是一个包含内存、CPU、网络、硬盘的资源集，这些有限的资源被离散地分配给运行在容器中
的进程。ResourceManager有一个叫作Scheduler(调度器)的实用程序，负责把容器分配给各个进程。Scheduler的职责包括分配资源以及在资源可用时进行声明。

#### 4.YARN的资源请求工作流程
典型的工作流如下：ApplicationMaster从ResourceManager中请求一个资源容器，NodeManager在每个节点上**启动并监控**一个或多个容器。一个应用请求的
资源可能超过单个容器的可用资源，然后ApplicationMaster将会请求多个容器去满足这个需求。而YARN不会与提出请求的应用协商，因此当请求不能被满足时，YARN
**将会阻塞**，提出请求的实体将被迫等待，直到请求的资源可用。

#### 5.YARN中的worker
在YARN上运行Spark应用时，Spark应用将**透明**地接入YARN生态系统。在YARN里面**没有worker**的概念，直至Spark出现才有。YARN知道集群里的节点，但是现在
Spark看到的是许多可用的容器，每一个容器都有一些资源。YARN ApplicationMaster处理**容器之间的通信**，承担Spark Standalone模式中Spark Master的角色。
因此，它也处理**worker节点间的通信**。

#### 6.Spark中的executor
Spark的每个executor在它自己的**YARN容器里运行**，每个节点上的每个容器里存放ResourceManager分配的资源。与Spark Standalone一样，在每个YARN容器里将
运行**一个或多个**任务。

#### 7.YARN中每个节点的总内存及CPU配置参数
* yarn.nodemanager.resource.memory-mb:决定每个节点YARN容器的总计可用内存。
* yarn.nodemanager.resource.cpu-vcores:决定YARN应用可用的CPU内核数。

#### 8.YARN的动态资源配置
* 在Spark1.2及以上版本中，可以根据需要动态地为executor分配及回收资源。因为**YARN是动态管理器**，而不像Spark Standalone一样是静态的，这种方式让我们
能更加高效地使用集群资源。
* 启动动态资源分配时，Spark使用一组启发式算法来确定executor是否过多或是需要更多的executor。第一个度量方法为**是否有等着被调度的积压任务**。Spark
将会定期检查挂起的任务数，如果一直有在等待的任务，就分配呈指数增加的executor。应用将首先增加1个executor，然后是2个、4个、8个，在大多数情形下，增加
几个executor就足够了。
* 当动态分配启用时，无须在手动设置 -num-executor 属性，因为系统会根据调度器的需求，增加可用的executor来执行任务。

#### 9.在YARN上运行Spark的两种模式
在YARN上运行Spark有两种方式。第一种情况，driver运行在启动Spark应用的机器上，无论这个Spark应用是spark-shell还是spark-submit提交的二进制码。在这种
情形下，YARN应用程序master只负责从YARN中请求资源，这就是**yarn-client模式**。第二种情况，driver在YARN容器里运行，客户端可以从集群中断开，或者用于
其他job，这叫做**yarn-cluster模式**。












